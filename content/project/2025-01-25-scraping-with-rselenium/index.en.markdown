---
title: Scraping with RSelenium
author: twg
date: '2025-01-25'
slug: scraping-with-rselenium
categories:
  - scraping
  - R
  - selenium
tags:
  - scraping
  - selenium
subtitle: ''
summary: 'Using RSelenium to automate a browser and grab some data'
authors: []
lastmod: '2025-01-25T10:30:26-08:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---



{{% toc %}}

Normally, when data is loaded by a website with an identifiable script, it's pretty simple to get the data with code like [this](https://taylorgrant.netlify.app/post/web-scraping/#relying-on-data-loaded-by-javascript). It's also pretty simple to [get data](https://taylorgrant.netlify.app/project/consistent-signals/#getting-the-data) when it's being loaded via a GET request to an API, which can be found using developer tools. But I recently ran into an issue where a site was loading data dynamically, but wouldn't authorize my curl request. 

I guess it's time to learn the basics of RSelenium. 

## RSelenium needs java

If the computer doesn't have java, it's easy to use homebrew to install it. From the terminal, check to see if java is installed 


``` zsh
java -version
```

If java isn't found, use homebrew to install it. After install, homebrew provides you the code to set the path to java. 


``` zsh
brew install openjdk
# set the path
sudo ln -sfn /opt/homebrew/opt/openjdk/libexec/openjdk.jdk /Library/Java/JavaVirtualMachines/openjdk.jdk
# check it can be found 
java -version
```

Once that's done install RSelenium via CRAN or Github for the development version. 


``` r
install.package("RSelenium")
# install.packages("remotes")
remotes::install_github("ropensci/RSelenium")
```

RSelenium has a helpful [vignette](https://docs.ropensci.org/RSelenium/articles/basics.html) that walks through the basics of use. The vignette says the recommended setup is to run a Selenium Server by running a Docker image. For whatever reason, I've been unable to get that working on a silicon mac. But the built in `rsDriver()` function does work. 

## Why RSelenium

I was trying to scrape Kawasaki dealer locations in the US. The site forces you to search with a zip code and then dynamically returns a list of 10 dealers. You can't expand the search criteria to return more than 10 listings. I can't find any script that loads the data nor can I find an API that's being queried. So I'll use RSelenium to hit the site, search for a specific zip code, and then scrape the results. Eventually, I'll do this for every zip code in a state. 

## Using RSelenium

The first thing to do is to instantiate a new remote driver. This will open up a new Firefox browser.  


``` r
# start a session
rD <- rsDriver(browser = "firefox", port = 4545L, verbose = FALSE)
remDr <- rD$client
```

After starting, you have to direct your driver to a specific site. In this case, I want the kawasaki dealer locator. 


``` r
remDr$navigate("https://www.kawasaki.com/en-us/research-tools/dealer-locator")
```

<div class="figure" style="text-align: center">
<img src="https://res.cloudinary.com/dn83gtg0l/image/upload/v1737837560/rselenium/kawi_dealer_locator.png" alt="Dealer locator with results" width="80%" />
<p class="caption"><span id="fig:unnamed-chunk-6"></span>Figure 1: Dealer locator with results</p>
</div>

The page has a text field where you have to enter the zip code. To the right of the text field is a search button. If I'm going to have my browser enter a zip code and search, I need to be able to tell it where to put the zip and which button to push. This requires me to specify the css locations and tell the browser what to do. 

The css for the input and button can be found easily using developer tools. As you run each of these commands, you can reference the open Firefox browser, and see that it's directed to the page, the zip is filled, and then the button pushed with dealer information returned. 


``` r
# Enter the zip code
zip_input <- remDr$findElement(using = "css", "#dealerSearchInput")
zip_input$clearElement() # Clear any pre-filled text
zip_input$sendKeysToElement(list(as.character(98105)))
  
# Click the search button
search_button <- remDr$findElement(using = "css", "#dealerSearchBtn")
search_button$clickElement()
```

Now to get the dealer data, I also need to specify the css for each piece. 

<div class="figure" style="text-align: center">
<img src="https://res.cloudinary.com/dn83gtg0l/image/upload/v1737837560/rselenium/kawi_css.png" alt="View from Developer Tools" width="80%" />
<p class="caption"><span id="fig:unnamed-chunk-8"></span>Figure 2: View from Developer Tools</p>
</div>

If I look at the developer tools, I can see that the dealer data is found in a list group with the id #dealerSearchResults.  


``` r
dealers <- remDr$findElements(using = "css", "#dealerSearchResults > li") # Get all dealer result items
```

This returns a list of 10 items within my R environment, but in order to extract the data for each item, I need to specify the css of where each is found. If I highlight the row of data I'm interested in, e.g., "LAKE CITY KAWASAKI," and right-click on it, I can copy the selector, which will give me the css location of that data -- `#dealerSearchResults > li:nth-child(1) > a > h2`. I've already pulled the dealer info using `#dealerSearchResults > li` so I only need to use the second part. I'll do the same thing with the address and phone data. 

Below will pull the results for the first of my 10 dealers returned. 


``` r
# Extract dealer name
name <- dealers[[1]]$findChildElement(using = "css", "a > h2")$getElementText() |> 
  unlist()
      
# Extract address line 1
address_line1 <- dealers[[1]]$findChildElement(using = "css", "a > p:nth-child(2)")$getElementText() |> 
  unlist()
      
# Extract address line 2 (if available)
address_line2 <- dealers[[1]]$findChildElement(using = "css", "a > p:nth-child(3)")$getElementText() |> 
  unlist()
      
# Extract phone number
phone <- dealers[[1]]$findChildElement(using = "css", "a > p:nth-child(4)")$getElementText() |> 
  unlist()

tibble(name = name, address_line1 = address_line1, address_line2 = address_line2, phone = phone)
```


```
## # A tibble: 1 × 4
##   name               address_line1          address_line2     phone        
##   <chr>              <chr>                  <chr>             <chr>        
## 1 LAKE CITY KAWASAKI 12048 LAKE CITY WAY NE SEATTLE, WA 98125 (206)364-1372
```

## Putting this all into a function

Now that I have everything working, I can put this into a function to run over multiple zip codes. 


``` r
dealer_data <- map_dfr(zip_codes, function(zip) {
  # Open the dealer locator page
  remDr$navigate("https://www.kawasaki.com/en-us/research-tools/dealer-locator")
  Sys.sleep(2) # Wait for the page to load
  
  # Enter the zip code
  zip_input <- remDr$findElement(using = "css", "#dealerSearchInput")
  zip_input$clearElement() # Clear any pre-filled text
  zip_input$sendKeysToElement(list(as.character(zip)))
  
  # Click the search button
  search_button <- remDr$findElement(using = "css", "#dealerSearchBtn")
  search_button$clickElement()
  
  Sys.sleep(3) # Wait for results to load
  
  # Extract dealer information
  dealers <- remDr$findElements(using = "css", "#dealerSearchResults > li") # Get all dealer result items
  
  if (length(dealers) == 0) {
    # No dealers found, return a single row tibble with NA values
    message(glue("No dealerships found for zip code: {zip}"))
    return(tibble(
      ZipCode = zip,
      Name = NA_character_,
      AddressLine1 = NA_character_,
      AddressLine2 = NA_character_,
      Phone = NA_character_
    ))
  } else {
    # Map over each dealer element to extract data
    map_dfr(dealers, function(dealer) {
      # Extract dealer details
      name <- dealer$findChildElement(using = "css", "a > h2")$getElementText() |> 
        unlist()
      address_line1 <- dealer$findChildElement(using = "css", "a > p:nth-child(2)")$getElementText() |> 
        unlist()
      address_line2 <- tryCatch(
        dealer$findChildElement(using = "css", "a > p:nth-child(3)")$getElementText() |> unlist(),
        error = function(e) NA_character_
      )
      phone <- tryCatch(
        dealer$findChildElement(using = "css", "a > p:nth-child(4)")$getElementText() |> unlist(),
        error = function(e) NA_character_
      )
      
      # Return a tibble for each dealer
      tibble(
        query_zip = zip,
        name = name,
        address_line1 = address_line1,
        address_line2 = address_line2,
        phone = phone
      )
    })
  }
})
```

If I run this across the 98105 zip code, this is what I get. 


```
## # A tibble: 10 × 4
##    name                             address_line1            address_line2 phone
##    <chr>                            <chr>                    <chr>         <chr>
##  1 LAKE CITY KAWASAKI               12048 LAKE CITY WAY NE   SEATTLE, WA … (206…
##  2 BELLEVUE KAWASAKI                14004 NE 20TH ST         BELLEVUE, WA… (425…
##  3 LYNNWOOD MOTOPLEX KAWASAKI       17900 HIGHWAY 99         LYNNWOOD, WA… (425…
##  4 ADVENTURE MOTORSPORTS KAWASAKI   320 N LEWIS ST           MONROE, WA 9… (360…
##  5 SOUTH BOUND KAWASAKI             1200 CHARLESTON BEACH R… BREMERTON, W… (360…
##  6 NASH POWERSPORTS AUBURN KAWASAKI 1611 W VALLEY HWY S      AUBURN, WA 9… (253…
##  7 CYCLE BARN KAWASAKI              15202 SMOKEY POINT BLVD  MARYSVILLE, … (360…
##  8 ENUMCLAW KAWASAKI                408 ROOSEVELT AVE        ENUMCLAW, WA… (360…
##  9 SKAGIT VALLEY KAWASAKI           107 SUNDQUIST DR         MOUNT VERNON… (360…
## 10 PAULSON'S KAWASAKI               4402 6TH AVE SE          LACEY, WA 98… (360…
```

## Close the session

Don't forget to close the RSelenium session 


``` r
remDr$close()
rD$server$stop()
```

