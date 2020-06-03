# webscrapper-free

[![Join the chat at https://gitter.im/webscrapper-free/community](https://badges.gitter.im/webscrapper-free/community.svg)](https://gitter.im/webscrapper-free/community?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

This is a free extension for developers. It provides web-scrapping capabilities in Javascript inside the browser.

# Presentation Video
[![youtubevideo](https://img.youtube.com/vi/7GL9g8ac9ME/0.jpg)](https://youtu.be/7GL9g8ac9ME)

# Installation
 Link to chrome web store coming on July 1st.

# Table of content

1. [Usage](#usage)  
    1. [COMMON](#common)
    3. [STORAGE](#storage)
    4. [COMPATABILITY](#compatability)
    5. [SCRAPPER.JS](#scrapperjs)  
        1. [rws.log](#rwslog)  
        2. [rws.resolve](#rwsresolve)  
        3. [rws.manualActionRequired](#rwsmanualactionrequired)  
    6. [URLS.JSON](#urlsjson)  
    7. [DATA.JSON](#datajson)  
    8. [DATAWORKER.JS](#dataworkerjs)  
        1. [rws.log](#rwslog-1)  
        2. [rws.resolve](#rwsresolve-1)  
        3. [rws.data](#rwsdata)  
        4. [rws.saveAs](#rwssaveas)  

# Usage

## Common

You javascript code, is run inside an `async function` inside a `Promise`.  
It means you can use `await` directly, but you must make sure to call `rws.resolve(...)` (cf according definitions [scrapper](#rwsresolve)/[dataworker]((#rwsresolve-1))) to actually end your script.

## Storage
Everything is stored in your browser localstorage.  
If you want to save/share your projects, use the export feature.  
NB: /!\ You will loose everything by uninstalling the extension.

## Compatability
For now, only Google Chrome is supported.  
Depending on demands, support for Firefox will be added.  

## SCRAPPER.JS

This script is run as a content script injected in the selected target tab.  
It means you can manipulate DOM, and you have a separate javascript runtime. Even if the target website uses JQuery, you won't have access to it.  
Also, you should never use global variable inside this script. do not use `window.myvar =...` or `var myvar =...`.  
  
It is injected after the tab's loading status switch to `"complete"`.  
It is noteworthy that some ecommerce website tries to prevent this status from being emitted by loading some ressources with a very long timeout (such as darty.fr). But they will timeout at some point, so you just need to wait.

### rws.log

#### Definition
```javascript
/** 
 * Use this function to log inside the extension console.  
 * `log(object)` will also perform a `console.log(object)` inside the target page.
 * @param {any} object - Must be a json serializable object, a HTMLElement or a DOM Node.
 * @param {String} [type=json] - (default:json) Which colorization to apply. Available values `['typescript', 'javascript', 'json', 'html', 'css', 'text']`
 * @returns {String} the logged string.
 */
log: (object, type='json') => {},
```
#### Examples
```javascript
rws.log({a:1});
rws.log(document.querySelectorAll('H1'));
rws.log(document.URL, 'text');
rws.log('console.log("test");', 'javascript');
```
#### Good to know
`rws.log` will do its best to stringify the object passed before sending it to the right panel. For this reason, it is also displayed as is in the target tab developer console.
The following types are available: `'typescript'`, `'javascript'`, `'json'`, `'html'`, `'css'`, `'text'`.  

### rws.resolve

You must always call this function to end your script, or it will hang and never stop.  

#### Definition
```javascript
/**
 * This object contains the data you want to save and optionnaly a button to click to scrap the next.
 * @typedef ScriptAction
 * @type {object}
 * @property {any} data - the data you want to save when this script ends.
 * @property {Object} [nextPage] - (optionnal) if you want to execute the script on another page.
 * @property {string} nextPage.buttonPath - The element to send a `click` event to.
 * @property {String} [nextPage.waitElemPath] - (optionnal) If defined, will wait for this elem to be present in the page before running the script again instead of waiting for the page to reload. Useful if the clicked elem triggers an ajax call.
 */
/** 
 * Call this function to :
 * 1. end your script  
 * 2. collect the data  
 * 3. (optional) click on button and execute your script again after the page loads  
 * @param {ScriptAction} [action]
 * @returns {void} make sure to end your script after this call.
 */
resolve: (action) => {},
```
#### Examples
Ends your script, doesn't save any data.  
```javascript
rws.resolve();
```
Ends your script, saves url.  
Adding `/** @type {ScriptAction} */` above the variable will activate auto-completion in the editor.
```javascript
/** @type {ScriptAction} */
let scriptAction = {data:{ url:document.URL }};
rws.resolve(scriptAction);
```
`...more processsing here...` will be executed and might cause an error or unwanted behavior. Always make sure `rws.resolve()` is your last call.
```javascript
rws.resolve();
...more processsing here...
```
Ends your script, saves your data, send a click event to `buttonPath` and wait for the page to be reloaded before executing your script again.  
You must make sure `buttonPath` exists before passing it, otherwise the page will never reload and the scrapper will hang.  
```javascript
/** @type {ScriptAction} */
let scriptAction = {
  data: {url: document.URL},
  nextPage: {
    buttonPath: '.nextPage',
  }
};
rws.resolve(scriptAction)
```
Ends your script, saves your data, send a click event to `buttonPath` and wait for the `waitElemPath` to be present in the page before executing your script again.  
You must make sure `buttonPath` exists before passing it, otherwise the scrapper will wait forever for `waitElemPath` to appear.  
```javascript
/** @type {ScriptAction} */
let scriptAction = {
  data: {url: document.URL},
  nextPage: {
    buttonPath: '.nextPage',
    waitElemPath: '.newElement',
  }
};
rws.resolve(scriptAction)
```
#### Good to know
Your script runs inside a promise. Therefor failing to call `rws.resolve()` will result in an ever hanging scrapper.  
When is happens, close the target tab and refresh the extension.

### rws.manualActionRequired

Maybe the most ground-breaking feature here.  
When you call this function, it will display a modal in the extension window. Depending on your answer, it will either skip the current page or re-run the script.  

As example, you can :
 - check whether the website caught you as a robot and displayed a captcha.
 - Call `rws.manualActionRequired` to pause your processing and wait for a user intervention.
 - Re-execute your script again.

#### Definition
```javascript
/** 
 * example use case:
 * You identify the page is a captcha needing solving in order to access the actual content.
 * You call `manualActionRequired('Please resolve captcha')` :
 * 1. A dialog will appear in the editor
 * 2. you resolve the captcha manually
 * 3. you wait for the content of the page to be loaded
 * 3. you click the "Continue" button to re-run the script on the page.
 * @param {String} message - the message displayed in the extension.
 * @returns {void} make sure to end your script after this call.
 */
manualActionRequired: (message) => {},
```
#### Examples
At the beginning of our scrapper.js script, we check if the website redirected us to a capthcha page. If yes, request user intervention.  
```javascript
if (document.URL.includes('url/path/to/catch.robots'))
 return rws.manualActionRequired('Please validate the captcha page, wait for it to be reloaded and click continue.');
```
#### Good to know
Make sure your script end after calling this function, otherwise the rest will be executed causing unwanted behaviors.  

## URLS.JSON

The content of this file is used when you click on `Run on all URLs and save scrapped data`.  
Each URL is processed one after another.  
The next URL is processed once scrapper has finished executing and once `buttonPath` is null or undefined.  

The content of URLS.JSON will be processed with `JSON.parse`. Therefor:  
**Good**
```json
{
  "url1"
}
```
**Bad**
```json
{
  "url1",
  "url2", //some javascript comment
  'url3',
  "url4",
}
```

## DATA.JSON

This file is read-only.
This content of this file is JSON.  
It is displayed inside the monaco editor, therefor provides its reading functionnality and colorization.

This file is populated after `Run on all URLs and save scrapped data` has finnished running, by the data you scrapped.

### Example
Let's say you scrap wikipedia.org with this script:
```javascript
let img = document.querySelector('img').src;
rws.resolve({data: {img}});
```
DATA.JSON will look like this:  
```json
[
  {
    "url": "https://www.wikipedia.org/",
    "date": "2020-06-01T18:34:47.949Z",
    "title": "Wikipedia",
    "userData": {
      "img": "https://www.wikipedia.org/portal/wikipedia.org/assets/img/Wikipedia-logo-v2.png"
    }
  }
]
```
Each time scrapper.js run, the scrapped data are encapsulated inside `userData`.  
`url`, `date`, `title` are also automatically filled in.  
NB: `date` is the result of `new Date()` at the time the data was returned.

## DATAWORKER.JS

This script is run inside a web kworker created in the extension tab.
It means you can not manipulate the DOM, and you have a separate javascript runtime.

Reccomendations:
 - you shouldn't transform your data here.  
  Use this worker to send them and process them on your server.
 - Use [fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) for your requests.  
 
### rws.log

#### Definition
see [scrapper.js/rws.log/definition](#definition).  

#### Examples
see [scrapper.js/rws.log/examples](#examples).  

#### Good to know
Logs are also displayed as is in the extension tab developer console.

### rws.resolve

You must always call this function to end your script, or it will hang and never stop.  

#### Definition
```javascript
/** 
 * Call this function to : end your script
 * @returns {void} make sure to end your script after this call.
 */
resolve: () => {},
```
#### Examples
Ends your script.  
```javascript
rws.resolve();
```
Use it in a promise.  
```javascript
fetch('https://swapi.dev/api/people/1')
.then(response => response.json())
.then(rws.log)
.then(rws.resolve)
```
#### Good to know
Your script runs inside a promise. Therefor failing to call `rws.resolve()` will result in an ever hanging dataworker.  
When it happens, click again the play button, it will terminate the worker.

### rws.data

An object containing the data from DATA.JSON.

#### Definition
```javascript
/** 
 * @type {Array.<any>} - the data previously scrapped.
 */
data: [],
```

#### Examples
Ends your script.  
```javascript
rws.data.map(...);
```

#### Good to know
You can't edit them. At least it won't be reflected back in DATA.JSON.  

### rws.saveAs

Allows you to save a string as a file in your browser.

#### Definition
```javascript
/** 
 * Use this function to download a string as a file in the browser.
 * @param {String} data
 * @param {String} mimeType - See {@link https://www.iana.org/assignments/media-types/media-types.xhtml List of types}
 * @param {String} name
 * @returns {String} the logged string.
 */
saveAs: (data, mimeType, name) => {},
```

#### Examples
Save data locally in a json file.
```javascript
rws.saveAs(JSON.stringify(rws.data), 'application/json', 'myFile.json');
```

#### Good to know
This functionnality relies on [file-saver](https://github.com/eligrey/FileSaver.js).  
