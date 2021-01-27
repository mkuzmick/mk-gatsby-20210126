---
title: "Install MD and MDX Plugins"
author: "Marlon Kuzmick"
tools: ["Gatsby", "React"]
date: 2021-01-17
---

# INSTALL MD AND MDX PLUGINS

## JUST MDX

One option is to JUST install the mdx plugin and have it handle both `.md` and `.mdx` files.

- install the [gatsby-plugin-mdx](https://www.gatsbyjs.com/plugins/gatsby-plugin-mdx/?=mdx) and [gatsby-remark-images](https://www.gatsbyjs.com/plugins/gatsby-remark-images/?=remark%20images) plugins:
  ```
  npm install gatsby-remark-images gatsby-plugin-sharp gatsby-plugin-mdx @mdx-js/mdx @mdx-js/react
  ```

* in `gatsby-config.js`, add the `gatsby-remark-images` plugin and the `gatsby-plugin-mdx` (also adding the `gatsby-remark-images` plugin _within_ the `gatsby-plugin-mdx` object too). If you want the `mdx` plugin to handle `.md` file extensions too, add a line to options specifying that:
  ```
  `gatsby-remark-images`,
  {
    resolve: `gatsby-plugin-mdx`,
    options: {
      extensions: [`.mdx`, `.md`],
      // add default layouts once you have them
      // defaultLayouts: {
      //  resources: require.resolve("./src/components/layouts/mdx-layout-resource.js"),
    //  default: require.resolve("./src/components/layouts/mdx-layout-basic.js"),
    },
    gatsbyRemarkPlugins: [
      {
        resolve: `gatsby-remark-images`,
        options: {
          maxWidth: 960,
        },
      },
    ],
  },
  ```
  We'll get in to layouts in a later step, so it's fine to have them commented out for now. But one thing to note is that the keys other thand `default` there (`resources` in this case) come from the `gatsby-source-filesystem` chunks of your `gatsby-config.js`. See [here](https://www.gatsbyjs.com/plugins/gatsby-plugin-mdx/#default-layouts) in the Gatsby docs for more info.

## USING BOTH THE MD AND MDX PLUGINS

There are some quirks to the mdx mparser that can cause some difficulty for markdown files that the normal parser in `gatsby-transformer-remark` would render with no problems. If you want to be able to handle `.mdx` files with `gatsby-plugin-mdx` and `.md` files with `gatsby-transformer-remark`, follow these steps instead

- install all the markdown and mdx plugins: [gatsby-transformer-remark](https://www.gatsbyjs.com/plugins/gatsby-transformer-remark/?=gatsby%20transformer%20remark), [gatsby-remark-images](https://www.gatsbyjs.com/plugins/gatsby-remark-images/?=remark%20images) and [gatsby-plugin-mdx](https://www.gatsbyjs.com/plugins/gatsby-plugin-mdx/?=mdx):
  ```
  npm install gatsby-transformer-remark gatsby-remark-images gatsby-plugin-sharp gatsby-plugin-mdx @mdx-js/mdx @mdx-js/react
  ```
- in `gatsby-config.js`, hook the `gatsby-transformer-remark` plugin up and add the `gatsby-remark-images` plugin to it

  ```
  `gatsby-remark-images`,
  {
    resolve: `gatsby-transformer-remark`,
    options: {
      plugins: [
        {
          resolve: `gatsby-remark-images`,v
          options: {
            maxWidth: 960,
          },
        },
      ],
    },
  },
  ```

- then add the `gatsby-plugin-mdx` code to your `gatsby-config.js`, specifying the layouts if you have them (here we are specifying one layout for `resources` and another for everything else as the `default`):
  ```
  {
    resolve: `gatsby-plugin-mdx`,
      options: {
        defaultLayouts: {
          resources: require.resolve("./src/components/layouts/mdx-layout-resource.js"),
          default: require.resolve("./src/components/layouts/mdx-layout-basic.js"),
        },
      gatsbyRemarkPlugins: [
        {
          resolve: `gatsby-remark-images`,
          options: {
            maxWidth: 960,
          },
        },
      ],
    },
  },
  ```

## THE SIMPLEST OF MDX LAYOUTS

If you just want to have a placeholder layout for your `.mdx` files, try these steps.

- start by creating a simple `mdx-layout.js` file for now that you'll link to in the config file. Let's imagine that for this project we want two layouts, one for "Resources" that will live in `content/resources` and one for other `.mdx` files that live elsewhere in the content folder or in the `src/pages` folder. Create `src/components/layouts/mdx-layout-basic.js` and `src/components/layouts/mdx-layout-resource.js`

  ```
  mkdir src/components/layouts
  touch src/components/layouts/mdx-layout-basic.js src/components/layouts/mdx-layout-resource.js
  ```

  then add some simple code.

  ```
  import React from 'react';

  export default ({ children }) => (
      <div>
        <h1>My Resource Layout</h1>
        <div>{children}</div>
      </div>
    )
  ```
