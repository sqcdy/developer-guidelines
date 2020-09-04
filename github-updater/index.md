# GitHub Updater

We use the fabulous GitHub Updater plugin to mangage updates of themes and plugins that are housed in our own repositories, both private and public.

Please look through the documentation here: [https://github.com/afragen/github-updater/wiki](https://github.com/afragen/github-updater/wiki)

The plugin author is extremely awesome and usually very available on Slack. Try not to bug him for answers that are already documented on the Wiki, but it's a good idea to ask for an invite to the Slack channel (see wiki homepage).

## Setting Up a Theme or Plugin on the Server

### In the WP Dashboard:

Go to **Settings > GitHub Updater** and add the theme or plugin from there in the **Install Plugin** or **Install Theme** tabs. You will need the github URL and if it’s a private repository you will also need a GitHub API key with access to private repos. It's not required, but you should definitely also use a GitHub API key for public repos. If you don't you can run into the daily limits for non-authenticated requests.

### From the Command Line:

For adding items in bulk or from the command line, you can use wp plugin install-git which is a special GitHub Updater command, so it can only be run on an install with that plugin active. You still need the github token to install anything private and in order to avoid daily limits on public repos, same as installing from the GUI.

Examples:

```
# get secret API key on Square Candy server
GITHUB=`cat /usr/local/bin/github_token.txt`

# ... or you can use your own API key for any user that has access to the repo

# install a theme
wp theme install-git https://github.com/squarecandy/example --github --token=$GITHUB
wp theme activate example

# install a plugin
wp plugin install-git https://github.com/squarecandy/example --github --token=$GITHUB
wp plugin activate example
```

## Automatic Deployment via Webhooks

If you want to hook it up so that changes are immediately deployed to staging when you push to the develop branch, you can automate that using Webhooks:

* In WordPress go to Settings > GitHub Updater > Remote Management
* Grab the URL from the bottom of that screen. Example: `https://example.sqcdy.com/wp-json/github-updater/v1/update/?key=12345678c26df81cc52b11529987ce6b`
* Add the following parameters to the end of the url: `theme` (or `plugin`), `branch`, `override`.  
  Example: `https://example.sqcdy.com/wp-json/github-updater/v1/update/?key=12345678c26df81cc52b11529987ce6b&theme=exampletheme&branch=develop&override=true`
* Test the url by going to it directly in a browser. You should get a success JSON response with some additional details.
* Add that full URL to github.com under **[repo] > Settings > Webhooks**. For develop / staging just have it run on every push. If you don’t have access to edit Webhooks, let someone know and we can either increase your permissions or setup the webhook for you.

Once a site is live, you can also setup a Webhook that will deploy to production when a new release is made on the `master` branch. The instructions would be the same as above except you would replace the domain name with the production domain, branch with master. Then add a second webhook and be absoltuely sure to only run it on new tags or releases so it's not running with every push.

## Refresh Trick

If there is a plugin or theme installed in a staging environment and you don't have webhooks setup, you can use this trick to refresh it automatically.

* Under **Settings > GitHub Updater** make sure the site is set to allow branch switching
* Under the **Appearance > Themes > Example Theme** or **Plugins** switch branches with the "another version" link. Even if you're already on the branch you want, "switching" from `develop` to `develop` will immediately pull in your most recent changes.
