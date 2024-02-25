---
title: General Tips for R
author: twg
date: '2024-02-24'
categories:
  - R
tags:
  - R
slug: general-tips-for-r
summary: Tips and Tricks for R and RStudio
lastmod: '2021-12-05T22:08:17-08:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
---




## Keyboard Shortcuts for Mac

Pipe operator (%>%): `Cmd` + `Shift` + `m` <br>
Assignment operator (<-): `Option` + `-` <br>
Rename all instances of variable: `Option`+`Shift`+`Cmd`+`m` (Code > Rename in Scope)

## Setting NAs to Zero 

Assume a data frame that has NAs and you want to set any NA to zero, it is straightforward to do so. 

```r
df = tibble(
  x = c(1,NA,3,NA,5), 
  y = c(1:5), 
  z = x ^ 2 + y) 

df[is.na(df)] <- 0

head(df)
```

```
## # A tibble: 5 × 3
##       x     y     z
##   <dbl> <int> <dbl>
## 1     1     1     2
## 2     0     2     0
## 3     3     3    12
## 4     0     4     0
## 5     5     5    30
```

We can also selectively choose the column to change by referencing it specifically 

```r
df = tibble(
  x = c(1,NA,3,NA,5), 
  y = c(1,NA,3,NA,5),
  z = 1:5) 

df$x[is.na(df$x)] <- 0

head(df)
```

```
## # A tibble: 5 × 3
##       x     y     z
##   <dbl> <dbl> <int>
## 1     1     1     1
## 2     0    NA     2
## 3     3     3     3
## 4     0    NA     4
## 5     5     5     5
```

Or, within a dplyr pipe, we can use this, and this will work across the entire data frame 

```r
df = tibble(
  x = c(1,NA,3,NA,5), 
  y = c(1,NA,3,NA,5),
  z = 1:5)

df %>% mutate_all(~replace(., is.na(.), 0))
```

```
## # A tibble: 5 × 3
##       x     y     z
##   <dbl> <dbl> <dbl>
## 1     1     1     1
## 2     0     0     2
## 3     3     3     3
## 4     0     0     4
## 5     5     5     5
```

## Getting rid of NULL values within dataframe

Occasionally, a datset will contain cells that, rather than simply being left empty, have been populated with 'NULL'. This can be a pain, especially when trying to determine how much data is missing. 

This is a quick function to overwrite all 'NULL' values and then replace with NA. 


```r
#function 
rm_null <- function(x){
  str_replace_all(x, "NULL", "")
}

df = tibble(
  x = c(1,NA,3,NA,5), 
  y = c(1,"NULL",3,NA,5),
  z = 1:5)

# apply to data
df_clean <- df %>% mutate_all(rm_null)
df_clean
```

```
## # A tibble: 5 × 3
##   x     y     z    
##   <chr> <chr> <chr>
## 1 1     "1"   1    
## 2 <NA>  ""    2    
## 3 3     "3"   3    
## 4 <NA>  <NA>  4    
## 5 5     "5"   5
```

```r
# replace with NA
df_clean[df_clean == ""] <- NA
```

## Finding NULL values in a list 


```r
# are there any 
any(sapply(list(1, NULL, 3), is.null))
```

```
## [1] TRUE
```

```r
# where is it 
which(sapply(list(1, NULL, 3), is.null))
```

```
## [1] 2
```


## Plotting colors with hexcode labels 

Here is a function that provides an easy way of seeing a color palette and retrieving the hexcodes. 


```r
show_pal <- function(pal, labels = TRUE, label_size = 1, label_color = "#000000") {
  library(gplots)
  pal <- gplots::col2hex(pal)
  n <- length(pal)
  n_col <- ceiling(sqrt(n))
  n_row <- ceiling(n / n_col)
  m <- matrix(0, n_col, n_row)
  m[n + 1] <- 1
  m <- t(m)
  pal <- c(pal, rep(NA, n_row * n_col - length(pal)))
  pal <- matrix(pal, ncol = n_col, byrow = TRUE)
  plot(c(0, dim(pal)[2]), c(0, -dim(pal)[1]), type = "n", xlab = "", ylab = "",
       axes = FALSE)
  rect(col(pal) - 1, -row(pal) + 1, col(pal), -row(pal), col = pal, lwd = 3, border = "white")
  if (labels) text(col(pal) - 0.5, -row(pal) + 0.5, pal, cex = label_size, col = label_color)
}

# load a palette 
pal <- RColorBrewer::brewer.pal(n = 9, name = 'PuBu')
show_pal(pal)
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/pal-1.png" width="1104" style="display: block; margin: auto;" />

## Finding all functions in a package

This is especially useful when using a dev version of a package. Often, there are github files, but it's sometimes easier to just use `asNamespace` to see functions. 

For example, let's look at the functions available within the spotifyR package. 


```r
library(spotifyr)
fns <- unclass(lsf.str(envir = asNamespace("spotifyr"), all = T))

