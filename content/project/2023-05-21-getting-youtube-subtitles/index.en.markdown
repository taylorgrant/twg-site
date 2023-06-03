---
title: Getting YouTube subtitles
author: twg
date: '2023-05-21'
slug: getting-youtube-subtitles
categories:
  - scraping
  - youtube
tags:
  - scraping
  - youtube
subtitle: ''
summary: 'Writing a script to get YouTube subtitles and all other data about a video'
authors: []
lastmod: '2023-05-21T16:52:35-07:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---



{{% toc %}}

YouTube subtitles are a trove of text data, but I've struggled with a good way of extracting and cleaning them. Now, thanks to a few utilities, I've built a nice R function that pulls and cleans subtitles as well as all comments and other data for each video. 

## Setup

The first thing we need to do is install a few utilities that will do the heavy lifting. First is [yt-dlp](https://github.com/yt-dlp/yt-dlp), which is a fork of [youtube-dl](https://github.com/ytdl-org/youtube-dl). Installation instructions are on the github page (use the `pip` functionality). It's very well documented and very powerful. We're using it to download the subtitles and video data, but this can actually download the entire video if you want.  

Next we need to download [ffmpeg](https://ffmpeg.org/). There are a number of ways of installing, but the easiest is probably just using Homebrew ("brew install ffmpeg"). 

Last, we need ripgrep to help to clean the text after download ("brew install ripgrep).

## Downloading the subtitles

These utilities function from the command line, but we can wrap our zsh commands in R's `system()` function to run in R. To get the subtitles, all we need is a YouTube url and the below. 


```r
system("yt-dlp --sub-lang en --skip-download --write-auto-sub --sub-format vtt --write-info-json --write-comments 'https://www.youtube.com/watch?v=Lz33OZf4Yx0'")
```

This command is going to download the subtitles in a .vtt format along with a json file that contains all the video data. And because we've included the command `--write-comments` it will also pull all the user comments for that video and include them in the json file. 

The subtitles and json are downloaded to the working directory. Both will be saved with the formal title of the video. We'll change that when everything gets put into a function.

If we read in the subtitles in .vtt format, we can see it's not in a clean format. 


```
##  [1] "WEBVTT"                                                                                                                                                                                                          
##  [2] "Kind: captions"                                                                                                                                                                                                  
##  [3] "Language: en"                                                                                                                                                                                                    
##  [4] ""                                                                                                                                                                                                                
##  [5] "00:00:00.179 --> 00:00:03.949 align:start position:0%"                                                                                                                                                           
##  [6] " "                                                                                                                                                                                                               
##  [7] "what<00:00:00.840><c> would</c><00:00:01.020><c> I</c><00:00:01.199><c> hope</c><00:00:01.380><c> my</c><00:00:01.920><c> musical</c><00:00:02.399><c> Legacy</c><00:00:03.120><c> is</c><00:00:03.659><c> I</c>"
##  [8] ""                                                                                                                                                                                                                
##  [9] "00:00:03.949 --> 00:00:03.959 align:start position:0%"                                                                                                                                                           
## [10] "what would I hope my musical Legacy is I"                                                                                                                                                                        
## [11] " "                                                                                                                                                                                                               
## [12] ""                                                                                                                                                                                                                
## [13] "00:00:03.959 --> 00:00:06.470 align:start position:0%"                                                                                                                                                           
## [14] "what would I hope my musical Legacy is I"                                                                                                                                                                        
## [15] "hope<00:00:04.200><c> that</c><00:00:04.500><c> the</c><00:00:04.740><c> people</c><00:00:05.040><c> that</c><00:00:05.339><c> I</c><00:00:05.460><c> love</c>"
```

## Cleaning our subtitles

We're going to use ffmpeg to convert these subtitles into a bit of a better format. Again, we're going to wrap our command line in our `system()` function to run it in R. As the code above is currently written, yt-dlp saves the data using the formal title of the video. So in the below code, the file name needs to be used. 


```r
system("ffmpeg -i '[FILENAME].en.vtt' [FILENAME].srt")
```

ffmpeg has converted our .vtt format into .srt format, but we can see that it's still not the most readable and a lot of work would need to be done if we were going to do any text analysis. 


```
##  [1] "1"                                       
##  [2] "00:00:00,179 --> 00:00:03,949"           
##  [3] ""                                        
##  [4] "what would I hope my musical Legacy is I"
##  [5] ""                                        
##  [6] "2"                                       
##  [7] "00:00:03,949 --> 00:00:03,959"           
##  [8] "what would I hope my musical Legacy is I"
##  [9] " "                                       
## [10] ""                                        
## [11] "3"                                       
## [12] "00:00:03,959 --> 00:00:06,470"           
## [13] "what would I hope my musical Legacy is I"
## [14] "hope that the people that I love"        
## [15] ""
```

So now we're going to use ripgrep to clean this and make it readable. 


```r
system("rg -v '^[[:digit:]]+$|^[[:digit:]]{2}:|^$', yt_data/ [FILENAME].srt | tr -d '\r' | rg -N '\\S' | uniq > [CLEAN_FILENAME].txt")
```

And now we have a clean text ready to read or use for further NLP analysis. 


```
##  [1] "what would I hope my musical Legacy is I"
##  [2] "hope that the people that I love"        
##  [3] "remember that I wasn't so much of a dick"
##  [4] "for the most part"                       
##  [5] "[Music]"                                 
##  [6] "Ants Marching was pretty early on maybe" 
##  [7] "the most recognizable of our earlier"    
##  [8] "Tunes I met a lot of the guys when I was"
##  [9] "bartending so I met Carter I think"      
## [10] "probably at the bar where I was working" 
## [11] "Miller's probably a became friendly with"
## [12] "Leroy there he played there quite a bit" 
## [13] "Straight Ahead Jazz stuff and I think I" 
## [14] "knew Stefan from that period too I wrote"
## [15] "four songs and we all said we decided"
```

## Wrapping it in a function

We have everything we need to put this into a usable function. By default, yt-dlp uses the formal title of the video for the filename, but we can change that with the `--output` command where we're going to pass through our own title as an argument.   


```r
youtube_scrape <- function(link, title, comments=TRUE) {
  if (comments == TRUE) {
    x <- glue::glue("yt-dlp --sub-lang en --skip-download --write-auto-sub --sub-format vtt --write-info-json --write-comments --output 'yt_data/{title}' '{link}'")
  } else {
    x <- glue::glue("yt-dlp --sub-lang en --skip-download --write-auto-sub --sub-format vtt --write-info-json --output 'yt_data/{title}' '{link}'")
  }
  system(x)
  y <- glue::glue("ffmpeg -i 'yt_data/{title}.en.vtt' yt_data/{title}.srt")
  system(y)
  # glue messes up this command for some reason; using paste instead #
  z <- paste0("rg -v '^[[:digit:]]+$|^[[:digit:]]{2}:|^$', yt_data/",title, ".srt | tr -d '\r' | rg -N '\\S' | uniq > yt_data/clean_",title, ".txt")
  system(z)
}
```

## YouTube data

The json file is also downloaded, and we can easily access that. Using `jsonlite` the returned data is a named list. 


```r
out <- jsonlite::fromJSON(glue::glue("yt_data/{title}.info.json"))
names(out)
```


```
##  [1] "id"                     "title"                  "formats"               
##  [4] "thumbnails"             "thumbnail"              "description"           
##  [7] "uploader"               "uploader_id"            "uploader_url"          
## [10] "channel_id"             "channel_url"            "duration"              
## [13] "view_count"             "age_limit"              "webpage_url"           
## [16] "categories"             "tags"                   "playable_in_embed"     
## [19] "live_status"            "automatic_captions"     "subtitles"             
## [22] "comment_count"          "chapters"               "like_count"            
## [25] "channel"                "channel_follower_count" "upload_date"           
## [28] "availability"           "webpage_url_basename"   "webpage_url_domain"    
## [31] "extractor"              "extractor_key"          "display_id"            
## [34] "fulltitle"              "duration_string"        "is_live"               
## [37] "was_live"               "comments"               "format"                
## [40] "format_id"              "ext"                    "protocol"              
## [43] "format_note"            "filesize_approx"        "tbr"                   
## [46] "width"                  "height"                 "resolution"            
## [49] "fps"                    "dynamic_range"          "vcodec"                
## [52] "vbr"                    "aspect_ratio"           "acodec"                
## [55] "abr"                    "asr"                    "audio_channels"        
## [58] "epoch"                  "_type"                  "_version"
```

From here we can easily pull the comment text. 


```r
comments <- out$comments |> 
  mutate(text = str_replace_all(text, "\n", " "),
         text = str_replace_all(text, "  ", " "),
         timestamp = lubridate::as_datetime(as.numeric(timestamp, tz = "America/Los_Angeles")))
```


```
## # A tibble: 6 × 4
##   text                                     timestamp           like_count author
##   <chr>                                    <dttm>                   <int> <chr> 
## 1 Big whiskey and the groogrux king was n… 2023-05-21 23:12:00          0 Jake …
## 2 Going to dmb shows was the best back in… 2023-05-21 21:00:00          0 Chris…
## 3 Those songs always bring me back to sim… 2023-05-21 20:00:00          1 Chris…
## 4 I've never heard of Dave Matthews        2023-05-21 19:00:00          0 Norbe…
## 5 Glad hes still getting the recognition … 2023-05-21 19:00:00          1 Scrub…
## 6 Dave Matthews Band is one of my Dad's f… 2023-05-21 19:00:00          0 Bryce…
```

## Getting comments using just R and Python

I'm sure that this could be much more streamlined working solely in Python, but this is how 
you would do it without using command line tools.

First, this is the Python function to pass YouTube urls into in order to pull the comments. 


```r
# python function 
def get_yt_info(URL):
  import json
  import yt_dlp

  # See help(yt_dlp.YoutubeDL) for a list of available options and public functions
  ydl_opts = {'getcomments':True}
  with yt_dlp.YoutubeDL(ydl_opts) as ydl:
      info = ydl.extract_info(URL, download=False)
      return(info)
```

We're going to use the `purrr::map()` function to pass through multiple urls, which passes back the expected lists, but it seems to be doing so one list deeper than we'd normally prefer. 

So we're going to use this function to extract our comment data from the lists and put them into a single dataframe. We're going to add a column with the video title so that we can tell which comments are for which video. 


```r
youtube_extract <- function(datalist) {
  # get summary data about the video
  summary <- purrr::map_dfr(datalist, 
                     magrittr::extract, 
                     c("uploader", "title", "description", "categories",
                       "duration_string", "view_count", 
                       "like_count", "comment_count", "original_url")) |> 
    dplyr::rename(duration = duration_string)
  # get the comments
  x <- purrr::map(datalist, "comments")
  comments <- NULL
  for (i in seq_len(nrow(summary))) {
    tmp <- x[[i]] %>% 
      do.call(rbind.data.frame, .) |> 
      dplyr::as_tibble() |> 
      dplyr::mutate(timestamp = lubridate::as_datetime(as.numeric(timestamp, tz = "America/Los_Angeles"))) |>
      dplyr::select(c(text, timestamp, like_count, author)) |> 
      dplyr::mutate(video = summary$title[i],
                    timestamp = as.Date(timestamp)) |> 
      dplyr::rename(date = timestamp)
    comments <- rbind(comments, tmp)
    tmp <- NULL
  }
  out <- list(summary = summary, comments = comments)
}
```

So, not the simplest, but absolutely works.  


```r
# assuming we've saved the python function, we have to call it via reticulate
reticulate::source_python(here::here('python_functions', 'get_yt_info.py'))
urls <- c("url1", "url2", "url3", ...)
# map over function
tmpdata <- urls |> 
  purrr::map(get_yt_info)
# clean and add to reactive values
out <- youtube_extract(tmpdata)
```

