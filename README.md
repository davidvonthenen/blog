# dvonthenen.com Blog

This GitHub repository serves as the backend for my Wordpress blog content. My Wordpress instance gets notifcations from Git push events which triggers an import of Markdown files located in this repo that get transformed into new blog posts.

There are a couple of cool things that occur as a result of GitHub/Wordpress integration:  
1. All my blog content is stored in GitHub and acts as the primary source "data". This in turns makes Wordpress just a display engine for my Markdown files.  
2. Since GitHub stores my content, if my webhosting provider goes out of business or they experience data loss, I will be unaffected and have access to all my blog articles. All I need to do is set up this configuration on a different provider and re-import my articles.  
3. I am being a good Open Source Citizen. All my work including my blog posts are open, version controlled, not published until the Pull Request posting the article is approved.  

### How did I go about doing this?

You need this [Writing On GitHub](https://github.com/litefeel/writing-on-github) to be installed on your Wordpress server. You obviously need to have GitHub account with a repo that will contain your blog posts along with an OAuth token to receive GitHub push events.

If you want the Markdown support for Wordpress format, you have 2 options:
1. you need the [WP-Markdown](https://wordpress.org/plugins/wp-markdown/installation/) to be installed and you need to make some slight modifications to the source code. Open the `wp-content/plugins/wp-markdown/markdown-extra.php` file and replace (and only replace) `{0}` and `{1}` with `[0]` and `[1]` respectively. These changes are needed to handle the PHP7 support.
2. you can install the [Jetpack](https://wordpress.org/plugins/jetpack/) plugin. Then to configure the plugin, select "Setting", go to the "Writing" tab, and in the "Composing" section, enable the following option "Write posts or pages in plain-text Markdown syntax".

Both options will work, but the `WP-Markdown` option seems to much lighter weight than `Jetpack`. In contrast, the `Jetpack` option might handle all tags of markdown, but so far `WP-Markdown` seems to handle all of my use cases.

### Quirks

So in running this integration I ran into a number of interesting quirks that I am going to attempt to document here so that I (and others) don't run into this again.

#### Blocks of Code

For blocks of code, the best thing to use is the &lt;pre&gt; tags. They dont look that great in markdown, but when posted to a wordpress using this integration, they seem to work the best.
