---
title: "MK Gatsby HowTo for 20210116"
author: "Marlon Kuzmick"
date: 2021-01-16
---

# MK GATSBY FOR 20210116

## THE STEPS

to create this, starting 7:33

### START THE PROJECT AND REPO

- we now have the initial build and github repo going with a script like so

  ```
  #! /usr/bin/env zsh

  PROJECT_NAME=$1
  gatsby new $PROJECT_NAME $2
  DATA_STRING="{\"name\":\"$PROJECT_NAME\",\"private\":false}"
  curl -u "mkuzmick:$GITHUB_TOKEN" https://api.github.com/user/repos -d $DATA_STRING
  code $PROJECT_NAME
  cd $PROJECT_NAME
  git remote add origin "https://github.com/mkuzmick/$PROJECT_NAME.git"
  git branch -M main
  git push -u origin main
  open -a "Firefox Developer Edition" "http://localhost:8000"
  open -a "Firefox Developer Edition" "http://localhost:8000/___graphql"
  gatsby develop
  ```

- it should be running. If you want, host it on Netlify.
- also add new metadata to `gatsby-config.js`:
  ```
  siteMetadata: {
      title: `MK Gatsby`,
      description: `Another daily Gatsby project.`,
      author: `mk`,
    },
  ```

### INSTALL MARKDOWN AND MDX PLUGINS

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
- If you want something other than the simplest of mdx layouts, start by creating a simple `mdx-layout.js` file for now that you'll link to in the config file. Let's imagine that for this project we want two layouts, one for "Resources" that will live in `content/resources` and one for other `.mdx` files that live elsewhere in the content folder or in the `src/pages` folder. Create `src/components/layouts/mdx-layout-basic.js` and `src/components/layouts/mdx-layout-resource.js`

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

- then add the `gatsby-plugin-mdx` code to your `gatsby-config.js`, specifying the layouts for `resources` and for `default`:
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

### ADD STYLES WITH THEME-UI

