---
title: Remove WordPress REST API links
author: Marcel Bootsman
categories:
- WordPress Tips
cover: 
  image: "images/locked-fence-padlock.jpg" # image path/url
  alt: "A padlock and a chain locking a greenish fence" # alt text
  caption: "" # display caption under cover
  relative: true # when using page bundles set this to true
  hidden: false # only hide on current single page
date: "2019-11-11T13:42:09+01:00"
draft: false
summary: When using the WordPress REST API, it might be needed to hide some WP REST API links from the page source to the outside world. Here's how to do that.
showToc: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
---
When using the WordPress REST API, it might be needed to hide some WP REST API links from the page source to the outside world. Here’s how to do that.

Remove WordPress REST API Links
-------------------------------

Let’s say you have added your own endpoint to the WP REST API, and do not want to publicly show it in the page source. There are two places the WP REST API link is added to the page source:

- [default-filters.php @ 271](https://core.trac.wordpress.org/browser/tags/5.2/src/wp-includes/default-filters.php#L271) (WordPress version 5.2.4)
- [default-filters.php @ 272](https://core.trac.wordpress.org/browser/tags/5.2/src/wp-includes/default-filters.php#L272) (WordPress version 5.2.4)

To remove these links we simply remove the functions that are hooked to `wp_head` and `template_redirect`

```php
// Hide WP REST API links in page headers
remove_action( 'wp_head', 'rest_output_link_wp_head', 10);
remove_action( 'template_redirect', 'rest_output_link_header', 11);
```

Be aware that if REST API calls are done via AJAX, the browser shows the URL that is called, and it is visible to the public.

Bonus: Clear the WP-JSON index
------------------------------

If you have the WP REST API active, and don’t want people to see what endpoints are available, use below code.

```php
// Hide WP REST API JSON index
add_filter('rest_index', 'nstrm_hide_wp_json_index');
function nstrm_hide_wp_json_index( $response ){
	return array();
}
```

If you want to further customize the WP REST API, use the plugin [Disable REST API](https://wordpress.org/plugins/disable-json-api/). This plugin disables all endpoint of the WP REST API and allows you to enable individual endpoints.

{{< figure src="images/disable-rest-api.png" caption="Settings of the Disable REST API plugin." title="Screenshot of settings for the Disable REST API plugin" alt="Screenshot of settings for the Disable REST API plugin">}}
