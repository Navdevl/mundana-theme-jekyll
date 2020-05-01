---
layout: post
title:  "Google Apps Script - How to start?"
categories: [ engineering ]
image: assets/images/google-sheets-addon.png
tags: [featured]
---

Hello! Hola! You might be trying to learn to do some basic automation with Google App Script and trying to figure out where to start. Great, finally you are on the right place.

<br>
## What is Google App Script?
For people who have little or no idea on GAS, let me put it in very simple ways. GAS is a platform released by Google to enable their power users to make more impact through their app.

You can do a lot of magic on MOST of Google’s applications say Sheets, Doc, Gmail etc.

_Say you are tracking all your expenses in an Android application. The android application is a simple one. You just need to insert the “Name of expense”, “how much was the expense” and “when did you do it”._

So, end of the month it shows in a graph about your expenses. Also they provide an API which you can make use of.

Now, you can simply wrap your Google sheets around the API and automatically stream every content from your app to the Sheets.

End of the month, you can share it with your fam & friends (if you need to boast about your strategical money spending methods).

## It is all simple Javascript
_Where should I be starting might be your first question?_

You should be aware of some Javascript concepts and familiarise with basics like variables, functions, loops and conditions. This is more than enough to do a simple application.

You can brush your JS skills in the following sites. The scope of our article will not be covering all this.

[Introduction to Javascript](https://www.codecademy.com/learn/introduction-to-javascript)

[Glossary of Modern Javascript Concepts](https://auth0.com/blog/glossary-of-modern-javascript-concepts/)

[Intro to Javascript by Udemy](https://www.udacity.com/course/intro-to-javascript–ud803)

These courses also cover the ES6 part of Javascript. But Google is not yet released any support to ES6, so you can’t type ES6 stuffs on the [script.google.com](script.google.com) (Although there are few workarounds, that aren’t covered in our topic)

## Starting with the Basics
We are going to create a Sheets app that will fetch an API and fill our sheets.

This is our data that we are going to fill our Sheets app with.

[http://www.mocky.io/v2/5cd69dbf300000aa00606184](http://www.mocky.io/v2/5cd69dbf300000aa00606184)

```json
{
  "heading": [ "Name", "Age", "Gender"],
  "data": [
    ["Naveen", "23", "Male"],
    ["Elisa", "22", "Female"]
  ]
}
```

#### Create a Google Sheet application
Go to sheets.new and save the sheet with the name your prefer. Now, click on `Tools -> Script Editor`. This will take you to [script.google.com](script.google.com). Save the project.

You will see a `Code.gs` file with an empty function over there. Don’t panic. GoogleScript(.gs) is similar to Javascript(.js). A very few differences have to be noted, but we are not covering in this blog. Interested folks can DM on [Twitter](https://twitter.com/nav_devl) and I can help you on this.

#### Building the Menu bar
Now there’s a bit of code we will do that will make our sheet to create a magical menu bar like the image below.

![building the menu bar](/assets/images/google-sheets-addon/building-the-menu-bar.png){:class="img-responsive"}

See the last menu option `Getting Started`. It also has an option to `Sync with API..` in it. This is an easy one to build.

Sheets are controlled by the global name `SpreadsheetApp` and you can read more on the methods it provides here.

There are some default triggers available to the appscript. Like `onOpen`, `onEdit`, `onInstall` etc. We are going to make use of our onOpen to trigger to build an UI on the Sheets app that will create us a menu bar.

```javascript
/**
 * @OnlyCurrentDoc
 */

function onOpen() {
  var spreadsheet = SpreadsheetApp.getActive();
  var menuItems = [
    {name: 'Sync with API...', functionName: 'syncAPI'}
  ];
  spreadsheet.addMenu('Getting Started', menuItems);
}
```
In the line 6, we select the active sheet from our app and in line 10 we are adding menu items. You can see in line 8, we define `name` and `functionName` attribute to the menuItems. That function will be triggered when you click on the menu item in the menu bar.

We are going to define the `syncAPI` function for our line 8 in the next section!

#### Define your own magic
So, now we are near to our `endgame`. We are going to define our `syncAPI` function which is going to make call to [http://www.mocky.io/v2/5cd69dbf300000aa00606184](http://www.mocky.io/v2/5cd69dbf300000aa00606184) and fill the data from there to our application.

GoogleAppScript provides a different way to make an HTTP call from the appscript. It provides a [function](https://developers.google.com/apps-script/reference/url-fetch/url-fetch-app) called `UrlFetchApp` which takes in url and `options` as params and fetches response from the given input.

If you don’t understand, just go along with me, you’ll understand in the next few paragraphs!

```javascript
function syncAPI() {
  var spreadsheet = SpreadsheetApp.getActive();
  var firstSheet = spreadsheet.getSheets()[0];
  var url = 'https://www.mocky.io/v2/5cd69dbf300000aa00606184.json'
  var response = UrlFetchApp.fetch(url);
  var rawBody = response.getContentText()
  var body = JSON.parse(rawBody);
  
  var heading = body['heading'];
  firstSheet.appendRow(heading);
  
  // Go proceed to loop through the data and 
  // append the data in each row
}
```
_So, in our line 2 and line 3, it is simple. We are trying to select the first available sheet. You can also select by name and such. Do your own research on that, dear!_

_Now line 4-7, is usual thing that we do in JS. If still you aren’t familiar, you need to read JS back again._

_Line 9-10 does our magic. Voila!_

![Voila](/assets/images/google-sheets-addon/voila.png){:class="img-responsive"}

This isn’t a great achievement, though! But it is still a progress and it does matter. Now, go do your work on the remaining part of the code and let me know what you are messing up while doing this and I am always on Twitter to help you out.

## What’s next?
There are few things you can progressively learn after doing this.

1. Learn about the appscript.json - the manifest file and how to do scopes etc.
2. Learn to use range and follow best practices with sheets.
3. Try building Gmail-addon that would be an intermediate learning.
4. Learn to use Clasp. Try to develop locally and push.
5. Use webpack’s power to write appscripts using ES6 and compile back to ES5 and push. (You can also use Clasp’s power for that as well, but my favorite is always webpack)

Last, but not the least. Don't forget to connect me on Twitter [@nav_devl](https://twitter.com/nav_devl)
