# MK-GATSBY-20210124

Just another daily mk gatsby project. What's new today?

- improved MDX providers
- Airtable connections
- style
- scrolling and anchors in MDX

## THE STEPS

to make this project.

### STARTING UP A GATSBY PROJECT

There are many ways to get going. Here is a simple script we've been using in January 2021:

```
#! /usr/bin/env zsh

PROJECT_NAME=$1
cd "~/Development"
gatsby new $PROJECT_NAME $2
DATA_STRING="{\"name\":\"$PROJECT_NAME\",\"private\":false}"
curl -u "mkuzmick:$GITHUB_TOKEN" https://api.github.com/user/repos -d $DATA_STRING

cd $PROJECT_NAME
git remote add origin "https://github.com/mkuzmick/$PROJECT_NAME.git"
git branch -M main
git push -u origin main
open -a "Firefox Developer Edition" "http://localhost:8000"
open -a "Firefox Developer Edition" "http://localhost:8000/___graphql"
# gatsby develop

cp -aR ~/Development/mk-show/content ~/Development/$PROJECT_NAME/content

code $CODE_PUBLIC
code --add $PROJECT_NAME
```
we're now copying in the content folder from `mk-show` and opening up in the default public workspace
- it should be running. If you want, host it on Netlify by signing in with Github and connecting a new site to your new Github repo.
- also don't forget to add new metadata to `gatsby-config.js`:
  ```
  siteMetadata: {
      title: `MK Gatsby`,
      description: `Another daily Gatsby project.`,
      author: `mk`,
    },
  ```

### INSTALL MDX PLUGIN

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

