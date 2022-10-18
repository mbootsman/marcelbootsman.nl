---
title: 'Preselecting a facet and hiding it automatically with FacetWP'
date: '2019-09-18T16:14:13+02:00'
draft: false
author: 'Marcel Bootsman'
summary: 'In this short howto I will show you how you can preselect a facet, and hide it when the page renders.'
categories:
    - 'WordPress Tips'
---
In this short howto I will show you how you can preselect a facet, and hide it when the page renders.

Preselecting facets
-------------------

For this case the client has a few facets that are filled by custom fields. The fields are specialism and level. These are related to a custom post type Mentor.

**Note:** When you need to preselect values that contain special characters (tested with ‘ë’, ‘é’, ‘è’) just put a normal ‘e’ in the custom field.

To preselect a facet, we can use the `facetwp_preload_url_vars` filter. But, during execution the `$post` global variable is not available, so we need to first get the post. By using the `get_uri()` method of the `FWP->helper` class, we can get the uri. This can be mapped to the page name which a separate function `nostromo_get_post_id_by_post_name()` uses to get the post id.

When we have the post id, it’s possible to get the custom fields and use them as values for the preselects. We just have to add them to the `url_vars[]` array with the names of the facets.

At the end of the function we return the `url_vars` array, so the filter can be applied.

Hiding the preselected facet
----------------------------

To hide the preselected facet(s), all we have to is add a piece of CSS to the page, when the `url_vars` are set. Here’s when a hook comes in very handy. You can see the code on lines 23 and 35. You might need to tweak the CSS, or add some classes to make the CSS specific enough to work. Or use !important, just don’t tell anyone I gave you that advice.

<script src="https://gist.github.com/mbootsman/e8bcf65286a90a01a89423d36f98006b.js"></script>