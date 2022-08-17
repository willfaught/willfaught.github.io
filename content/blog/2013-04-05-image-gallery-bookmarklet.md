---
date: '2013-04-05T20:35:00+00:00'
draft: false
tags:
- computation
- code
title: Image gallery bookmarklet
---

Display all images in a web page in a gallery: javascript:function p(m){var s=300;return m.width>=s&&m.height;>=s;}function f(m){var s=300;var ow=m.width;var oh=m.height;var nw=Math.min(s,ow);var nh=oh*(nw/ow);if(nh>s){oh=nh;nh=s;nw*=nh/oh;}h+="[![]("+m.src+")]("+m.src+")";}var h="";var c=0;var g=document.images;for(var i=0;i0){var d=document.open();d.write(""+h+"");d.close();}else alert("No images"); I added a predicate to filter out images that were too small.
