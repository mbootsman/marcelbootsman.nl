---
title: 'Using FacetWP to show featured posts first'
date: '2019-11-03T11:07:13+01:00'
draft: false
author: 'Marcel Bootsman'
summary: 'Want to show featured posts first in FacetWP results? Here''s how to do it.'
categories:
    - 'WordPress Tips'
---
Want to show featured posts first in FacetWP results? Here’s how to do it.

The requirement
---------------

A client, using FacetWP to show posts filtered by a few Advanced Custom Fields, wanted to introduce featured posts. These posts need to be show first, and the rest of the results should be shown random.

Since FacetWP does an AJAX request every time a page is requested, or a facet is changed, we need to do something to prevent every request to show random posts, since that could show posts on multiple result pages.

Thinking about a solution
-------------------------

When a client drops an idea, actually any idea, my brain starts to think on how to implement this. With my experience I know some filters of my favorite plugins, but not all. So I draft a solution first.

Posts need to have an extra field, where the client can indicate a post is featured. Since the posts already have some ACF fields, I will add the featured field to it.

Next up is filtering the query to feed FacetWP the `$post_ids` in the right order, with featured posts first, followed by the other posts in a random order. I found out the `<a href="https://facetwp.com/documentation/developers/querying/facetwp_filtered_post_ids/">facetwp_filtered_post_ids</a>` filter can be used for that.

Coding the solution
-------------------

Here’s my favorite part of implementing a clients request. The actual coding. I start with writing the `facetwp_filtered_post_ids` filter function. This function has to return all post ids, in the right order, with the featured post on the front of the array.

The query arguments are self explanatory, except maybe for the `meta_compare` argument. `LIKE` is used here, becuase ACF stores the value of the `uitgelicht` (Featured) field in a serialized way. Be careful when using this, since this might give you unexpected results. In this case, the `uitgelicht` field can only have a value of `'ja'` (yes), or `''` (empty), so we can safely use this.

The we launch the query and get the results on line 16. NOw we need to remove the featured posts from the `$posts_ids` variable that is passed to this function through the filter. So we walk through the `$post_ids` and put the featured post id in the `$matches` array, and remove the post id from the `$post_ids` array. This gives us two arrays, `$matches` which holds the featured posts, and `$post_ids` which holds all other posts, without the featured posts.

Now we need to randomize the posts in `$post_ids`. As stated before, we don’t want to randomize on every request. With the client I discussed this, and came to a solution where results would be randomized every hour.

First I used the `<a href="https://www.php.net/manual/en/function.shuffle.php">shuffle()</a>` function to randomize the `$post_ids` array. Sadly, this did not give me an option to keep the random order for a certain amount of time. So, here comes `<a href="https://www.php.net/manual/en/function.array-multisort.php">array_multisort()</a>`.

We first set a duration in minutes, get the current time in minutes and subtract the duration from this time, to set the seed. Then we use the `<a href="https://www.php.net/manual/en/function.mt-srand.php">mt_srand()</a>` function to create seed. Next is seting the order and use that to re-order the `$post_ids` array with `array_multisort()`.

The final thing we need to do in this filter function is merge the randomized post ids, together with the featured posts and return the array.

🚀 **Performance tip**: If you only need post ids, add `'fields'       => 'ids'` to the query args. This results in a query that only asks for ids, and not all other fields available in the posts table.

Apply the sorting to the FacetWP template
-----------------------------------------

One final step is needed to get the results right. We need to set the order and orderby parameters on the FacetWP query for the template we want this to be used for. Here’s the `<a href="https://facetwp.com/documentation/developers/querying/facetwp_query_args/">facetwp_query_args</a>` filter. We add the query parameters to the original query parameters, and return the `$query_args` variable.

Here’s the code on Github, feel free to comment if you want.

<script src="https://gist.github.com/mbootsman/d1192b981e2f7c13d5df959bea16bb84.js"></script>Photo by [Susan Yin](https://unsplash.com/@syinq?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/library?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)