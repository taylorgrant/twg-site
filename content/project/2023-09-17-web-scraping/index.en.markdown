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
  head(2) |> 
  knitr::kable()
```


|post_date           |typename   |type |id                  |shortcode   |thumbnail                                                                                                                                                                                                                                                                                                                                     |video_url                                                                                                                                                                                                                                                                                           |caption                                                                                                                                                            | video_viewcount| comment_count| like_count|location         |music_artist |song_name      |
|:-------------------|:----------|:----|:-------------------|:-----------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------:|-------------:|----------:|:----------------|:------------|:--------------|
|2023-09-05 09:12:49 |GraphVideo |TRUE |3185240436572707198 |Cw0P00KIFl- |https://scontent-sjc3-1.cdninstagram.com/v/t51.2885-15/374559247_1122467862075371_4052290103429490205_n.jpg?stp=c0.280.720.720a_dst-jpg_e15_s640x640&_nc_ht=scontent-sjc3-1.cdninstagram.com&_nc_cat=111&_nc_ohc=SAj5yRb9JSAAX9Fdzrb&edm=AOQ1c0wBAAAA&ccb=7-5&oh=00_AfCo7-VRfJ-CKbSIGcUstQjoSRyzxREgw6wxsdo9NWPk1Q&oe=65093ECD&_nc_sid=8b3546 |https://scontent-sjc3-1.cdninstagram.com/v/t66.30100-16/48992601_309418074969392_3129943915792075644_n.mp4?_nc_ht=scontent-sjc3-1.cdninstagram.com&_nc_cat=108&_nc_ohc=v4PrmdeM1f4AX9dpHa-&edm=AOQ1c0wBAAAA&ccb=7-5&oh=00_AfCf5gEg39w22CMCTRUGIM3_cCXSQWp8yfQa2iemzvGdsg&oe=650905F2&_nc_sid=8b3546 |Taking over our hometown in our home colours 💙🤍 #NeueKlasse #IAA23 #THEVisionNeueKlasse #TheNeueNew                                                              |          678386|           813|     164169|Max-Joseph-Platz |ottocarclub  |Original audio |
|2023-09-07 01:00:45 |GraphVideo |TRUE |3186442829185025149 |Cw4hN7iqgR9 |https://scontent-sjc3-1.cdninstagram.com/v/t51.2885-15/375582045_601344018615068_3770427890167221787_n.jpg?stp=c0.280.720.720a_dst-jpg_e15_s640x640&_nc_ht=scontent-sjc3-1.cdninstagram.com&_nc_cat=108&_nc_ohc=fEnLa6qpylwAX-_q0Rz&edm=AOQ1c0wBAAAA&ccb=7-5&oh=00_AfDLC9hqryprBFIeU6JDmRAalGTih3uG2SAmRK2K8HRG1w&oe=65092A98&_nc_sid=8b3546  |https://scontent-sjc3-1.cdninstagram.com/v/t66.30100-16/10000000_732958088846277_4663615406285571373_n.mp4?_nc_ht=scontent-sjc3-1.cdninstagram.com&_nc_cat=109&_nc_ohc=swLxGCOuLCUAX9Jy4yY&edm=AOQ1c0wBAAAA&ccb=7-5&oh=00_AfCZYrhhYGqiL45HHMJRjphAu7JmkuzdkoZFKDwilPMHrw&oe=6509556B&_nc_sid=8b3546 |Every decision is an act of creation, shaping the path of our journey.
THE NEUE NEW. The BMW Vision Neue Klasse.
#NeueKlasse #IAA23 #THEVisionNeueKlasse #TheNeueNew |          325513|           256|      40401|NA               |bmw          |Original audio |
