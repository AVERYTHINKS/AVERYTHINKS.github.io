---
title: "Building AveryThinks: Jekyll, Chirpy and GitHub Pages"
date: 2026-09-02 16:00:00 +0300
categories: [Blog, Setup]
tags: [jekyll, github-pages, chirpy, linux, git]
---

So it's time for my first post here. I decided to go with the setup of the blog... so original. Hope it helps anyone in need. :)

**AveryThinks** is built with [Jekyll](https://jekyllrb.com/), hosted with [GitHub Pages](https://pages.github.com/), uses the [Chirpy](https://chirpy.cotes.page/) theme, and the development environment is ran locally on **Linux Mint**.

## Creating the Repository

I started with the official Chirpy getting-started guide - <https://chirpy.cotes.page/posts/getting-started/>. From the **Chirpy Starter** repository on GitHub, I clicked:

**Use this template → Create a new repository**

Then:

1. Entered a repository name. - **averythinks.github.io**
2. Added a short **description**.
3. Selected the desired repository visibility.
4. Created the repository from the template.

This gave me my own repository containing the Chirpy Starter structure, ready to be cloned and customized locally.

---

## Installing Jekyll on Linux Mint

For the local Jekyll environment, I followed the official Ubuntu installation instructions - <https://jekyllrb.com/docs/installation/ubuntu/>

First, I installed Ruby and the required build packages and after that I've configured the Ruby gems to be installed. (I chose home directory in for this case).

```bash
sudo apt-get install ruby-full build-essential zlib1g-dev
echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc
echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc
echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

Then I installed Jekyll and Bundler:

```bash
gem install jekyll bundler
```

At this point the local machine had the basic tools required to build and run the blog.

---

## Setting Up GitHub SSH Authentication

Rather than entering GitHub credentials every time I interact with the repository, I decided I will stick to SSH authentication.

If you are not sure that your SSH keys exist, you can display it with the following command:

```bash
ls -la ~/.ssh
```

After there was no result in my case, I generated an Ed25519 SSH key, start the SSH agent and finally added the key.

```bash
ssh-keygen -t ed25519 -C "github-email"
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```

### Adding the SSH Key to GitHub

I copied the output of the **public key** (whole output):

```bash
cat ~/.ssh/id_ed25519.pub
```

Went to:

**GitHub → Settings → SSH and GPG keys → New SSH key**

Then:

1. I've added a descriptive title for the computer.
2. Selected **Authentication Key**.
3. Pasted the public SSH key.
4. Added the key to my GitHub account.

Only the **public key** is uploaded to GitHub.

The `private key` remains on the local machine and should not be shared!

---

## Cloning the Chirpy Repository

With SSH configured, I've performed couple of steps, first cloned my new repository, then entered the directory and checked the git status to see if Git recognized the repository correctly. Finally we verify if the configuration is for remote.

```bash
git clone git@github.com:AVERYTHINKS/AVERYTHINKS.github.io.git
cd averythinks
git status
git remote -v
```
---

## Installing the Chirpy Dependencies and Running the Blog Locally

Inside the repository, I installed the dependencies defined by the project with the following command:

```bash
bundle install
```

Before modifying the site, I tested the default installation locally where Jekyll should start a local development server on the **http://127.0.0.1:4000/** address. Now the server is running locally and you can access it to check the current state of the web app, or cancel it wih **CTRL + C**.

```bash
bundle exec jekyll serve
```

This provides a useful workflow where changes can be tested locally before publishing them to GitHub.

---

## Configuring Chirpy

Most of the main Chirpy configuration is stored in **_config.yaml** and you can open it with nano or any other text editor and configure things such as the site title, description, GitHub username and site URL.

```bash
nano _config.yaml
```

After making changes, I've started Jekyll again:

```bash
bundle exec jekyll serve
```

This allowed me to verify the configuration locally before committing anything to git.

---

## Renaming the Repository for GitHub Pages

Initially, the repository had a normal project name - **averythinks**


I wanted the blog to be served directly from **https://averythinks.github.io/**

For a GitHub Pages user site, I therefore renamed the repository to:

```text
AVERYTHINKS.github.io
```

In GitHub, I opened **Repository → Settings → General** and changed the **Repository name** to:

```text
AVERYTHINKS.github.io
```

After renaming the repository on GitHub, my local Git configuration still referenced the original repository name. So I updated the `origin` remote, then verified it, and tested the communication with GitHub:

```bash
git remote set-url origin git@github.com:AVERYTHINKS/AVERYTHINKS.github.io.git
git remote -v
git fetch
```

The local repository was now connected to **git@github.com:AVERYTHINKS/AVERYTHINKS.github.io.git**

---

## Keeping My Git Identity Private

When I attempted my first commit, it required an author name and email address. After a little research I found that Git stores these values inside commit metadata, which means they can become visible in a public repository. Since I wanted to keep some separation between this blog and my personal information, I decided not to use my personal email address. After that I was led to the private `noreply` email address that GitHub provides specifically for this purpose.

To do that I've opened: **GitHub → Profile picture → Settings → Emails** and enabled **Keep my email addresses private**

GitHub provides an address similar to:

```text
ID+USERNAME@users.noreply.github.com
```

I then configured Git with the public identity I want to use for this project:

```bash
git config --global user.name "AVERYTHINKS"
git config --global user.email "INSERT_THE_NOREPLY_MAIL_HERE"
```
To verify the configuration I used:

```bash
git config --global user.name
git config --global user.email
```

The result is that commits can still be associated with my GitHub account without exposing my personal email address.

There is also another useful privacy option under - **GitHub → Settings → Emails** called **Block command line pushes that expose my email** which can provide an additional safeguard against accidentally publishing a private email address through Git commit metadata.

---

## My First Commit

After configuring Git, I added the modified Chirpy configuration, create the commit and push to git with the following commands:

```bash
git add _config.yaml
git commit -m "Configure AVERYTHINKS blog"
git push
```

The push successfully updated the `main` branch of the repository.

---

## Enabling GitHub Pages

The next step was enabling deployment through GitHub Pages. To do that in the repository I opened **Settings → Pages** right under **Build and deployment** and configured the Source to be - **GitHub Actions**. Chirpy Starter already includes a GitHub Actions workflow for building and deploying the Jekyll site.

After the push, I opened **Repository → Actions** and checked the deployment workflow. The workflow completed successfully within few seconds and obtained the green status icon.

Once deployment finished, the blog became available at `https://averythinks.github.io/`


VOILA! Great success!

---

## Creating the First Post

So Chirpy stores blog posts inside the **_posts** directory. Jekyll post filenames follow this general structure - **YYYY-MM-DD-post-name.md**. So I created the first post. 


```bash
nano _posts/2026-09-02-first-post.md
bundle exec jekyll serve
```
And, appropriately enough, that first post became the article you're reading now. Before publishing it, I tested it locally again and opened it - **http://127.0.0.1:4000/**

---

## Why Document This?

Didn't found the process itself all stiched together and decided why not provide it to others. This article was generated mostl by gpt under my instructions and redacted heavily by me. Would rather not not use it in future, except for translating content from Bulgarian. 

**actually writing.**
