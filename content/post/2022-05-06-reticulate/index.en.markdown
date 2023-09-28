---
title: Reticulate
author: twg
date: '2022-05-06'
slug: reticulate
categories:
  - reticulate
  - python
  - R
tags:
  - reticulate
  - python
  - R
subtitle: ''
summary: 'Using reticulate within R'
authors: []
lastmod: '2022-05-06T10:01:16-07:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---



A simple reference on using the `reticulate` package within R. Things make sense in real time and then coming back to it later and I've lost all memory of what I've done or how I've done it. 

Assuming that [miniconda](https://rstudio.github.io/reticulate/reference/install_miniconda.html) has been installed.  

## Creating a Conda environment

Create new Rstudio project and then within that project create a new conda environment


```r
library(reticulate)
conda_create("twg-site")
```

Reticulate seems to have an aggressive habit of forcing a specific python version within the environment. And what's interesting, is that I don't have an environmental variable that points to a specific python version. But if I use the `Sys.getenv('RETICULATE_PYTHON')` function for it I find that the python version is set somewhere:  

So, when we call `py_config()` to see which version is being used, it's always this base python3, which isn't that helpful.  


```r
library(reticulate)
reticulate::py_config()
```

```
## python:         /opt/homebrew/bin/python3
## libpython:      /opt/homebrew/opt/python@3.11/Frameworks/Python.framework/Versions/3.11/lib/python3.11/config-3.11-darwin/libpython3.11.dylib
## pythonhome:     /opt/homebrew/Cellar/python@3.11/3.11.3/Frameworks/Python.framework/Versions/3.11:/opt/homebrew/Cellar/python@3.11/3.11.3/Frameworks/Python.framework/Versions/3.11
## version:        3.11.3 (main, Apr  7 2023, 20:13:31) [Clang 14.0.0 (clang-1400.0.29.202)]
## numpy:           [NOT FOUND]
## 
## NOTE: Python version was forced by RETICULATE_PYTHON_FALLBACK
```

If we use the `conda_list()` function we can see the various conda environments that have been set up. So what we want to do is create our own .Renviron file within our project to ensure that the proper environment is called every time. This way, we know that packages downloaded for a specific project can be found again.


```r
conda_list()
```

```
##           name                                                                    python
## 1         base                   /Users/taylorgrant/Library/r-miniconda-arm64/bin/python
## 2 r-reticulate /Users/taylorgrant/Library/r-miniconda-arm64/envs/r-reticulate/bin/python
## 3     scrapper     /Users/taylorgrant/Library/r-miniconda-arm64/envs/scrapper/bin/python
## 4   topicmodel   /Users/taylorgrant/Library/r-miniconda-arm64/envs/topicmodel/bin/python
```

Now create an .Renviron file with the proper directory location


```r
usethis::edit_r_environ(scope = 'project')

# this opens the new file then add this, save, and restart
RETICULATE_PYTHON=/Users/taylor_grant/Library/r-miniconda/envs/twg-site/bin/python
```

On restart, the correct conda environment will be found. 

## Installing packages 

Installing packages is very easy via the `conda_install()` function. There are essentially three arguments - the conda environment, e.g., "twg-site", the package to install, and whether to use pip or the conda channels. By default, it uses conda channels.



```r
reticulate::conda_install("twg-site", "statsmodels", pip = TRUE)
```

You can specify specific package versions to install by including the package version, e.g., `conda_install("twg-site", top2vec=="1.0.26", pip = TRUE)`

## Removing packages 

Just as easy, use the `conda_remove()` function. 


```r
reticulate::conda_remove("twg-site", "statsmodels", pip = TRUE)
```

Occasionally, the `conda_remove()` function will fail to find a package that is clearly installed in the conda environment. If that happens, the following code is useful. Put the package name in quotes. The trailing '-y' tells not to ask for confirmation.


```r
system2(reticulate::py_exe(), c("-m", "pip", "uninstall", <package>, '-y))
```

## Working in Python 


To switch to python, simply call 


```r
reticulate::repl_python()
```

You can exit the environment by typing `exit` in the command line. 

Within R you can call objects created in python with by specifying `py$` much like you would call a column from a data frame. 

For the rest see [calling_python](https://rstudio.github.io/reticulate/articles/calling_python.html)


