---
title: "Building AveryThinks: Jekyll, Chirpy and GitHub Pages"
date: 2026-09-02 16:00:00 +0300
categories: [Blog, Setup]
tags: [jekyll, github-pages, chirpy, linux, git]
---

So it's time for my first post here. I decided to go with the setup of the blog... so original. Hope it helps anyone in need. :)

**AveryThinks** is built with [Jekyll](https://jekyllrb.com/), hosted with [GitHub Pages](https://pages.github.com/), and uses the [Chirpy](https://chirpy.cotes.page/) theme.

I'm running the development environment locally on **Linux Mint**.

## Creating the Repository

I started with the official Chirpy getting-started guide:

<https://chirpy.cotes.page/posts/getting-started/>

From the **Chirpy Starter** repository on GitHub, I clicked:

**Use this template → Create a new repository**

I then:

1. Selected my GitHub account as the **Owner**.
2. Entered a repository name.
3. Added a short **description**.
4. Selected the desired repository visibility.
5. Created the repository from the template.

This gave me my own repository containing the Chirpy Starter structure, ready to clone and customize locally.

---

## Installing Jekyll on Linux Mint

For the local Jekyll environment, I followed the official Ubuntu installation instructions:

<https://jekyllrb.com/docs/installation/ubuntu/>

First, I installed Ruby and the required build packages:

```bash
sudo apt-get install ruby-full build-essential zlib1g-dev
```

Instead of installing Ruby gems system-wide, I configured them to be installed under my home directory:

```bash
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

Rather than entering GitHub credentials every time I interact with the repository, I configured SSH authentication.

First, I checked whether SSH keys already existed:

```bash
ls -la ~/.ssh
```

Then I generated an Ed25519 SSH key:

```bash
ssh-keygen -t ed25519 -C "github-email"
```

The value after `-C` is simply a comment associated with the key. There is no need to expose a personal email address when documenting the process.

Next, I started the SSH agent:

```bash
eval "$(ssh-agent -s)"
```

and added the private key:

```bash
ssh-add ~/.ssh/id_ed25519
```

To display the corresponding **public key**:

```bash
cat ~/.ssh/id_ed25519.pub
```

### Adding the SSH Key to GitHub

I copied the output of the `.pub` file and opened:

**GitHub → Profile picture → Settings → SSH and GPG keys → New SSH key**

I then:

1. Added a descriptive title for the computer.
2. Selected **Authentication Key**.
3. Pasted the public SSH key.
4. Added the key to my GitHub account.

Only the **public key** is uploaded to GitHub.

The private key:

```text
~/.ssh/id_ed25519
```

remains on the local machine and should not be shared.

---

## Cloning the Chirpy Repository

With SSH configured, I cloned my new repository:

```bash
git clone git@github.com:AVERYTHINKS/averythinks.git
```

Then entered the project directory:

```bash
cd averythinks
```

I checked that Git recognized the repository correctly:

```bash
git status
```

and verified the configured remote:

```bash
git remote -v
```

The remote was using SSH rather than HTTPS:

```text
git@github.com:AVERYTHINKS/averythinks.git
```

---

## Installing the Chirpy Dependencies

Inside the repository, I installed the dependencies defined by the project:

```bash
bundle install
```

Bundler reads the project's `Gemfile` and installs the Ruby gems required by Chirpy and Jekyll.

---

## Running the Blog Locally

Before modifying the site, I tested the default installation locally:

```bash
bundle exec jekyll serve
```

Jekyll started a local development server, available at:

```text
http://127.0.0.1:4000/
```

Opening that address in a browser showed the Chirpy site running directly from my laptop.

The local server can be stopped with:

```text
Ctrl+C
```

This provides a useful workflow where changes can be tested locally before publishing them to GitHub.

---

## Configuring Chirpy

Most of the main Chirpy configuration is stored in:

```text
_config.yml
```

I opened it with:

```bash
nano _config.yml
```

and configured things such as the site title, description, GitHub username and site URL.

After making changes, I started Jekyll again:

```bash
bundle exec jekyll serve
```

This allowed me to verify the configuration locally before committing anything.

The basic workflow quickly became:

```text
Edit
  ↓
Run locally
  ↓
Check
  ↓
Commit
  ↓
Push
```

---

## Renaming the Repository for GitHub Pages

Initially, the repository had a normal project name:

```text
averythinks
```

I wanted the blog to be served directly from:

```text
https://averythinks.github.io/
```

For a GitHub Pages user site, I therefore renamed the repository to:

```text
AVERYTHINKS.github.io
```

In GitHub, I opened:

**Repository → Settings → General**

and changed the **Repository name** to:

```text
AVERYTHINKS.github.io
```

After renaming the repository on GitHub, my local Git configuration still referenced the original repository name.

I updated the `origin` remote:

```bash
git remote set-url origin git@github.com:AVERYTHINKS/AVERYTHINKS.github.io.git
```

Then verified it:

```bash
git remote -v
```

and tested communication with GitHub:

```bash
git fetch
```

The local repository was now connected to:

```text
git@github.com:AVERYTHINKS/AVERYTHINKS.github.io.git
```

---

## Keeping My Git Identity Private

When I attempted my first commit, Git required an author name and email address.

Git stores these values inside commit metadata, which means they can become visible in a public repository.

Since I want to keep some separation between this blog and my personal information, I decided not to use my personal email address.

GitHub provides a private `noreply` email address specifically for this purpose.

I opened:

**GitHub → Profile picture → Settings → Emails**

and enabled:

**Keep my email addresses private**

GitHub provides an address similar to:

```text
ID+USERNAME@users.noreply.github.com
```

I then configured Git with the public identity I want to use for this project:

```bash
git config --global user.name "AVERYTHINKS"
git config --global user.email "GITHUB_NOREPLY_ADDRESS"
```

I verified the configuration:

```bash
git config --global user.name
git config --global user.email
```

The result is that commits can still be associated with my GitHub account without exposing my personal email address.

There is also another useful privacy option under:

**GitHub → Settings → Emails**

called:

**Block command line pushes that expose my email**

This can provide an additional safeguard against accidentally publishing a private email address through Git commit metadata.

---

## My First Commit

After configuring Git, I added the modified Chirpy configuration:

```bash
git add _config.yml
```

Then created the commit:

```bash
git commit -m "Configure AVERYTHINKS blog"
```

and pushed it to GitHub:

```bash
git push
```

The push successfully updated the `main` branch of the repository.

---

## Enabling GitHub Pages

The next step was enabling deployment through GitHub Pages.

In the repository I opened:

**Settings → Pages**

Under **Build and deployment**, I configured:

```text
Source → GitHub Actions
```

Chirpy Starter already includes a GitHub Actions workflow for building and deploying the Jekyll site.

After the push, I opened:

**Repository → Actions**

and checked the deployment workflow.

The workflow completed successfully with a green status.

Once deployment finished, the blog became available at:

```text
https://averythinks.github.io/
```

---

## Creating the First Post

Chirpy stores blog posts inside the:

```text
_posts
```

directory.

Jekyll post filenames follow this general structure:

```text
YYYY-MM-DD-post-name.md
```

So I created the first post:

```bash
nano _posts/2026-09-02-first-post.md
```

And, appropriately enough, that first post became the article you're reading now.

Before publishing it, I tested it locally again:

```bash
bundle exec jekyll serve
```

and opened:

```text
http://127.0.0.1:4000/
```

---

## Why Document This?

Didn't found the process itself all stiched together and decided why not provide it to others. This article was generated mostl by gpt under my instructions, but would rather not use it in future, except for translating content from Bulgarian. 

**actually writing.**
