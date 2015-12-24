# dvonthenen.com Blog

This GitHub repository serves as the backend for my Wordpress blog content. My Wordpress instance gets notifcations from Git push events which triggers an import of Markdown files located in this repo that get transformed into new blog posts.

There are a couple of cool things that occur as a result of GitHub/Wordpress integration:  
1. All my blog content is stored in GitHub and acts as the primary source "data". This in turns makes Wordpress just a display engine for my Markdown files.  
2. Since GitHub stores my content, if my webhosting provider goes out of business or they experience data loss, I will be unaffected and have access to all my blog articles. All I need to do is set up this configuration on a different provider and re-import my articles.  
3. I am being a good Open Source Citizen. All my work including my blog posts are open, version controlled, not published until the Pull Request posting the article is approved.  

![Dogs](https://github.com/dvonthenen/blog/blob/master/images/sydneyandizzy.jpg)

### How did I go about doing this?

You need this [Wordpress-GitHub-Plugin](https://wordpress.org/plugins/wp-github-sync/installation/) to be installed on your Wordpress server. You obviously need to have GitHub account with a repo that will contain your blog posts along with an OAuth token to receive GitHub push events.

If you want the Markdown support to translate Markdown into the Wordpress format, you need the [WP Markdown Plugin](https://wordpress.org/plugins/wp-markdown/installation/) to be installed.

Something that wasnt immediately obvious is that when you create new Markdown files in your GitHub repo, you need to have the top of your Markdown file contain some Wordpress metadata in order to assist with the translation to Wordpress. You can find this article [here](https://wordpress.org/plugins/wp-github-sync/faq/).
