# GENERATING PAGES FROM CONTENT

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

To create more node fields (as we are after 20210116), try something like this (NOTE: we don't know how this affects performance yet)

```
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
  }
}
```

Then you can call use the `createPages` API to create a page for each node:
```
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
      // This is the slug you created before
      // (or `node.frontmatter.slug`)
      path: node.fields.slug,
      // This component will wrap our MDX content
      component: path.resolve(
        `./src/templates/test-layout.js`
      ),
      // You can use the values in this context in
      // our page layout component
      context: { id: node.id },
    })
  })
}
```

## MDX AND MD

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

## MULTIPLE PAGE TYPES WITH MULTIPLE LAYOUTS
