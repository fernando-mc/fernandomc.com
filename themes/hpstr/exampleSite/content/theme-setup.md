---
type: page
title: Theme Setup
description: "Instructions on how to install and customize the modern Jekyll theme HPSTR."
image:
  feature: /images/abstract-11.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
share: true
---

This is a port of the **HPSTR** theme to [Hugo](https://gohugo.io). This theme is perfect for bloggers.

## Get the theme
With Git installed, run the following commands inside the Hugo site folder. If Hugo has not yet been installed, read the setup guide [here](https://gohugo.io/overview/installing/).

```
$ mkdir themes
$ cd themes
$ git clone https://github.com/dldx/hpstr-hugo-theme.git hpstr
```

You can get a zip of the latest version of the theme from the [home page](https://github.com/dldx/hpstr-hugo-theme) and extract it to the themes folder.

## Setup
Next, you need to configure your site and add some content. We provide a complete example site under the folder `exampleSite`. All you need to do is copy the contents of that folder into the main hugo site folder so that it looks something like this:
```
hugo-website
├── config.toml
├── content
│   └── posts
├── data
├── static
│   ├── assets
│   │   ├── css
│   │   ├── fonts
│   │   └── js
│   │       ├── plugins
│   │       └── vendor
│   └── images
└── themes
    └── hpstr
        ├── archetypes
        ├── layouts
        │   ├── _default
        │   └── partials
        └── static
            ├── css
            │   └── _sass
            │       └── vendor
            │           ├── font-awesome
            │           └── magnific-popup
            ├── fonts
            └── js
                ├── plugins
                └── vendor

```

This should be enough to get a working website!

---

## Running Hugo

To serve the site while you write posts, simply run `hugo server` in the base folder. You can now edit and make new posts, which will show up instantly. To render a permanent site, run `hugo` on its own.

---

## Configuration

### Disqus Comments

Create a [Disqus](http://disqus.com) account and change `disqusShortname` in `config.toml` to the Disqus *shortname* you just setup. By default comments appear on all post and pages if you assigned a shortname. Note that comments won't generally load when you are running the server locally. To disable commenting on a post or page, add the following to its YAML Front Matter:

```yaml
comments: false
```

### Social Share Links

To disable Facebook, Twitter, and Google+ share links on a post or page, add the following to its front matter:

```yaml
share: false
```

### Owner/Author Information

Change your name, and avatar photo (200x200 pixels or larger), email, and social networking URLs in `config.toml`.

To add a copyright string to the footer, add `copyright = "(c) Blah"` to `config.toml`

### Google Analytics and Webmaster Tools

To add Google Analytics, you may add your tracking id to `config.toml`:
```
googleAnalytics = "UA-123-45"
```

### Navigation Links

To add additional links in the drop down menu edit `data/navigation.toml`. Use the following format to set the URL and title for as many links as you'd like. *External links will open in a new window.*

```
[[links]]
title = "Theme Setup"
url = "/theme-setup/"

[[links]]
title = "External Link"
url = "http://mademistakes.com"
```

---

## Adding New Content

Posts are stored in the `content` directory. By default, only content in the `content/posts` will show up in the `All Posts` section, however, you can link to other sections manually. For example, if you create a post at `gallery/photo1.md`, your post will appear both under the home page and under `/gallery`.

By the way, Hugo has a shortcut for creating new posts: `hugo new gallery/photo1.md`. See `hugo new --help` for more details.

---

### Feature Images

A good rule of thumb is to keep feature images nice and wide so you don't push the body text too far down. An image cropped around around 1024 x 256 pixels will keep file size down with an acceptable resolution for most devices.

To add a feature image to a post or page just include the filename in the front matter like so.

```yaml
image:
  feature: /images/feature-image-filename.jpg
  thumb: thumbnail-image.jpg #keep it square 200x200 px is good
```

If you want to apply attribution to a feature image use the following YAML front matter on posts or pages. Image credits appear directly below the feature image with a link back to the original source.

```yaml
image:
  feature: feature-image-filename.jpg
  credit: Michael Rose #name of the person or site you want to credit
  creditlink: http://mademistakes.com #url to their site or licensing
```

By default the `<div>` containing feature images is set to have a minimum height of 400px with CSS. Anything taller is hidden with an `overflow: hidden` declaration. You can customize the height of the homepage feature image and those appearing on posts/pages by modifying the following variables in `/static/css/_sass/_variables.scss`.

```scss
$feature-image-height: 400px; // min 150px recommended
$front-page-feature-image-height: 400px; // min 150px recommended
```

#### Post/Page Thumbnails for OG and Twitter Cards

Post and page thumbnails work the same way. These are used by [Open Graph](https://developers.facebook.com/docs/opengraph/) and [Twitter Cards](https://dev.twitter.com/docs/cards) meta tags found in `partials/twitter-og-cards.html`. If you don't assign a thumbnail the image you assigned to `params.author.avatar` in `config.toml` will be used.

Here's an example of what a tweet to your site could look like if you activate Twitter Cards and include all the metas in your post's YAML.

![Twitter Card summary large image screenshot](/images/twitter-card-summary-large-image.jpg)

Twitter cards make it possible to attach images and post summaries to Tweets that link to your content. Summary Card meta tags have been added to `head.html` to support this, you just need to [validate and apply your domain](https://dev.twitter.com/docs/cards) to turn it on.

### Videos

Video embeds are responsive and scale with the width of the main content block with the help of [FitVids](http://fitvidsjs.com/).


### Link Post Type

Link blog like a champ by adding `link: http://url-you-want-linked` to a post's YAML front matter. Arrow glyph links to the post's permalink and the the `post-title` links to the source URL. Here's an [example of a link post]({{< relref "posts/sample-link-post.md" >}}) if you need a visual.

---

## Further Customization

By editing values found in `static/css/_sass/variables.scss` you can fine tune the site's colors and typography. You will need a [sass processor](http://sass-lang.com) to render the files to css.

For example if you wanted a red background instead of white you'd change `$bodycolor: #fff;` to `$bodycolor: $cc0033;`.

*Note from dldx: I haven't changed the Gruntfile to work for hugo yet. If anyone would like to submit a PR, feel free! :) The following is just the original documentation for this theme*

To modify the site's JavaScript files I setup a Grunt build script to lint/concatenate/minify all scripts into `scripts.min.js`. [Install Node.js](http://nodejs.org/), then [install Grunt](http://gruntjs.com/getting-started), and then finally install the dependencies for the theme contained in `package.json`:


```bash
npm install
```

From the theme's root, use `grunt` to concatenate JavaScript files and optimize `.jpg`, `.png` and `.svg` files in the `images/` folder.

You can also use `grunt dev` in combination with `bundle exec jekyll serve` to watch for updates in JS files that Grunt will then automatically re-build as you write your code, which will in turn auto-generate your Jekyll site when developing locally.

For more information on how to configure Hugo or content, check out the [Hugo documentation](https://gohugo.io/overview/introduction/).

---

## Questions?

Having a problem getting something to work or want to know why I setup something in a certain way? You can contact me on [github](https://github.com/dldx) or [file a GitHub Issue](https://github.com/dldx/hpstr-hugo-theme/issues/new). And if you make something cool with this theme feel free to let me know.

---

## License

This theme is free and open source software, distributed under the [MIT License](https://github.com/dldx/hpstr-hugo-theme/blob/master/LICENSE.md) version 2 or later. So feel free to to modify this theme to suit your needs.