- install [gatsby-plugin-theme-ui](https://www.gatsbyjs.com/plugins/gatsby-plugin-theme-ui/?=theme-ui) . one option =
  ```
  npm i theme-ui gatsby-plugin-theme-ui @theme-ui/presets
  ```
- then add it to `gatsby-config.js`

  ```
  plugins: ['gatsby-plugin-theme-ui'],
  ```

  or

  ```
  {
    resolve: `gatsby-plugin-theme-ui`,
    options: {
      preset: "@theme-ui/preset-tailwind",
    },
  },
  ```

- make the shadow dir
  ```
  mkdir src/gatsby-plugin-theme-ui
  ```
  and add index
  ```
  echo "export default {}" > src/gatsby-plugin-theme-ui/index.js
  ```
- we then need to add some elements to the theme, for instance:

  ```
  import baseTheme from "@theme-ui/preset-funk"
  import { merge } from "theme-ui"

  export default merge(baseTheme, {
      colors: {
          text: "#999",
          background: "#fff",
          primary: "#639",
          secondary: "#ff6347",
          headerBackground: "rgba(0, 0, 20, .7)"
        },
      fontWeights: {
          body: 400,
          heading: 900,
          bold: 700,
      },
      lineHeights: {
          body: 1.5,
          heading: 1.125,
      },
      fontSizes: [10, 14, 18, 24, 32, 48, 72, 96, 144],
      styles: {
          h1: {
            fontFamily: "heading",
            fontWeight: "heading",
            lineHeight: "heading",
            marginTop: 0,
            marginBottom: 3,
          //   fontSize: 2,
          //   color: "red"
          },
          a: {
            color: "primary",
            ":hover, :focus": {
              color: "secondary",
            },
          },
        },
  })
  ```

### ADD FONTS

- npm install @fontsource/prompt

```
npm install @fontsource/crimson-pro @fontsource/lato
```

- then add the import statements to your theme

```
import '@fontsource/crimson-pro/500.css';
import '@fontsource/crimson-pro/400.css';
import '@fontsource/lato/400.css';
```

- then add these fonts and reference them in your styles

```
fonts: {
    body: "Crimson Pro, sans-serif",
    heading: "Crimson Pro, serif",
    monospace: "Menlo, monospace",
  },
styles: {
    h1: {
      fontFamily: "heading",
      // etc.
    }
}
```

### MDX LAYOUT WITH COMPONENTS

- let's also add custom components to mdx following the pattern found [here](https://theme-ui.com/mdx-components).

```
/** @jsx jsx */
import { jsx, ThemeProvider } from 'theme-ui'
import theme from './theme'
import components from './components'
export default props => (
  <ThemeProvider theme={theme} components={components}>
    {props.children}
  </ThemeProvider>
)
```

- then a test file in `pages` called `mdx.mdx` that will be [here](http://localhost:8000/mdx)

  ```
  ---
  title: "MDX with Components 001"
  date: 2020-01-16
  ---

  # MY MDX WITH COMPONENTS
  content goes here
  ![Gatsby](../../content/images/gatsby.jpg)


  ```

### CREATE CONTENT FOLDERS AND PAGES FROM THEM

- copy in content folders from previous project. Today that's `cp -ar ../mk-gatsby-20210115/content content`
- then in the `gatsby-config.js` let's connect the `gatsby-source-filesystem` plugin to these new folders for mdx posts and images. We'll delete or modify the current `gatsby-source-filesystem` element, and instead have these two:
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
- don't forget to ensure that there's a correct path to the icon (either a new icon or the one gatsby provided, which has been moved):
  ```
  icon: `content/images/gatsby-icon.png`,
  ```
- add slug field (and slug values) to all md and mdx nodes in `gatsby-node.js`. (note: let's do this step by step in the tutorial to get a sense of how it all works)

  ```
  const { createFilePath } = require(`gatsby-source-filesystem`)

  exports.onCreateNode = ({ node, getNode, actions }) => {
      const { createNodeField } = actions
      console.log(node.internal.type)
      if (node.internal.type === `MarkdownRemark`) {
          console.log(`found markdown node of type ${node.internal.type}`)
          const fileNode = getNode(node.parent)
          console.log(`\n`, fileNode.relativePath)
          console.log(createFilePath({ node, getNode }))
          const slug = createFilePath({ node, getNode })
          const prefix = fileNode.sourceInstanceName ? fileNode.sourceInstanceName : "content"
          createNodeField({
              node,
              name: `slug`,
              value: `/${prefix}${slug}`,
          })
      }
      if (node.internal.type === `Mdx`) {
          console.log(`found mdx node of type ${node.internal.type}`)
          const fileNode = getNode(node.parent)
          console.log(`\n`, fileNode.relativePath)
          console.log(createFilePath({ node, getNode }))
          const slug = createFilePath({ node, getNode })
          const prefix = fileNode.sourceInstanceName ? fileNode.sourceInstanceName : "content"
          createNodeField({
              node,
              name: `slug`,
              value: `/${prefix}${slug}`,
          })
        }
    }
  ```

- then create pages for each node.
- create an initial test layout in `src/templates/test-layout.js`

  ```
  import React from "react"
  import Layout from "../components/layout"

  export default function TestLayout() {
    return (
      <Layout>
        <div>Hello resource layout</div>
      </Layout>
    )
  }
  ```

- follow one of the docs (like [this on one creating mdx pages](https://www.gatsbyjs.com/docs/mdx/programmatically-creating-pages/)) to create some pages, or just something like

```
exports.createPages = async ({ graphql, actions, reporter }) => {
  const { createPage } = actions
  const mdxResult = await graphql(`
    query {
      allMdx {
        edges {
          node {
            id
            frontmatter {
              title
            }
            fields {
              slug
              title
              pageType
            }
            headings(depth: h1) {
              value
            }
          }
        }
      }
    }
  `)

  if (mdxResult.errors) {
    reporter.panicOnBuild('🚨  ERROR: Loading "createPages" query')
  }

  const mdx = mdxResult.data.allMdx.edges
  mdx.forEach(({ node }, index) => {
    console.log(`creating page for id ${node.id} with slug ${node.fields.slug} with initial h1 of ${node.headings[0].value}`)
    createPage({
      // This is the slug you created before
      // (or `node.frontmatter.slug`)
      path: node.fields.slug,
      // This component will wrap our MDX content
      component: path.resolve(`./src/components/templates/resource-template.js`),
      // You can use the values in this context in
      // our page layout component
      context: { id: node.id },
    })
  })
}
```

---

## NEXT

### TOMORROW

### LIST OF THINGS TO CHECK OUT

- [creating multiple different types of pages in gatsby-node.js](https://swas.io/blog/using-multiple-queries-on-gatsbyjs-createpages-node-api/)
- add pages for content
- add theme-ui back in
- add base style (even just reset) to body
- create ideal mdx provider (with style and key components)
- create a grid, columns and stick-divs
- log all steps
- use useContext again while it's fresh (to pick colors of div elements or keyboard keys)
- connect to Airtable
- write a "color contrast" resource with that Gatsby shot and some waveform and vectorscope images
- make that the starter resource
- definitely try [gatsby-theme-style-guide](https://theme-ui.com/packages/gatsby-theme-style-guide/)
- are we grasping and using [typography.js](https://github.com/KyleAMathews/typography.js)?
- create title field, and go with
  - frontmatter.title
  - first H1
  - filename
- what happens on name collisions? (i.e. a `resources` folder in pages with identical file name? or if there is a page-1.js AND a page-1.mdx in `pages` . . . . or a `this-resource.md` and `this-resource.mdx` somewhere in the content folder?)
- create index (commented-out template in `src/pages/resource-index.js`)
- open links in new tabs. check out [gatsby-remark-external-links](https://www.gatsbyjs.com/plugins/gatsby-remark-external-links/)

## MODULAR BITS AND PIECES

- [creating more than one type of mdx page](/resources/gatsby/creating-more-than-one-type-of-mdx-page.md)

### INVENTORY OF PLACEHOLDER VIDEOS

- Malkovich AFC Championship teaser: https://www.youtube.com/watch?v=X4OCn_YWlpg

### SOME DYNAMIC ROUTES

for things that are "just in" and currently need to be grabbed from the server.

- [message board comment on dynamic routes that looks ok](https://stackoverflow.com/questions/55756994/how-to-create-dynamic-route-in-gatsby)
-

### NESTED THEME PROVIDERS

for different sorts of mdx pages (glossary vs llmdx vs shows vs resources, etc.)

- [some code in this basic doc on mdx layout components](https://theme-ui.com/guides/mdx-layout-components/) from the themeUI site.
- doc that has no examples, but is literally about [Nested Theme Providers](https://theme-ui.com/guides/nested-theme-providers/)
- [customizing MD components with just the MDX Provider](https://www.gatsbyjs.com/docs/how-to/routing/customizing-components/) — this could actually be a simpler way to go rather than the black boxes of themeUI?
- themeUI api docs including [ThemeProvider](https://theme-ui.com/api)
-

### ADVANCED STYLING STEPS

- nice walkthrough of [resetting and changing the global styles](https://scottspence.com/2020/02/06/globally-style-gatsby-styled-components/) in the default starter.
- article on [box sizing](https://css-tricks.com/box-sizing/#article-header-id-3) linked from that article
- [nice article on using mdx](https://www.digitalocean.com/community/tutorials/gatsbyjs-mdx-in-gatsby) that covers multiple layout options.

### WORKING WITH VIDEO

main Gatsby [doc on working with video in Gatsby here](https://www.gatsbyjs.com/docs/how-to/images-and-media/working-with-video/). But there will be many steps. Should create a custom component. Also think about pathways, video-enhanced or video-based quizzes, etc.

- [gatsby-remark-embed-video](https://www.gatsbyjs.com/plugins/gatsby-remark-embed-video/?=video) plugin
- add transformer ffmpeg, etc.

## LINKS

- [react hooks article](https://www.robinwieruch.de/react-state-usereducer-usestate-usecontext)
- [typescript in 5 minutes](https://www.typescriptlang.org/docs/handbook/typescript-in-5-minutes.html)
- nice long article on all this: https://jondot.medium.com/4-new-theme-based-react-ui-toolkits-and-why-its-going-to-change-how-you-think-72d276b1f6de