- if you restart the dev server (ctrl+C then `gatsby develop` again), you should be able to now see any mdx pages you have in `src/pages`. If you don't have one, create a quick one
  ```
  echo "# THIS IS MDX\nand that means we can include React components." > src/pages/mdx.mdx
  ```
  Then restart the dev server AGAIN and you should be able to see it at [localhost:8000/mdx](http://localhost:8000/mdx).



#### ADDING TO GATSBY-CONFIG.JS

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
  {
    resolve: `gatsby-source-filesystem`,
    options: {
      name: `glossary`,
      path: `${__dirname}/content/glossary`,
    },
  },
  ```
- once you do this, you SHOULD be able to see all of your md or mdx files using the GraphiQL tool at [http://localhost:8000/\_\_\_graphql](http://localhost:8000/___graphql). If you are doing this for the first time, it pays to poke around a bit at this stage, creating some queries that help you try out various options for `allMarkdownRemark` and/or `allMdx` queries.
- one quick note: if you have copied in any MDX files that are importing from packages you haven't yet installed, you'll need to install them. For today's project
  ```
  npm i theme-ui react-compare-image
  ```

#### ADD STATIC FOLDER

In general, we will use GraphQL to grab gatsby-processed content from our content folder, but from time to time we may need to use static assets (an initial use case we encountered involved `react-compare-image`). Let's prefix all of the folders we put in static with an `_` so that we don't run into naming collisions.

```
mkdir static static/_images
curl -o static/_images/gatsby.jpg "https://i.guim.co.uk/img/media/cc5ff87a032ce6e4144e63a2a1cbe476dbc7cd5a/273_0_3253_1952/master/3253.jpg?width=620&quality=45&auto=format&fit=max&dpr=2&s=d8da5fd430d3983dc50543a44b3979d4"
```



## MDX ONLY

first create a slug for each node. Since we'll be grabbing mdx and md files from multiple sources (`resources`, `shows`, etc), we're going to hold on to that string and add it to the url. We do this in `gatsby-node.js` with something like

```
 const { createFilePath } = require(`gatsby-source-filesystem`)

 exports.onCreateNode = ({ node, getNode, actions }) => {
     const { createNodeField } = actions
     if (node.internal.type === `Mdx`) {
         console.log(`found mdx node of type ${node.internal.type}`)
         const fileNode = getNode(node.parent)
         const slugStem = createFilePath({ node, getNode })
         const slugRoot = fileNode.sourceInstanceName ? fileNode.sourceInstanceName : "content"
         createNodeField({
             node,
             name: `slug`,
             value: `/${slugRoot}${slugStem}`,
         })
       }
   }
```

You can now perform your `allMdx` query again in your [http://localhost:8000/\_\_\_graphql](http://localhost:8000/___graphql).

- then create pages for each node.
- create an initial test layout in `src/templates/test-layout.js` (note—this code is just to get us started—it will not work as is)
  ```
  mkdir src/templates
  touch src/templates/test-layout.js
  ```
then
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

The `gatsby-node.js` in this project currently looks like this

```
const { createFilePath } = require(`gatsby-source-filesystem`)
const path = require(`path`)

exports.onCreateNode = ({ node, getNode, actions }) => {
  const { createNodeField } = actions
  if (node.internal.type === `Mdx`) {
    console.log(`found mdx node of type ${node.internal.type}`)
    const fileNode = getNode(node.parent)
    const slugStem = createFilePath({ node, getNode })
    const slugRoot = fileNode.sourceInstanceName
      ? fileNode.sourceInstanceName
      : "content"
    createNodeField({
      node,
      name: `slug`,
      value: `/${slugRoot}${slugStem}`,
    })
    createNodeField({
      node,
      name: `pageType`,
      value: slugRoot,
    })
    createNodeField({
      node,
      name: `title`,
      value: node.frontmatter.title || fileNode.name || "no title",
    })
    createNodeField({
      node,
      name: `mainImage`,
      value: node.frontmatter.mainImage || "/gatsby.jpg",
    })
  }
}

exports.createPages = async ({ graphql, actions, reporter }) => {
  const { createPage } = actions
  const mdxResult = await graphql(`
    query {
      allMdx {
        edges {
          node {
            id
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
    console.log(
      `creating page for id ${node.id} with slug ${node.fields.slug} with initial h1 of ${(node.headings && node.headings[0]) ? node.headings[0].value : "no h1"}`
    )
    createPage({
      path: node.fields.slug,
      component: path.resolve(
        `./src/templates/test-layout.js`
      ),
      context: { id: node.id },
    })
  })
}

```

if we are in a hurry, we can sometimes just copy in yesterday's `src` folder. Be careful with this one.

```
rm -rf src
cp -aR ../mk-gatsby-20210120/src src
```

### ADD STYLES WITH THEME-UI

- install [gatsby-plugin-theme-ui](https://www.gatsbyjs.com/plugins/gatsby-plugin-theme-ui/?=theme-ui) . one option =
  ```
  npm i theme-ui gatsby-plugin-theme-ui @theme-ui/presets normalize.css
  ```
- then add it to `gatsby-config.js` (note: this will immediately apply the theme . . . even if you haven't created themeproviders in your code yet)

  ```
  plugins: ['gatsby-plugin-theme-ui'],
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

#### PRE STYLING

Change the width of the pre styling

```
pre: {
            overflow: "auto",
            width: "100%",
            },
        },
```


### ADD FONTS

- npm install @fontsource/prompt
  ```
  npm install @fontsource/crimson-pro @fontsource/lato @fontsource/ibm-plex-mono
  ```

* then add the import statements to your theme
  ```
  import '@fontsource/crimson-pro/500.css';
  import '@fontsource/crimson-pro/400.css';
  import '@fontsource/lato/400.css';
  ```
* then add these fonts and reference them in your styles

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

### AIRTABLE

loosely following [this tutorial](https://dev.to/sethu/how-to-build-a-website-using-gatsby-airtable-in-30-mins-42gm) but some of the syntax has changed.

```
npm i gatsby-source-airtable dotenv
```

then create a `.env` with the relevant stuff
```
touch .env.development
```

then in `gatsby-config.js` add the [gatsby-source-airtable](https://www.gatsbyjs.com/plugins/gatsby-source-airtable/) plugin

```
{
  resolve: "gatsby-source-airtable",
  options: {
    apiKey: process.env.AIRTABLE_API_KEY,
    tables: [
      {
        baseId: process.env.AIRTABLE_BASE_ID,
        tableName: "Pokémon",
      },
    ]
  }
}, 

```

and use `dotenv` to bring in environment variables if desired (note: if hosting on netlify you'll have to enter them there too.)

```
require("dotenv").config({
  path: `.env.${process.env.NODE_ENV}`,
})
```


```
import React from "react"
import { graphql } from "gatsby"

export default ({data}) => {
    const allAirtableData = data.allAirtable.edges;
    return (
        <div>
            {/* <pre>
                {JSON.stringify(allAirtableData, null, 4)}
            </pre> */}
            {
                allAirtableData.map(({node}) => (
                    <div>
                        <img alt={node.data.Name} src={node.data.Sprites[0].url} />
                        <h1>{node.data.Name}</h1>
                        <a href={`/pokemon/${node.recordId}`}>Click Here</a>
                    </div>
                ))
            }
        </div>
    )
}

export const query = graphql`
    query {
        allAirtable {
            edges {
              node {
                id
                data {
                  Name
                  APIURL
                  Height
                  Base_Experience
                  Weight
                  HP
                  Attack
                  Defense
                  Speed
                  Special_Defense
                  Special_Attack
                  Description
                  ID
                  Updated
                  TimeDiff
                  Sprites {
                    url
                  }
                }
                recordId
              }
            }
          }
    }
`

```

### CREATING AIRTABLE PAGES

now we add an additional query and then map the array of results to pages:

```
const { createFilePath } = require(`gatsby-source-filesystem`)
const path = require(`path`)

exports.onCreateNode = ({ node, getNode, actions }) => {
  const { createNodeField } = actions
  if (node.internal.type === `Mdx`) {
    console.log(`found mdx node of type ${node.internal.type}`)
    const fileNode = getNode(node.parent)
    const slugStem = createFilePath({ node, getNode })
    const slugRoot = fileNode.sourceInstanceName
      ? fileNode.sourceInstanceName
      : "content"
    createNodeField({
      node,
      name: `slug`,
      value: `/${slugRoot}${slugStem}`,
    })
    createNodeField({
      node,
      name: `pageType`,
      value: slugRoot,
    })
    createNodeField({
      node,
      name: `title`,
      value: node.frontmatter.title || fileNode.name || "no title",
    })
    createNodeField({
      node,
      name: `mainImage`,
      value: node.frontmatter.mainImage || "/gatsby.jpg",
    })
  }
}

exports.createPages = async ({ graphql, actions, reporter }) => {
  const { createPage } = actions
  const mdxResult = await graphql(`
    query {
      allMdx {
        edges {
          node {
            id
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
    console.log(
      `creating page for id ${node.id} with slug ${node.fields.slug} with initial h1 of ${(node.headings && node.headings[0]) ? node.headings[0].value : "no h1"}`
    )
    createPage({
      path: node.fields.slug,
      component: path.resolve(
        `./src/templates/test-layout-02.js`
      ),
      context: { id: node.id },
    })
  })

  const pokemonResult = await graphql(`
    query {
        allAirtable {
            edges {
                node {
                    id
                    recordId
                }
            }
        }
    }
  `)

  if (pokemonResult.errors) {
    reporter.panicOnBuild('🚨  ERROR: Loading "pokemon" query')
  }


  const pokemon = pokemonResult.data.allAirtable.edges
  pokemon.forEach(({ node }, index) => {
    console.log(
      `creating page for id ${node.id} with slug /pokemon/${node.recordId} `
    )
    console.log(JSON.stringify(node, null, 4))
    createPage({
      path: `/pokemon/${node.recordId}`,
      component: path.resolve(
        `./src/templates/pokemon-template.js`
      ),
      context: { recordId: node.recordId },
    })
  })
  
}

  



```


### TEMPLATES
* where templates go and why
* more on providers (and stock components for thems)

### NETLIFY STATUS

is this useful?

[![Netlify Status](https://api.netlify.com/api/v1/badges/2da80c54-9062-4114-b5e0-e25e3b5e40c5/deploy-status)](https://app.netlify.com/sites/mk-gatsby-20210121/deploys)

Let's see.


### THEMES

tutorial on [using multiple themes](https://www.gatsbyjs.com/tutorial/using-multiple-themes-together)

### MODELS

* [state of european tech](https://2017.stateofeuropeantech.com/chapter/introduction/) has cool navigation for documentation.

