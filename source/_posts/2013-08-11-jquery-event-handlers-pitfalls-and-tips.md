---
layout: post
title:  Jquery Event Handlers - Pitfalls and Tips
categories: [ development, jquery, javascript ]
comments: true
sharing: true
footer: true
---
Always return true or false at the end of the handler; true to allow additional events to fire, false to stop event propogation. If you do not oh boy will you get very strange behavior. For example, in recent Firefox releases you will get an error thrown with HTTP status 0 and message “Error,” yep, that is all you get.

if you have mutiple elementa trigger the same event and the only difference is data ( for example a list of names where clicking posts the name ), write a handler based off of a CSS class selector. Do not create one handler per row or item using unique div IDs as the CSS selector – this eats browser memory quickly!

Save jQuery elements in function scoped variablea and chain events from them – doing $( “selector” ) calls for the same element over and over again is expensive time and memory wise and slows your code down.

