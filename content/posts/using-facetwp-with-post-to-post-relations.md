---
title: 'Using FacetWP with post to post relations'
date: '2018-09-07T14:26:59+02:00'
draft: false
author: 'Marcel Bootsman'
summary: 'On a client site some posts have a relationship with other posts, set in meta fields. The client wanted to have a category filter for products on a brand page. In this blog I will explain what I did to achieve this.'
categories:
    - 'WordPress Tips'
---
On a client site some posts have a relationship with other posts, set in meta fields. The client wanted to have a category filter for products on a brand page. In this blog I will explain what I did to achieve this.

The Relationship
----------------

Let me first explain what the post to post relationship is.

There are two [Custom Post Types](https://codex.wordpress.org/Post_Types): Brand and Product. Products can have exactly one brand, and these are stored in a custom field made with [ACF](https://www.advancedcustomfields.com/).

The Requirement
---------------

The client was showing all products of a brand on the brand page. This was the initial requirement, but as we all know, requirements can change. And so did this one. The client wants visitors to be able to filter the products by category. This allows them to better find a product they want, for example a desk, a chair or a separator wall.

The solution in words
---------------------

When a client tells me about a requirement, or a change, I immediately start to browse through my known plugins library in my head, and fire up my code engine. Also in my head.

I made up the following:

- Use FacetWP
- Create a taxonomy `product-category` for product
- Create a facet based on the `product-category`
- Create a FacetWP template to show the products
- Done

Well, that looks easy, and in the end it was, but I had some struggles to get it right. The brand page only shows products of that brand. So I thought it would be handy to create a FacetWP template file in which I try to limit the WP query to only get products of the shown brand. And that’s where the s^%# hit the fan. But hey, making mistakes equals learning.

The solution in code
--------------------

The FacetWP template for this case is pretty simple. Just a default loop, with some code on how to show posts (products in this case). This file is stored in the root directory of the theme as `facetwp-products.php` so FacetWP can find it.

```
<pre class="wp-block-preformatted"><?php<strong><br></br></strong><strong>echo </strong><strong>'<div class="products">'</strong>;<br></br><strong>while </strong>( have_posts() ) {<br></br>   the_post();<br></br>   <strong>echo </strong><strong>'<div class="product">'</strong>;<br></br>   <strong>echo </strong><strong>'<div class="product-image"><a title="' </strong>. get_the_title() . <strong>'" href="' </strong>. get_the_permalink() . <strong>'">' </strong>. get_the_post_thumbnail( get_the_ID(), <strong>'rectangle-landscape' </strong>) . <strong>'<h2 class="product-title">' </strong>. get_the_title() . <strong>'</h2></a></div>'</strong>;<br></br>   <strong>echo </strong><strong>'</div>'</strong>;<br></br>}<br></br><strong>echo </strong><strong>'</div>'</strong>;<br></br><strong><br></br>?></strong>
```

The template and facets are loaded in the page template for brands. And that’s done with this code:

```
<pre class="wp-block-preformatted"><em><?php<br></br><br></br>// Add brand collection to single-brand with facets and facet template<br></br></em>add_action( <strong>'genesis_entry_content'</strong>, <strong>'nstrm_add_brand_products'</strong>, 15 );<br></br><strong>function </strong>nstrm_add_brand_products() {<br></br><strong>   if </strong>( is_singular( <strong>'brand' </strong>) ) {<br></br>  <em>    // load facets<br></br></em><strong>      echo </strong>facetwp_display( <strong>'facet'</strong>, <strong>'productcategory' </strong>);<br></br>    <br></br>      <em>// load default template<br></br></em><strong>      echo </strong>facetwp_display( <strong>'template'</strong>, <strong>'products' </strong>);<br></br>   }<br></br>}<br></br><br></br>?>
```

The above code is used in a [Genesis](https://nostromo.nl/genesis) theme, but can be used in other themes as well.

When we stop after these steps, products will be shown and the facets will be active (if product-categories have been assigned to products). But, all products will be shown, and not just the products that belong on this brand page.

Adding brand awareness  

-------------------------

We need to make sure FacetWP is aware that it should only show products that have the product-category set to the brand of the currently active brand page. This is where the `facetwp_query_args` filter comes in handy. We need to add some arguments to the query, being the brand. To do that we need the brand id.

On a ‘normal’ page or post, it;s easy to get the post ID. But, when using FacetWP, which uses AJAX calls, the post ID can sometimes not be present. So, we need to help FacetWP by adding the post ID to the page in a little snippet of JavaScript, and use that in the `facetwp_query_args` filter function.

```
<pre class="wp-block-preformatted"><em><?php <br></br>// facetwp - pass current post_id to FacetWP<br></br></em>add_action( <strong>'wp_head'</strong>, <strong>function </strong>() {<br></br><strong>   if </strong>( is_singular( <strong>'brand' </strong>) ) {<br></br>      <strong>global </strong>$post;<br></br>      <strong>?></strong><<strong>script</strong>><br></br>      (<strong>function </strong>($) {<br></br>         $(<strong>document</strong>).on(<strong>'facetwp-refresh'</strong>, <strong>function </strong>() {<br></br>            <strong>FWP_HTTP</strong>.<strong>post_id </strong>= <strong>'</strong><strong><?php echo </strong>$post-><strong>ID</strong>; <strong>?></strong><strong>'</strong>;<br></br>         });<br></br>      })(jQuery);<br></br>      </<strong>script</strong>><br></br>      <strong><?php<br></br></strong>   }<br></br>}, 100 );<br></br><br></br>?><br></br>
```

And let’s create the filter function so we can use the brand id in the query that FacetWP uses.

```
<pre class="wp-block-preformatted"><em><?php<br></br><br></br>//facetwp - add facetwp_query_args filter for limiting products by brand<br></br></em>add_filter( <strong>'facetwp_query_args'</strong>, <strong>function </strong>( $query_args, $class ) {<br></br><br></br><em>   // get post_id from Ajax request<br></br></em>   $post_id = (int) $class-><strong>http_params</strong>[ <strong>'post_id' </strong>];<br></br>   <strong>if </strong>( <strong>empty</strong>( $post_id ) ) {<br></br>      $post_id = get_queried_object_id();<br></br>   }<br></br><br></br>   $query_args[ <strong>'meta_key' </strong>]       = <strong>'merk'</strong>;<br></br>   $query_args[ <strong>'meta_value' </strong>]     = $post_id;<br></br>   $query_args[ <strong>'posts_per_page' </strong>] = <strong>'-1'</strong>;<br></br><br></br>   <strong>return </strong>$query_args;<br></br>}, 10, 2 );<br></br><br></br>?>
```

That’s it. Happy coding and thanks for reading. If you have any remarks or questions, please let me know in the comments.

Photo by [rawpixel](https://unsplash.com/photos/ytHiKjbaQoY?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/filter?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)