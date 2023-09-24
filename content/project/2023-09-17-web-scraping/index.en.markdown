---
title: Web Scraping
author: twg
date: '2023-09-17'
slug: web-scraping
categories:
  - rvest
  - scraping
tags:
  - rvest
  - scraping
subtitle: ''
summary: 'Code for scraping various sites'
lastmod: '2023-09-17T14:35:55-07:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---




{{% toc %}}

## Scraping Instagram

This is a function to scrape the user data and the last 12 posts from that user. Set this up as a recurring cron job and it could scrape multiple users and store the data. R code is adapted from [this post](https://scrapfly.io/blog/how-to-scrape-instagram/), which was written in python. We're also using the ```rjsoncons``` package, which allows for [JMESpath](https://jmespath.org/) subsetting of JSON data. Since we're adding headers to the GET request, we'll use the ```httr``` package, rather than ```rvest```. 


```r
library(httr)
library(jsonlite)

# function to parse data and put into list 
parse_user <- function(data) {
  # get data on the account
  account_data <- rjsoncons::jmespath(data, "data.user.{
        name: full_name,
        username: username,
        id: id,
        category: category_name,
        business_category: business_category_name,
        phone: business_phone_number,
        email: business_email,
        bio: biography,
        bio_links: bio_links[].url,
        homepage: external_url,        
        followers: edge_followed_by.count,
        follows: edge_follow.count,
        facebook_id: fbid,
        is_private: is_private,
        is_verified: is_verified,
        profile_image: profile_pic_url_hd,
        video_count: edge_felix_video_timeline.count,
        image_count: edge_owner_to_timeline_media.count,
        saved_count: edge_saved_media.count,
        related_profiles: edge_related_profiles.edges[].node.username
                                   
}"
  ) |> 
    fromJSON()
  
  # get post data
  post_data <- rjsoncons::jmespath(data, "data.user.edge_owner_to_timeline_media.edges[].node.{
         post_date: taken_at_timestamp, 
         typename: __typename,
         type: is_video,
         id: id,
         shortcode: shortcode,
         thumbnail: thumbnail_src,
         video_url: video_url,
         caption: edge_media_to_caption.edges[].node.text,
         video_viewcount: video_view_count,
         comment_count: edge_media_to_comment.count,
         like_count: edge_liked_by.count,
         location: location.name,
         music_artist: clips_music_attribution_info.artist_name,
         song_name: clips_music_attribution_info.song_name
}"
  ) |> 
    fromJSON() |> 
    mutate(post_date = as.POSIXct(post_date, origin = "1970-01-01"))
  
  list(account_data = account_data, post_data = post_data)
}

scrape_user <- function(username) {
  INSTAGRAM_APP_ID <- "936619743392459" # this is the public app id for instagram.com
  url <- paste0("https://i.instagram.com/api/v1/users/web_profile_info/?username=", username)
  response <- GET(
    url = url,
    add_headers(`x-ig-app-id` = INSTAGRAM_APP_ID)
  )
  data <- content(response, "text")
  parse_user(data)
}

out <- scrape_user('bmw')
```

Two lists are saved. The first is the account data.  


```r
out$account_data[c(1,2,3,5,8,11:12,17,18)] |> 
  cbind() |> 
  data.frame() |> 
  set_names(nm = "") |> 
  knitr::kable()
```


|                  |                                                                                                         |
|:-----------------|:--------------------------------------------------------------------------------------------------------|
|name              |BMW                                                                                                      |
|username          |bmw                                                                                                      |
|id                |43109246                                                                                                 |
|business_category |Auto Dealers                                                                                             |
|bio               |The official #BMW account, home of Sheer Driving Pleasure.
Use #BMWrepost for the chance to get featured. |
|followers         |37361042                                                                                                 |
|follows           |76                                                                                                       |
|video_count       |49                                                                                                       |
|image_count       |10594                                                                                                    |

The second is the post data that we've pulled. 


```r
out$post_data |> 
  head(3) |> 
  knitr::kable()
```


|post_date           |typename   |type |id                  |shortcode   |caption                                                                                                                                                              | video_viewcount| comment_count| like_count|location         |music_artist |song_name      |
|:-------------------|:----------|:----|:-------------------|:-----------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------:|-------------:|----------:|:----------------|:------------|:--------------|
|2023-09-05 09:12:49 |GraphVideo |TRUE |3185240436572707198 |Cw0P00KIFl- |Taking over our hometown in our home colours 💙🤍 #NeueKlasse #IAA23 #THEVisionNeueKlasse #TheNeueNew                                                                |          678386|           813|     164169|Max-Joseph-Platz |ottocarclub  |Original audio |
|2023-09-07 01:00:45 |GraphVideo |TRUE |3186442829185025149 |Cw4hN7iqgR9 |Every decision is an act of creation, shaping the path of our journey. THE NEUE NEW. The BMW Vision Neue Klasse. #NeueKlasse #IAA23 #THEVisionNeueKlasse #TheNeueNew |          325513|           256|      40401|NA               |bmw          |Original audio |
|2023-09-08 10:34:30 |GraphVideo |TRUE |3187455988330466509 |Cw8HlVhoQzN |BMW faces its toughest critic yet @liamcarps1 🫣 Shop Liam’s look via the link in bio. #NeueKlasse #IAA23 #THEVisionNeueKlasse #TheNeueNew #BMW                       |         1540983|           959|     121633|NA               |bmw          |Original audio |

## Scraping hidden data

In this [example](https://taylorgrant.netlify.app/post/web-scraping/#relying-on-data-loaded-by-javascript) I show how when there is an identifiable script, that we can scrape the javascript from a dynamically loaded site. But what to do when this hidden data is loaded via a generic script? When this occurs, we have to do some parsing. It can be a pain, but being able to do this correctly opens a whole new world of data availability. 

As an example, we're going to use the Home Depot state by state [store location]("https://www.homedepot.com/l/AL") page. In this case, for the state of Alabama. This data is actually easily scrapable using `rvest` and css selection, but pulling the JSON is far more convenient, and often yields additional information. 

After loading the page, we go to devtools and search for part of the first address. As can be seen, the script isn't identified, but we see the `window.__APOLLO_STATE__:`. We're going to use this little bit to find the JSON data. 

![img](https://res.cloudinary.com/dn83gtg0l/image/upload/v1695522449/hd_locations.png)
The first thing we're going to do is use the `httr` package to request the page and then parse it. 


```r
# Define the URL
url <- "https://www.homedepot.com/l/AL"

# Send a GET request to the URL
response <- GET(url)

# Check if the request was successful
(http_status(response)$category == "Success")
  
# Parse the HTML content from the response
html_content <- content(response, as = "text", encoding = "UTF-8")
```

Now that we have our content in text format, we have to find that script identifier. The `start_pos` variable is finding the location after the `window.__APOLLO_STATE__=` text is found. The `end_pos_relative` is looking for the location of the end of the JSON. In this case, becasue the data is nested, we're looking for 3 end brackets. This will change depending on the structure of the data. 


```r
# Find the position of the substring "window.__APOLLO_STATE__="
start_pos <- str_locate(html_content, "window.__APOLLO_STATE__=")[2] + 1
    
# If the start position is found, proceed to find the end position and extract the JSON string
end_substring <- str_sub(html_content, start = start_pos)
end_pos_relative <- str_locate(end_substring, "\\}\\}\\}")
```

Now we adjust our end position relative to where our JSON data started, extract the data, and parse it with the `jsonlite::fromJSON()` function.   


```r
# Assuming we found the end_pos, adjust end_pos relative to start_pos 
end_pos <- end_pos_relative[2] + start_pos - 1
        
# Extract the JSON string using substring
json_str <- substr(html_content, start_pos, end_pos)
        
# Parse the JSON string to a list
json_data <- fromJSON(json_str, flatten = TRUE)
```

Because our data is nested, it's going to parse as a list. Calling the data, we can see that our data can be found in the following location. 


```r
stores_df <- json_data$ROOT_QUERY$`storeDirectoryByState({"state":"AL"})`$storesInfo |> 
  select(storeName, address.street, address.city, address.state, 
         address.postalCode, address.county, url, phone)

head(stores_df)
```


```
##      storeName        address.street address.city address.state address.postalCode address.county
## 1     W Mobile  755 Schillinger Rd S       Mobile            AL              36695         Mobile
## 2        Foley    2899 S Mckenzie St        Foley            AL              36535        Baldwin
## 3 W Huntsville  4045 Lawson Ridge Dr      Madison            AL              35757        Madison
## 4 N Huntsville 1035 Memorial Pkwy Nw   Huntsville            AL              35801        Madison
## 5       Pelham      3191 Pelham Pkwy       Pelham            AL              35124         Shelby
## 6   Prattville  2710 Legends Parkway   Prattville            AL              36066         Elmore
##                                                                url         phone
## 1         https://www.homedepot.com/l/W-Mobile/AL/Mobile/36695/801 (251)634-0351
## 2             https://www.homedepot.com/l/Foley/AL/Foley/36535/802 (251)955-2401
## 3    https://www.homedepot.com/l/W-Huntsville/AL/Madison/35757/803 (256)837-6658
## 4 https://www.homedepot.com/l/N-Huntsville/AL/Huntsville/35801/804 (256)536-2216
## 5           https://www.homedepot.com/l/Pelham/AL/Pelham/35124/805 (205)685-1837
## 6   https://www.homedepot.com/l/Prattville/AL/Prattville/36066/806 (334)285-1693
```

