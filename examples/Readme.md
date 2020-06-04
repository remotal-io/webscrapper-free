# Examples

For each example, you will find the matching exported file in the current folder.  
Import it in your extension via Drag&Drop through the "NEW PROJECT" menu.

Types of examples:
 1. Page reload:  
    details: Scrap, click on button, wait for page reload, scrap again  
    example: [Vinted-example](#vinted-example)  
 2. Ajax reload:  
    details: Scrap, click on button, wait for ajax reload, scrap again  
    example: [CDiscount-example](#cdiscount-example)  
 3. Wait for captcha validation  
    details: See how 2Factor was handled in [GitHub-example](#github-example).  
 4. Flow of action:  
    details: Login, pass 2factor if needed, go to repositories, scrap all repositories  
    example: [GitHub-example](#github-example)  

## Vinted-example

**SCRAPPER.JS**  
It scraps a list of product, click on the "NEXT PAGE" button, wait 1.2 sec after the page is loaded, and stop at the 4th page.    

**URLS.JSON**  
It runs scrapper.js on 2 different products page (see URLS.JSON).  

## CDiscount-example

**SCRAPPER.JS**  
It scraps a list of product, delete the list of  product, click on the "NEXT PAGE" button, wait for the list to be re-populated, and stop when all pages are scrapped.

**URLS.JSON**  
It runs scrapper.js on 2 different products page (see URLS.JSON).  

## GitHub-example

**SCRAPPER.JS**  
It fills in login and password then submit, if 2Factor is requested it calls `rws.manualActionNeeded` to notify the user to solve it, it then go to the user repositories page, it scraps all the repositories' name (pagination is handled).  

**URLS.JSON**  
Just one url : `https://github.com/login`  