fns
```

```
##  [1] "add_tracks_to_playlist"          "change_playlist_details"        
##  [3] "check_me_following"              "check_users_following"          
##  [5] "create_playlist"                 "dedupe_album_names"             
##  [7] "follow_artists_or_users"         "follow_playlist"                
##  [9] "get_album"                       "get_album_tracks"               
## [11] "get_albums"                      "get_artist"                     
## [13] "get_artist_albums"               "get_artist_audio_features"      
## [15] "get_artist_top_tracks"           "get_artists"                    
## [17] "get_categories"                  "get_category"                   
## [19] "get_category_playlists"          "get_featured_playlists"         
## [21] "get_genre_artists"               "get_label_artists"              
## [23] "get_my_current_playback"         "get_my_currently_playing"       
## [25] "get_my_devices"                  "get_my_followed_artists"        
## [27] "get_my_playlists"                "get_my_profile"                 
## [29] "get_my_recently_played"          "get_my_saved_albums"            
## [31] "get_my_saved_tracks"             "get_my_top_artists_or_tracks"   
## [33] "get_new_releases"                "get_playlist"                   
## [35] "get_playlist_audio_features"     "get_playlist_cover_image"       
## [37] "get_playlist_tracks"             "get_recommendations"            
## [39] "get_recommendations_all"         "get_related_artists"            
## [41] "get_show"                        "get_show_episodes"              
## [43] "get_shows"                       "get_spotify_access_token"       
## [45] "get_spotify_authorization_code"  "get_track"                      
## [47] "get_track_audio_analysis"        "get_track_audio_features"       
## [49] "get_tracks"                      "get_user_audio_features"        
## [51] "get_user_playlists"              "get_user_profile"               
## [53] "is_uri"                          "pause_my_playback"              
## [55] "print.playlist"                  "query_playlist"                 
## [57] "remove_tracks_from_playlist"     "scopes"                         
## [59] "search_spotify"                  "seek_to_position"               
## [61] "set_my_repeat_mode"              "set_my_volume"                  
## [63] "skip_my_playback"                "skip_my_playback_previous"      
## [65] "start_my_playback"               "tidy"                           
## [67] "tidy.playlist"                   "toggle_my_shuffle"              
## [69] "transfer_my_playback"            "unfollow_playlist"              
## [71] "validate_country"                "validate_include_meta_info"     
## [73] "validate_limit"                  "validate_locale"                
## [75] "validate_market"                 "validate_offset"                
## [77] "validate_parameters"             "validate_position_ms"           
## [79] "validate_state"                  "validate_time_range"            
## [81] "validate_type_artist_or_user"    "validate_type_artists_or_tracks"
## [83] "validate_volume_percent"         "verify_result"                  
## attr(,"envir")
## <environment: namespace:spotifyr>
## attr(,"mode")
## [1] "function"
```

## Getting the names of datasets within a package

If in RStudio, you can simply use `data(package = "package_name")` and a new window will open up with a list. 

Or by using the `data()` function we can find datasets and information about each. 


```r
d <- data(package = "dplyr")
# names can be pulled 
d$results[, 'Item']
```

```
## [1] "band_instruments"  "band_instruments2" "band_members"     
## [4] "starwars"          "storms"
```

While it's probably not necessary to do this, it's also possible to pull the dimensions for the promised data. 


```r
d <- data(package = "dplyr")
# assign 
nm <- d$results[, 'Item']
# call the promised data
data(list = nm, package = "dplyr")
# get dimensions
lapply(mget(nm), dim)
```

```
## $band_instruments
## [1] 3 2
## 
## $band_instruments2
## [1] 3 2
## 
## $band_members
## [1] 3 2
## 
## $starwars
## [1] 87 14
## 
## $storms
## [1] 19537    13
```

The `vcdExtra` package actually does all of the above very nicely. 


```r
vcdExtra::datasets("dplyr")
```

```
##                Item      class      dim               Title
## 1  band_instruments data.frame      3x2     Band membership
## 2 band_instruments2 data.frame      3x2     Band membership
## 3      band_members data.frame      3x2     Band membership
## 4          starwars data.frame    87x14 Starwars characters
## 5            storms data.frame 19537x13   Storm tracks data
```

## Loading a dataset from a package without loading package itself

I want a dataset from a package, but loading that package sometimes interferes with functions from another package. Load only the dataset with 


```r
data(Howell1, package = "rethinking")
```

## Searching for functions within a package

The `utils` package comes to the rescue. It searches all packages that have been installed for any function you provide. This even seems to find functions that are directly loaded by the package, which is really helpful 


```r
utils::getAnywhere("FUNCTION_NAME")

# if more than one function by the name is found, it returns multiple; then just subset by location
utils::getAnywhere("FUNCTION_NAME")[2]
```


## Loading all functions within a folder

The `R.utils` package also has a command for this, but this is a simple way to source all of your functions at once. 


```r
# set directory location, or use here() rather than glue
# load functions
file.sources = list.files(path = glue(dir, "functions"), pattern="*.R")
sapply(glue("{dir}functions/{file.sources}"), source, .GlobalEnv)
```

## Editing a package function

If there is a function that is failing for some reason, it can be edited with the `trace()` function.


```r
trace("[function_name]", edit = TRUE)
```
