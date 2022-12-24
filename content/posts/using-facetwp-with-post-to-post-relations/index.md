---
title: Using FacetWP with post to post relations
author: Marcel Bootsman
categories:
- WordPress Tips
cover: 
  image: "images/coffee-and-filter.jpg" # image path/url
  alt: "Making coffee the old fashioned way" # alt text
  caption: "" # display caption under cover
  relative: false # when using page bundles set this to true
  hidden: false # only hide on current single page
date: "2018-09-07T14:26:59+02:00"
draft: false
summary:  On a client site some posts have a relationship with other posts, set in meta fields. The client wanted to have a category filter for products on a brand page. In this blog I will explain what I did to achieve this.
showToc: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
---
On a client site some posts have a relationship with other posts, set in meta fields. The client wanted to have a category filter for products on a brand page. In this blog I will explain what I did to achieve this.

The Relationship
----------------

Let me first explain what the post to post relationship is.

There are two [Custom Post Types](https://codex.wordpress.org/Post_Types): Brand and Product. Products can have exactly one brand, and these are stored in a custom field made with [Advanced Custom Fields](https://www.advancedcustomfields.com/).

The Requirement
---------------

The client was showing all products of a brand on the brand page. This was the initial requirement, but as we all know, requirements can change. And so did this one. The client wants visitors to be able to filter the products by category. This allows them to better find a product they want, for example a desk, a chair or a separator wall.

The solution in words
---------------------

When a client tells me about a requirement, or a change, I immediately start to browse through my known plugins library in my head, and fire up my code engine. Also in my head. 🤯

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

```php
<?php
echo '<div class="products">';
while ( have_posts() ) {
   the_post();
   echo '<div class="product">';
   echo '<div class="product-image"><a title="' . get_the_title() . 
        '" href="' . get_the_permalink() . '">' . 
        get_the_post_thumbnail( get_the_ID(), 'rectangle-landscape' ) . 
        '<h2 class="product-title">' . get_the_title() . '</h2></a></div>';
   echo '</div>';
}
echo '</div>';
?>
```

The template and facets are loaded in the page template for brands. And that’s done with this code:

```php
<?php

// Add brand collection to single-brand with facets and facet template
add_action( 'genesis_entry_content', 'nstrm_add_brand_products', 15 );
function nstrm_add_brand_products() {
   if ( is_singular( 'brand' ) ) {
      // load facets
      echo facetwp_display( 'facet', 'productcategory' );
    
      // load default template
      echo facetwp_display( 'template', 'products' );
   }
}

?>
```

The above code is used in a [Genesis](https://www.studiopress.com/themes/genesis/) theme, but can be used in other themes as well.

When we stop after these steps, products will be shown and the facets will be active (if product-categories have been assigned to products). But, all products will be shown, and not just the products that belong on this brand page.

Adding brand awareness  
----------------------

We need to make sure FacetWP is aware that it should only show products that have the product-category set to the brand of the currently active brand page. This is where the `facetwp_query_args` filter comes in handy. We need to add some arguments to the query, being the brand. To do that we need the brand id.

On a ‘normal’ page or post, it;s easy to get the post ID. But, when using FacetWP, which uses AJAX calls, the post ID can sometimes not be present. So, we need to help FacetWP by adding the post ID to the page in a little snippet of JavaScript, and use that in the `facetwp_query_args` filter function.

```php
<?php 
// facetwp - pass current post_id to FacetWP
add_action( 'wp_head', function () {
   if ( is_singular( 'brand' ) ) {
      global $post;
      ?><script>
      (function ($) {
         $(document).on('facetwp-refresh', function () {
            FWP_HTTP.post_id = '<?php echo $post->ID; ?>';
         });
      })(jQuery);
      </script>
      <?php
   }
}, 100 );

?>
```

And let’s create the filter function so we can use the brand id in the query that FacetWP uses.

```php
<?php

//facetwp - add facetwp_query_args filter for limiting products by brand
add_filter( 'facetwp_query_args', function ( $query_args, $class ) {

   // get post_id from Ajax request
   $post_id = (int) $class->http_params[ 'post_id' ];
   if ( empty( $post_id ) ) {
      $post_id = get_queried_object_id();
   }

   $query_args[ 'meta_key' ]       = 'merk';
   $query_args[ 'meta_value' ]     = $post_id;
   $query_args[ 'posts_per_page' ] = '-1';

   return $query_args;
}, 10, 2 );

?>
```

That’s it. Happy coding and thanks for reading. If you have any remarks or questions, please let me know in the comments.

Photo by [rawpixel](https://unsplash.com/photos/ytHiKjbaQoY?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/filter?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)