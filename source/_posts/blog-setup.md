---
title: Building a Blog on GitHub Pages with Hexo and NexT
date: 2018-07-19 03:01:51
tags:
	- Ubuntu
	- Blog
	- Node.js
categories: Web
---

# Requirement and Installation
**Node.js**, **Git**, **Hexo** and **Next** are required. The installation guides or documents that I referred to are listed below. 

## Node.js 
> As an asynchronous event driven JavaScript runtime, Node is designed to build scalable network applications. 

Users can simply download and install Node.js from [the official website](https://nodejs.org/en/). However, I chose an alternative method and installed Node.js with [Node Version Manager (nvm)](https://github.com/creationix/nvm/blob/master/README.md) instead. 

<!-- more -->

### nvm Installation
```
$ curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash
```

### Node.js Installation
The terminal must be restarted before Node.js stable is installed. 
```
$ nvm install stable
```

The Node version can be checked with the following command. 
```
$ node -v
```

**Warning: Windows is not supported by nvm.**

## Git
I run Linux (Ubuntu) when I develop things for 2 reasons. On the one hand, it is much easier to use TensorFlow-GPU on Ubuntu than on Windows. On the other hand, the fastest GPUs among current Apple products are the Radeon Pro Vega series on the iMac Pro, whose price starts at $4,999, while my computer with an Intel 8700 CPU and a Gigabyte GeForce 1080 GPU only costs around $1,999 (though a little less powerful than the iMac Pro). 
```
$ sudo apt-get install git-core
```

**Tip: Windows users can simply download git [here](https://git-scm.com/download/win). Mac users can install git with [Homebrew](https://brew.sh/) with `brew install git`.**

## Hexo

### Installation
After Node.js and git have already been installed I followed [the documentation](https://hexo.io/docs/index.html) and installed Hexo. 
```
$ npm install -g hexo-cli
```

**Warning: Node.js and Git must be installed before Hexo.**

### Setup
After installation, I created my local folder /home/bowenfeng/Projects/alfredbowenfeng.github.io and continued to setup. 
```
$ hexo init ~/Projects/alfredbowenfeng.github.io/
$ cd ~/Projects/alfredbowenfeng.github.io/
$ npm install
```

## Next
Next is recommended to be cloned to local, because in the future it can be updated simply with a command `git pull`. The root directory of my local folder is /home/bowenfeng/Projects/alfredbowenfeng.github.io/
```
$ cd ~/Projects/alfredbowenfeng.github.io/
$ git clone https://github.com/iissnan/hexo-theme-next themes/next
```

# Configuration and Local Testing

## Local Testing
To do a local testing, Hexo server can be started with a simple command `hexo s` or `hexo server`. After the server starts running, I opened the browser and went to localhost:4000. Voila!
![Initial Landscape Theme](/images/blog-setup/initial-landscape-theme.png)

I opened the `_config.yml` file in the root directory with Sublime Text and did my initial configuration. 
`76 theme: next`

Let's take a look after restarting the Hexo server.  
![Initial Next Theme](/images/blog-setup/initial-next-theme.png)

## Basic Configuration
Basic configurations can be made by making changes to `_config.yml` file in the root directory. I made the following changes. 
` 6 title: Alfred Codes`
` 8 description: Bowen Feng's Fiddle-Faddle Blogs`
` 9 keywords: Alfred Bowen Blogs 冯博文 博文 博客`
`10 author: Alfred Bowen Feng`
`11 language: en`
`12 timezone: Asia/Shanghai`
`16 url: http://alfredbowenfeng.com`
`18 permalink: :month/:day/:year/:title/`
`65 date_format: MM-DD-YYYY`

Make the changes, save the file, and let's take a look after restarting the Hexo server. 
![Basic Configuration](/images/blog-setup/basic-configuration.png)

## Next Configuration
Next configurations can be made by making changes to the `/themes/next/_config.yml` file. I made the following changes. 
` 96 about: /about/ || user`
` 97 tags: /tags/ || tags`
` 98 categories: /categories/ || th`
`114 #scheme: Muse`
`117 scheme: Gemini`
`129 social:`
`130   GitHub: https://github.com/alfredbowenfeng || github`
`131   E-Mail: mailto:alfred.bowenfeng@gmail.com || envelope`
`157 avatar: /images/avatar.jpeg`

Besides setting the config file, creating three pages (about, tags and categories) is naturally necessary. 
```
$ hexo n page about
$ hexo n page tags
$ hexo n page categories
```

For each page, Hexo will create a folder in the `/source` directory. I opened the three markdown files in the three folders and edited them respectively. 

About
```
---
title: About
date: 2018-07-19 02:49:02
type: "about"
---
```

Tags
```
---
title: Tags
date: 2018-07-19 02:48:49
type: "tags"
---
```

Categories
```
---
title: Categories
date: 2018-07-19 02:48:57
type: "categories"
---
```

This time I do not have to restart the Hexo server because I just made some changes only towards Next, not Hexo. Let's take a look after refreshing the page.
![Next Configuration](/images/blog-setup/next-configuration.png)

## Advanced Configuration

### GitHub Corners
I refer to [tholman](http://tholman.com/github-corners/) to add a GitHub corner to my web pages. Add the code given right after `<div class="headband"></div>` in the `/themes/next/layout/_layout.swig` file. Also, do not forget to change the url to your own GitHub url. Let's take a look after refreshing the page. 
![Github Corner](/images/blog-setup/github-corner.png)

### Tags to Icon
At the bottom of every article, tags with the beginning of "#" are displayed. By making some changes to the `/themes/next/layout/_macro/post.swig` file, an icon instead of "#" will be displayed. 
`359 rel="tag"><i class="fa fa-tag"></i> {{ tag.name }}`

### Table Plugin
In order to present tables if needed in the future, I installed [hexo-tag-table-bootstrap](https://www.npmjs.com/package/hexo-tag-table-bootstrap). 
```
$ npm install hexo-tag-table-bootstrap --save
```

### Gitment
Enabling Gitment will add the comment section in every blog page. The configuration can be made by making changes to the `/themes/next/_config.yml` file. I made the following changes. 
`404   enable: true`
`409   language: en`
`410   github_user: alfredbowenfeng`
`411   github_repo: alfredbowenfeng.github.io`
Apart from those changes, I submitted a new [OAuth Apps application](https://github.com/settings/applications/new) on GitHub, and received a Client ID and a Client Secret. Write them into the config file as well. 
`412   client_id: xxx`
`413   client_secret: xxx`

Two errors occurred when I set up Gitment. 1. "Error: Not Found" is due to the wrong github_repo name; 2. "Error: Comments Not Initialized" can be solved by logging into GitHub after deployment. Later, I disabled Gitment because I had to initialize the comments on every page manually. 

### Others
There are a lot of other configurations one can make, most of which are white elephants. Those in need can refer to [NexT Website](http://theme-next.iissnan.com/theme-settings.html) for more information. 

# New Posting
To post a new article (other than hello-world), just simply continue type commands in the terminal. For example, I created my first post 'blog-intro'. 
```
$ hexo n blog-intro
```

Then, I opened the newly created `/source/_posts/blog-intro.md` and changed the header. 
```
---
title: An introduction to my Blog
date: 2018-07-19 02:44:43
tags:
	- Blog
	- Hexo
	- Next
categories: Web
---
```

The main body is inserted after the header. I finished my first article and let's take a look.
![First Article](/images/blog-setup/first-article.png)

# Deployment to GitHub
The prerequisite of the deployment is that the user has the ssh access to his/her GitHub repository. I have already generated and set the ssh key, but if those in need have not met the prerequisite, you can refer to this [guide](https://help.github.com/articles/connecting-to-github-with-ssh/).

Before deploying my website to GitHub Pages and push the repository to GitHub, the basic `_config.yml` file should be edited. 
`81   type: git`
`82   repo: git@github.com:alfredbowenfeng/alfredbowenfeng.github.io.git`
`83   branch: master`

To deploy, simply type the following commands in the Terminal. 
```
$ npm install hexo-deployer-git --save
$ hexo clean # remove current static files from the /public folder
$ hexo g # create new static files into the /public folder
$ hexo d # deploy the website to GitHub
```

In China Mainland, a Telecom network environment is not recommended for the deployment, because it usually takes hours to push the files, especially when you put lots of pictures into the `/themes/next/source/images` folder. Deploy in a network environment of Mobile or Unicom instead. 

All done! Please enjoy!