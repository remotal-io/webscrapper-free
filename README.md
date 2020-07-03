# webscrapper-free

[![Join the chat at https://gitter.im/webscrapper-free/community](https://badges.gitter.im/webscrapper-free/community.svg)](https://gitter.im/webscrapper-free/community?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

This is a free extension for developers. It provides web-scrapping capabilities in Javascript inside the browser.

# Presentation Video
[![youtubevideo](https://img.youtube.com/vi/7GL9g8ac9ME/0.jpg)](https://youtu.be/7GL9g8ac9ME)

# Installation
Get it from the [chrome web store](https://chrome.google.com/webstore/detail/webscrapper-for-developer/agkbkgialbnmaeamocceenjbhcjagigf)

# Table of content

1. [What's new in V2](#whatsnewinv2)
2. [Usage](#usage)

# What's new in V2

After using our own extension a bit, we noticed some big improvments were needed.  
We came up with the following new improvments:  
 - V1 projects are still supported.  
 - Only V2 projects will enjoy the followings improvments
    - Scrap an URL several times with different parameters.  
    - Parameters for URLs can be static or dynamic by getting them from your server right before scrapping a page.  
    - Using a switch, you can decide to automatically send the data to your server after each iteration of scrapper.js.  
    - In the left panel, you can now schedule your projects to run at different interval automatically
    - Clicking on the [RUN ON  ALL URLS....] button, will now run scrapper.js in a seperate tab, and  automatically close it at the end.  
    - Using rws.manualActionRequired("..."), will now open in a seperate tab.
    
# Usage

## Life-Cyle
//TODO

``` javascript
//URLS.JSON
{
  "https://www.wikipedia.org": [
    {"type": "json", "json": [1, 2, 3]},
    { "type": "url",
      "url": "https://api.themoviedb.org/3/movie/76341?api_key=test1",
      "options": {
        "method": "GET",
        "headers": { "Content-Type": "application/json" }
      }
    }
  ]
}
```
```
//SCRAPPER.JS
let img = document.querySelector('img').src;
rws.resolve({data: {img}});
```
```
//DATATWORKER.JS (automatic after each itteration - ON)
let url = 'https://api.themoviedb.org/3/movie/76341?api_key=test';
let options = {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json'
  },
  body: JSON.stringify(rws.data)
};
fetch(url, options)
 .then(response => response.json())
 .then(rws.log)
 .then(rws.resolve);
```

Here is what will happen //
