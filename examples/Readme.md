# Examples

For each example, you will find the matching exported file in the current folder.  
Import it in your extension via Drag&Drop through the "NEW PROJECT" menu.

Types of examples:
 1. Page reload:  
    details: Scrap, click on button, wait for page reload, scrap again  
    example: [Vinted-example](#vinted-example)  
 2. Ajax reload:  
    details: Scrap, click on button, wait for ajax reload, scrap again  
    example: TODO  
 3. Wait for captcha validation  
    details: Scrap, etc  
    example: TODO  
 4. Flow of action:  
    details: Login, consult profile, etc  
    example: TODO  

## Vinted-example

**SCRAPPER.JS**  
it scraps a list of product, click on the "NEXT PAGE" button, wait 1.2 sec after the page is loaded, and stop at the 4th page.    

**URLS.JSON**  
It runs scrapper.js on 2 different products page (see URLS.JSON).  

**DATAWORKER.JS**  
It sends the data via POST to a random API.  
It uses `fetch()`. And shows how to use it either with `await` or `.then()`.  
