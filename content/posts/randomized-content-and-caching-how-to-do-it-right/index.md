---
title: Randomized content and caching - How to do it right
author: Marcel Bootsman
categories:
- WordPress Tips
cover: 
  image: "images/random-duckies.jpg" # image path/url
  alt: "A lot of yellow rubber duckies" # alt text
  caption: "" # display caption under cover
  relative: false # when using page bundles set this to true
  hidden: false # only hide on current single page
date: "2018-01-30T13:21:24+01:00"
draft: false
summary: In this post I will let you know how to display random content on cached (static) pages.
showToc: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
---
For a client I had to display partners on the site. To give them all equal visibility, they needed to be displayed in random order. I had a solution, but then came WP Rocket. That reduced page loading times, but also eliminated the random display of the partners since WP Rocket generates static pages.

Initial solution
----------------

In the initial solution I used the `WP_Query` `orderby` argument and gave it the value `rand`. This gave me a randomized set results from the database, which were display through a widget. This works great, but it also requires database calls, every time the partners needed to be displayed. Which is on *every* page view. Yeah, not smart, I know. But we all need to learn right? And making mistakes is the best way of learning.

> Making mistakes is the best way of learning.

Optimizing the site
-------------------

When optimizing the site I installed and configured WP Rocket. A great plugin which helps site owners to make their website load fast. Really fast. Another plugin/service I installed was Imagify. This service reduces image sizes, and since this site uses a lot of images, the reduction of the image sizes (as in file size) resulted in faster loading.

One challenge remained, the random display of partners didn’t work anymore. Every page was cached, and the order of partners on that page was fixed. And that’s not what we wanted.

The final solution
------------------

Thinking about this, I asked the friendly WP Rocket support desk if it was possible to not cache a certain part (fragment) of a page. I got a fast answer from [Caspar Hübinger](https://caspar.blog/) who told me that this was not possible, since WP Rocket stores the whole HTML document and fragments cannot be excluded. He also mentioned that dynamic features should be done in JavaScript and not in PHP. And I totally agree on that.

So, I decided to lookup some samples of randomizing the display of elements on a page, and came across a very cool post on [Stackoverflow](https://stackoverflow.com/questions/1533910/randomize-a-sequence-of-div-elements-with-jquery). A user had a similar challenge and another user came up with a really cool solution. A jQuery function `randomize` is created to randomize elements in a certain container. In the following code example `div.band` is the container we want to check for occurrences of `table tr td` and the elements to randomize are `div.member`

```javascript
$('button').click(function() {
   $("div.band").randomize("table tr td", "div.member");
});

$.fn.randomize = function(tree, childElem) {
   return this.each(function() {
   var $this = $(this);
   if (tree) $this = $(this).find(tree);
   var unsortedElems = $this.children(childElem);
   var elems = unsortedElems.clone();

   elems.sort(function() { return (Math.round(Math.random())-0.5); });

   for(var i=0; i < elems.length; i++)
      unsortedElems.eq(i).replaceWith(elems[i]);
   });
};
```

In this case the `randomize` function is triggered by a button, but in my case I want to execute the function when the page is loaded. So I adapted the code a little, changed the element names and came up with this:

```javascript
$(document).ready(function () {
   $('section.widget_partner_widget').randomize('div.partners', 'div.supplier');
});
```

Now, the partner divs are randomly displayed. I’m happy and the client is happy.🕺