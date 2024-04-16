+++
title = 'How to quickly use Hugo to create your own website'
date = 2024-03-05T11:01:24+08:00
draft = false
tags = ["hugo"]
+++

In the Internet age, having a personal website has become standard for almost every technology enthusiast. It not only showcases your technology stack, but also shares your thoughts and insights. This article will guide you how to use Hugo - a fast, flexible static website generator - to build your personal website. We will start with the basic concepts, gradually deepen into the core features of Hugo, and finally lead you to complete the construction of a personalized website.

## Introduction

Hugo is a static website generator written in Go language, which is famous for its fast build speed and flexible configuration. Compared with dynamic websites, static websites do not rely on databases, and pages are pre-generated on the server, which can provide faster loading speeds and higher security. Hugo not only helps you quickly generate a website, but also makes website design simple and elegant through rich themes and templates.

## Install Hugo

Installing Hugo is a simple and straightforward process. You can download the installation package for the corresponding platform through the package manager or directly from Hugo's official website.

### Windows

If you are using Windows, you can install it through Chocolatey:

```bash
choco install hugo -confirm
```

### macOS

For macOS users, you can use Homebrew to install:

```bash
brew install hugo
```

### Linux

Linux users can use package managers such as apt or yum. Here, apt is used as an example:

```bash
sudo apt-get install hugo
```

After the installation is complete, you can verify whether the installation was successful by running `hugo version`.

## Create your first website

After installing Hugo, you can start creating your first website immediately. Open a terminal or command prompt and run the following command:

```bash
hugo new site my-first-website
```

This line of command will create a new directory called `my-first-website` and initialize an empty Hugo website structure.

## Select a theme

The Hugo community provides many beautiful themes, and you can find them on the [Hugo Themes](https://themes.gohugo.io/) website. Choose a theme you like and clone it into the `themes` directory of your website. Take the Ananke theme as an example:

```bash
cd my-first-website
git submodule add https://github.com/theNewDynamic/gohugo-theme-ananke.git themes/ananke
```

Next, edit your website’s configuration file `config.toml` and set the theme to Ananke:

```toml
theme = "ananke"
```

## Add content

Now that your website has a theme, it’s time to add some content. Hugo manages content through Markdown files, so you can easily write and format your articles.

```bash
hugo new posts/my-first-post.md
```

This will create a new Markdown file in the `content/posts` directory. Open this file with your favorite text editor and you'll see that Hugo has filled in some basic prepend metadata for you. Below this, you can start writing your article content.

## Local preview website

Before publishing your site, you may want to preview it locally. Hugo provides a built-in server that allows you to see changes in real time:

```bash
hugo server -D
```

The `-D` parameter will cause Hugo to include the contents of the draft state. Open your browser and visit `http://localhost:1313`, and you will be able to see your website.

## Deploy your website

When you are satisfied with your website and ready to publish it to the Internet, you need to generate the static files and upload them to a web server or use a static website hosting service such as GitHub Pages or Netlify.

Run the following command to generate static content:

```bash
hugo -D
```

This will generate your website in the `public` directory. Just upload this directory to your server or use any service that supports static website hosting.

## Conclusion

Congratulations, you now have a personal website built with Hugo! Through the above steps, we not only introduced the basic concepts and installation process of Hugo, but also guided you step by step to create, design, add content, and finally deploy your website. Hugo's flexibility and speed provide individuals and businesses with a powerful tool to quickly build and manage websites. As you further explore Hugo, you'll be able to take advantage of its advanced features, such as custom themes, shortcodes, multi-language support, and more, to enrich your website's functionality and improve your user experience.

Keep exploring and make your website more personalized and feature-rich!
