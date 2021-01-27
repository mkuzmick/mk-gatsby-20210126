---
title: "Adding Content Folders and Pages"
author: "Learning Lab"
tags: ["markdown", "tools"]
mainImage: "/images/gatsby.jpg"
date: 2021-01-17
---

# ADDING CONTENT FOLDERS AND TEST PAGES

Frequently we will want to have some markdown (and sometimes mdx) content outside of the `src/pages` folder. Here are some steps and simple scripts to get this set up.

## CREATE OR COPY CONTENT FOLDER

- if you already have a content folder (like from yesterday's project, say), just copy it in, like so:

```
cp -aR ../mk-gatsby-20210116/content content`
```

- if you need to create the folders, here is a script that makes the content directory and addes some images and a basic page
  ```
  mkdir content content/resources content/shows content/llmdx content/glossary
  mv src/images content/images
  curl -o content/images/gatsby.jpg "https://i.guim.co.uk/img/media/cc5ff87a032ce6e4144e63a2a1cbe476dbc7cd5a/273_0_3253_1952/master/3253.jpg?width=620&quality=45&auto=format&fit=max&dpr=2&s=d8da5fd430d3983dc50543a44b3979d4"
  echo "# MDX RESOURCE\ncontent goes here\n\![Gatsby](../images/gatsby.jpg)" > content/resources/sample-resource.mdx
  echo "# MDX PAGE\ncontent goes here\n\![Gatsby](../../content/images/gatsby.jpg)" > src/pages/sample-mdx-page.mdx
  ```
- if you do move the `src/images` folder and the images in there, don't forget to ensure that there's a correct path to the icon image referenced in the `gatsby-config.js` file (either a new icon or the one gatsby provided, which has been moved):
  ```
  icon: `content/images/gatsby-icon.png`,
  ```

## ADDING TO GATSBY-CONFIG.JS

- for each folder we add that contains a specific type of content that we want to NAME as a specific type of content (like if we want `posts` to be different from `documents` and receive a different default layout), we should add a reference to the `gatsby-source-filesystem` plugin that targets that particular folder. For the developing LL content folders, we're going with
  ```
  {
    resolve: `gatsby-source-filesystem`,
    options: {
      name: `resources`,
      path: `${__dirname}/content/resources`,
    },
  },
  {
    resolve: `gatsby-source-filesystem`,
    options: {
      name: `shows`,
      path: `${__dirname}/content/shows`,
    },
  },
  {
    resolve: `gatsby-source-filesystem`,
    options: {
      name: `llmdx`,
      path: `${__dirname}/content/llmdx`,
    },
  },
  {
    resolve: `gatsby-source-filesystem`,
    options: {
      name: `images`,
      path: `${__dirname}/content/images`,
    },
  },
  ```
- once you do this, you SHOULD be able to see all of your md or mdx file using the GraphiQL tool at [http://localhost:8000/\_\_\_graphql](http://localhost:8000/___graphql). If you are doing this for the first time, it pays to poke around a bit at this stage, creating some queries that help you try out various options for `allMarkdownRemark` and/or `allMdx` queries.
- one quick note: if you have copied in any MDX files that are importing from packages you haven't yet installed, you'll need to install them. For today's project
  ```
  npm i theme-ui react-compare-image
  ```
