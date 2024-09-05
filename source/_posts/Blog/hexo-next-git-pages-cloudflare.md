---
title: How I created my Hexo blog
categories:
  - Blog
tags:
  - Blog
toc: true
---

---
How to create a blog with `hexo` framework, `NexT` theme, version control and host with GitHub, and manage domain with Cloudflare.

---
<!-- more -->

<br>

## Prerequisites
`vscode`, `node`, `git`

### 1. On Linux

#### `Git`
```bash
sudo apt install -y git gh
```
> Go in your GitHub account > Developer settings > Personal access tokens > Tokens (classic) > Generate new token
```bash git auth
git config --global user.email "your_email@example.com"
git config --global user.name "your_username"

git config --global credential.helper store
# replace <your-token> with your actual token
echo "https://<your-token>@github.com" > ~/.git-credentials
```

> Create a local repository
```bash
mkdir -p ~/GitHub/repo_name
cd ~/GitHub/repo_name
git init

# add files (next step can be done at the end)
# if using vscode, the application allows you to update the repo in GUI, 
# so the following git steps will not be necessary
git add .
git commit -m "Initial commit"

# My username is ChronosPK
# my repo name is chronospk.github.io (this is the domain format for github pages)
git remote add origin https://github.com/ChronosPK/chronospk.github.io.git

git push -u origin master
```

<br>

#### `VS Code`
```bash Install vscode
sudo apt install -y code
```

<br>

#### `Node.js`
```bash Install node
sudo apt install -y nodejs npm 
node -v
npm -v
```

<br>

### 2. On Windows

#### `Git`
```batch
REM https://git-scm.com/download/win
winget install --id Git.Git -e --source winget 
```

<br>

#### `VS Code`
```bash
https://code.visualstudio.com/download
```

<br>

#### `Node`
```bash
https://phoenixnap.com/kb/install-node-js-npm-on-windows
```

<br>

## Install `Hexo`
```bash
npm install -g hexo-cli
hexo -v
```
```bash
npm install hexo-theme-next
rm -rf node_modules package*
hexo config theme next
hexo generate
hexo server --debug --port 9000 # test locally
```

<br>

## Important files
```bash
_config.yml
_config.next.yml
.github/workflows/hexo-deploy.yml # or whatever name
CNAME # to tell GitHub pages you have a custom domain (DNS config needed)
```

<br>

## Deploy
```bash
hexo generate 
hexo server --debug --port 9000 # test locally
```
```bash
hexo deploy # the repo needs to be public
# this will run the github workflow action, which needs to give a running status
```

<br>

## Configure DNS
I used Cloudflare, so I went to the DNS dashboard and add the following DNS record:
- type: `A`
- name: `@`
- IPv4: `192.30.252.154` (GitHub)
- Proxy status: `Proxied`

Wait a minute and you should see your website running.
If you go to your GitHub page, you should be redirected to the new domain (or subdomain) chosen.
For example,<a href="https://chronospk.github.io/" target="_"><code>chronospk.github.io</code></a> redirects to <a href="https://chronossec.site/" target="_"><code>chronossec.site</code></a>.

<br>

---
---