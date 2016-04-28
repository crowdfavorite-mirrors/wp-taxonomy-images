Taxonomy Images
===============

A WordPress plugin that enables you to associate images from your media library with categories, tags and custom taxonomies.

Displaying Images in Your Theme
-------------------------------

There are a few filters that you can use in your theme to display the image associations created by this plugin. Please read below for detailed information.

Display a single image representing the term archive
----------------------------------------------------

The following filter will display the image associated with the term asked for in the query string of the URL. This filter only works in views that naturally use templates like category.php, tag.php, taxonomy.php and all of their derivatives. Please read about [template hierarchy](http://codex.wordpress.org/Template_Hierarchy) for more information about these templates. The simplest use of this filter looks like:

```php
print apply_filters( 'taxonomy-images-queried-term-image', '' );
```

This code will generate and print an image tag. It's output can be modifed by passing an optional third parameter to apply_filters(). This parameter is an array and the following keys may be set:

* __after__ _(string)_ - Text to append to the image's HTML.

* __attr__ _(array)_ - Key / value pairs representing the attributes of the `img` tag. Available options include: `alt`, `class`, `src` and `title`. This array will be passed as the fourth parameter to WordPress core function `wp_get_attachment_image()` without modification.

* __before__ _(string)_ - Text to prepend to the image's HTML.

* __image_size__ _(string)_ - May be any image size registered with WordPress. If no image size is specified, 'thumbnail' will be used as a default value. In the event that an unregistered size is specified, this filter will return an empty string.

Here's an example of what a fully customized version of this filter might look like:


```php
print apply_filters( 'taxonomy-images-queried-term-image', '', array(
	'attr'       => array(
		'alt'   => 'Custom alternative text',
		'class' => 'my-class-list bunnies turtles',
		'src'   => 'this-is-where-the-image-lives.png',
		'title' => 'Custom Title',
		),
	'before'     => '<div id="my-custom-div">',
	'after'      => '</div>',
	'image_size' => 'medium'
) );
```

Similar functionality
---------------------

If you just need to get the database ID for the image, you may want to use:

```php
$image_id = apply_filters( 'taxonomy-images-queried-term-image-id', 0 );
```

If you need to get the full object of the image, you may want to use:

```php
$image = apply_filters( 'taxonomy-images-queried-term-image-object', '' );
```

If you need to get the URL to the image, you may want to use the following:

```php
$image_url = apply_filters( 'taxonomy-images-queried-term-image-url', '' );
```

You can specify the size of the image in an option third parameter:

```php
$image_url = apply_filters( 'taxonomy-images-queried-term-image-url', '', array(
	'image_size' => 'medium'
) );
```

If you need data about the image, you may want to use:

```php
$image_data = apply_filters( 'taxonomy-images-queried-term-image-data', '' );
```

You can specify the size of the image in an option third parameter:

```php
$image_data = apply_filters( 'taxonomy-images-queried-term-image-data', '', array(
	'image_size' => 'medium'
	) );
```

List term images associated with a post object
----------------------------------------------

When a post is being displayed you may want to display the images associated with all of the terms associated with the post. The `taxonomy-images-list-the-terms` filter does this. Here's what it looks like in its simplest form:

```php
print apply_filters( 'taxonomy-images-list-the-terms', '' );
```

This filter accepts an optional third parameter that you can use to customize its output. It is an array which recognizes the following keys:

* __after__ _(string)_ - Text to append to the output. Default value is a closing unordered list tag.

* __after_image__ _(string)_ - Text to append to each image. Default value is a closing list-item tag.

* __before__ _(string)_ - Text to prepend to the output. Default value is an open unordered list tag with an class attribute of "taxonomy-images-the-terms".

* __before_image__ _(string)_ - Text to prepend to each image. Default value is an open list-item tag.

* __image_size__ _(string)_ - Any registered image size. Values will vary from installation to installation. Image sizes defined in core include: "thumbnail", "medium" and "large". "full" may also be used to get the unmodified image that was uploaded. Defaults to "thumbnail".

* __post_id__ _(int)_ - The post to retrieve terms from. Defaults to the ID property of the global `$post object`.

* __taxonomy__ _(string)_ - Name of a registered taxonomy to return terms from. Defaults to `category`.

Here's an example of what a fully customized version of this filter might look like:

```php
print apply_filters( 'taxonomy-images-list-the-terms', '', array(
	'before'       => '<div class="my-custom-class-name">',
	'after'        => '</div>',
	'before_image' => '<span>',
	'after_image'  => '</span>',
	'image_size'   => 'detail',
	'post_id'      => 1234,
	'taxonomy'     => 'post_tag',
) );
```

Working with all terms of a given taxonomy
------------------------------------------

You will want to use the `taxonomy-images-get-terms` filter. This filter is basically a wrapper for WordPress core function [get_terms()](http://codex.wordpress.org/Function_Reference/get_terms). It will return an array of enhanced term objects: each term object will have a custom property named `image_id` which is an integer representing the database ID of the image associated with the term. This filter can be used to create custom lists of terms. Here's what it's default useage looks like:

```php
$terms = apply_filters( 'taxonomy-images-get-terms', '' );
```

Here is what php's `print_r()` function may return:

```
Array
(
	[0] => stdClass Object
		(
			[term_id]          => 8
			[name]             => Pirate
			[slug]             => pirate
			[term_group]       => 0
			[term_taxonomy_id] => 8
			[taxonomy]         => category
			[description]      => Pirates live in the ocean and ride around on boats.
			[parent]           => 0
			[count]            => 1
			[image_id]         => 44
		)
)
```

As you can see, all of the goodness of `get_terms()` is there with an added bonus: the `image_id` parameter!

This filter recognizes an optional third parameter which is an array of arguments that can be used to modify its output:

* __cache_images__ _(bool)_ If this value is `true` all associated images will be queried and cached for later use in various template tags. If it is set to `false`, this query will be suppressed. Do not set this value to `false` unless you have a really good reason for doing so :) Default value is `true`.

* __having_images__ _(bool)_ If this value is `true` then only terms that have associated images will be returned. Setting it to `false` will return all terms. Default value is `true`.

* __taxonomy__ _(string)_ Name of a registered taxonomy to return terms from. Multiple taxonomies may be specified by separating each name by a comma. Defaults to `category`.

* __term_args__ _(array)_ Arguments to pass to [`get_terms()`](http://codex.wordpress.org/Function_Reference/get_terms) as the second parameter. Default value is an empty array.

Here's an example of a simple custom loop that you can use to display all term images:

```php
$terms = apply_filters( 'taxonomy-images-get-terms', '' );
if ( ! empty( $terms ) ) {
	print '<ul>';
	foreach ( (array) $terms as $term ) {
		print '<li><a href="' . esc_url( get_term_link( $term, $term->taxonomy ) ) . '">' . wp_get_attachment_image( $term->image_id, 'detail' ) . '</li>';
	}
	print '</ul>';
}
```

Support
-------

If you have questions about integrating this plugin into your site, please [add a new thread to the WordPress Support Forum](https://wordpress.org/tags/taxonomy-images?forum_id=10#postform). I try to answer these, but I may not always be able to. In the event that I cannot there may be someone else who can help.

Bugs, Suggestions
-----------------

Development of this plugin is hosted in a public repository on [Github](https://github.com/benhuson/Taxonomy-Images). If you find a bug in this plugin or have a suggestion to make it better, please [create a new issue](https://github.com/benhuson/Taxonomy-Images/issues/new)

Hook it up yo!
---------------

If you have fallen in love with this plugin and would not be able to sleep without helping out in some way, please see the following list of ways that you can _hook it up!_:

* __Rate it!__ - Use the star tool on the right-hand sidebar of the [plugin homepage](https://wordpress.org/plugins/taxonomy-images/).

* __Let me know if it works__ - Use the _Compatibility_ widget on the [plugin homepage](https://wordpress.org/plugins/taxonomy-images/) to let everyone know that the current version works with your version of WordPress.

* __Do you Twitter?__ Help promote by using this shortlink: [http://bit.ly/taxonomy-images](http://bit.ly/taxonomy-images)

* __Are you a writer?__ Help promote by writing an article on your website about this plugin.

Need More Taxonomy Plugins?
---------------------------

The original author of this plugin, Michael Fields, released a handful of plugins dealing with taxonomies. Please see his [WordPress.org profile](https://profiles.wordpress.org/mfields/) for more information.

Installation
------------

1. Download
1. Unzip the package and upload to your `/wp-content/plugins/` directory.
1. Log into WordPress and navigate to the "Plugins" panel.
1. Activate the plugin.
1. Click the "Taxonomy Images" link under the Settings section in the admin menu. There you can select the taxonomies that you would like to add image support for.

Upgrade Notice
--------------

### 0.9.5
Fix loading of admin stylesheet when editing terms in WordPress 4.5

### 0.9.4
Fix for taxonomy names that may contain characters other than lowercase and underscores (e.g. uppercase).

### 0.9.3
Fix post permissions error when using media modal.

### 0.9.2
Fix old and new media modal opening simultaneously in some circumstances.

### 0.9.1
Fixes media modal not opening on newly created terms.

### 0.9 
Fixes media modal for newer versions of WordPress. Tested up to WordPress 4.3.1 (requires 3.4+).

### 0.8
Major and minor bug fixes tested with WordPress 3.6.

### 0.7
Complete rewrite. Better everything. Many bug fixes.

Changelog
---------

View a list of all plugin changes in [CHANGELOG.md](https://github.com/benhuson/Taxonomy-Images/blob/master/CHANGELOG.md).
