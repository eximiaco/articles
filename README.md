# EximiaCo's articles

Welcome to EximiaCo's articles repository. 

This repository is the place where all of EximiaCo's articles are born. Every content about business ([eximia.co](eximia.co)), technology ([eximiaco.tech](eximiaco.tech)) or Microsoft ([eximiaco.ms](eximiaco.ms)) that we publish in our websites appears first here.

**This is NOT a community project.** For information about permission to reproduce selections of the content, write to us at [contact@eximia.co](mailto:contact@eximia.co).

As a reference of good writing style, consult the [Mailchimp Styleguide](https://styleguide.mailchimp.com/). Also, there are a plenty of published articles in our web-sites.

## Contributing

All new articles must be proposed as pull requests.

At the root of this repository, there is a directory named `proposals`, specific to new articles.

In the `proposals` directory, there are three subdirectories, one for each site (`co` | `tech` | `ms`). Within each subdirectory, articles must be separated according to the language in which they are written (`pt` | `en` | `es`).

Every new article must be placed inside a proper directory, where the directory name must be the title of the article, replacing spaces and special characteres with underscores. The article text needs to be written in a markdown-formatted file named `README.md`. All images used in the article body and the cover image should be placed at the same directory. 

For example, an article entitled "Hello World!", In English, for [EximiaCo.Tech](eximiaco.tech), should be submitted at `proposals/tech/en/hello_world/README.md`. 


## Definition of Done

All accepted pull requests are still open for editing by the content committee. In any case, the main message of each content must be preserved.

Published articles are moved from the `proposals` directory to a proper archive directory.

## Pull request

When you are done, pull the changes from the `master` branch on the main articles repo and integrate them.

You have to do that in the command line:

````bash
# add the main repo with the `eximiaco-articles` name
git remote add eximiaco-articles https://github.com/eximiaco/articles.git
# checkout the master branch
git checkout master
# download the latest changes from the master repo
git pull eximiaco-articles master
# go back to your working branch
git checkout <youbranchname>
# integrate your changes
git merge master
# solve integration conflicts
````

If you know git well, you can rebase your changes instead of merging them. If not, it is ok to merge them.
When your changes are up to date with the `master` branch then you should push them to your Github repo and then you will be able to issue
a [pull request](https://help.github.com/articles/using-pull-requests/). Make your PR message clear, and the description should be the title of the article. If when you are creating the pull request on
Github it mentions that the PR cannot be merged because there are conflicts it means you forgot to integrate the `master` branch. Correct that push the changes to your personal repo. This will automatically update the PR. **The project maintainers should not have to resolve merge conflicts, you should.**

After your pull request is accepted you may delete your local branch if you want. Update your `master` branch so you can continue to contribute in the future. And thank you! :)

If your pull request is denied try to understand why. It is not uncommon that PRs are denied but after some discussing and fixing they are accepted. 
