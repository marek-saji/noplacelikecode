---
layout: post
title: quoting selected text
date: '2010-03-10T05:03:09+01:00'
tags:
- javascript
- ui
tumblr_url: http://saji-codes.tumblr.com/post/438224962
---
```js
/**
 * Selecting text in <article /> will make a button appear,
 * after clicking it selected text will get copied to
 * reply form
 */
$('article').bind('mouseup', function(e){
  
    // remove previous instances of the button
    $('.quote-this').remove();
  
    // when nothing is selected, do nothing
    if (window.getSelection().isCollapsed)
        return;

    $('<button />', {
        'class' : 'quote-this',
        'text' : 'quote this',
        'title' : 'use selected text as quotation in a reply',
        'css' : {
            // place button on mouse position
            'position': 'absolute',
            'top': e.pageY + 'px',
            'left': e.pageX + 'px',
            // avoiding selecting button when double clicking
            'margin': '1em'
        },
        click: function(e){
            e.stopPropagation();
            var sel = window.getSelection();
          
            // place selected text in reply form
            $('.reply').text(
                sel.toString()
                    .replace(/^\s*|\s*$/g, '')  // trim
                    .replace(/(\n|^)/g, "$1> ") // add quote markup
            );
          
            sel.collapse(); // un-select
            $(this).remove(); // destroy me
         }
    // button.quote-this creation end
    }).hide() // we'll show it in a sec.
        // place somewhere outside element that mousedown
        // is bond to
        .appendTo('body')
        // appear misteriously
        .fadeIn();

}); // article mouseup
```



<iframe src="https://jsbin.com/epade3/5" style="width: 100%; height: 30em">
    <p><a href="https://jsbin.com/epade3/5">view it in action</a></p>
</iframe>
