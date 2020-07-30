# EximiaCo's articles

Welcome to EximiaCo's articles repository. 

This repository is the place where all of EximiaCo's articles are born. Every content about business ([eximia.co](eximia.co)), technology ([eximiaco.tech](eximiaco.tech)) or Microsoft ([eximiaco.ms](eximiaco.ms)) that we publish in our websites appears first here.

**This is NOT a community project.** For information about permission to reproduce selections of the content, write to us at [contact@eximia.co](mailto:contact@eximia.co).

## Contributing

All new articles must be proposed as pull requests.

At the root of this repository, there is a directory, named proposals, specific to new articles.

In the proposed directory, there are three subdirectories, one for each site (`co` | `tech` | `ms`). Within each subdirectory, articles must be separated according to the language in which they are written (`pt` | `en` | `es`).

Every new article must be placed inside a proper directy, where the directory name must be the title of the article, replacing spaces with underscores. The article text needs to be written in a markdown-formatted file named `README.md`. All images used in the article body and the cover image should be placed at the same directory. 

For example, an article entitled "Hello World!", In English, for [EximiaCo.Tech](eximiaco.tech), should be submitted at `proposals/tech/en/hello_world/README.md`. 


## Definition of Done

All accepted pull requests are still open for editing by the content committee. In any case, the main message of each content must be preserved.

Published articles are moved from the proposals directory to a proper archive directory.

## Pull request

When you are done, pull the changes from the `master` branch on the main CodeCracker repo and integrate them.

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
