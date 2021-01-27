# CREATING MORE THAN ONE TYPE OF PAGE

## BOTH MD AND MDX

here's a concept for a `gatsby-node.js` that creates pages for both md and mdx files.

```
const path = require("path")
const { createFilePath } = require(`gatsby-source-filesystem`)

exports.onCreateNode = ({ node, getNode, actions }) => {
  const { createNodeField } = actions
  console.log(node.internal.type)
  if (node.internal.type === `MarkdownRemark`) {
    console.log(`found markdown node of type ${node.internal.type}`)
    const fileNode = getNode(node.parent)
    // console.log(JSON.stringify(fileNode, null, 4))
    console.log(`\n`, fileNode.relativePath)
    console.log(createFilePath({ node, getNode }))
    const slug = createFilePath({ node, getNode })
    const prefix = fileNode.sourceInstanceName || "content"
    createNodeField({
      node,
      name: `slug`,
      value: `/${prefix}${slug}`,
    })
    createNodeField({
      node,
      name: `pageType`,
      value: prefix,
    })
    createNodeField({
      node,
      name: `title`,
      value: node.frontmatter.title || fileNode.name || "no title",
    })
  }
  if (node.internal.type === `Mdx`) {
    console.log(`found mdx node of type ${node.internal.type}`)
    const fileNode = getNode(node.parent)
    console.log(`\n`, fileNode.relativePath)
    console.log(createFilePath({ node, getNode }))
    const slug = createFilePath({ node, getNode })
    const prefix = fileNode.sourceInstanceName || "content"
    createNodeField({
      node,
      name: `slug`,
      value: `/${prefix}${slug}`,
    })
    createNodeField({
      node,
      name: `pageType`,
      value: prefix,
    })
    createNodeField({
      node,
      name: `title`,
      value: node.frontmatter.title || fileNode.name || "no title",
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

  console.log(JSON.stringify(mdxResult, null, 4))
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

  const mdResult = await graphql(`
    query {
      allMarkdownRemark {
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



  console.log(JSON.stringify(mdResult, null, 4))
  if (mdResult.errors) {
    reporter.panicOnBuild('🚨  ERROR: Loading "createPages" query')
  }

  const theMd = mdResult.data.allMarkdownRemark.edges

  theMd.forEach(({ node }, index) => {
    console.log(`creating page for id ${node.id} with slug ${node.fields.slug} with initial h1 of ${(node.headings[0] ? node.headings[0].value : "no heading")}`)
    createPage({
      // This is the slug you created before
      // (or `node.frontmatter.slug`)
      path: node.fields.slug,
      // This component will wrap our MDX content
      component: path.resolve(`./src/components/templates/glossary-template-md.js`),
      // You can use the values in this context in
      // our page layout component
      context: { id: node.id, slug: node.fields.slug },
    })
  })




}
```

## CREATING MULTIPLE DIFFERENT TYPES OF MD AND MDX PAGES

coming soon.

## REFERENCE

link to discussion board [here](https://github.com/gatsbyjs/gatsby/issues/20159) has some useful suggestions.

You can achieve what you're looking for by grouping your API code into one function for each node API.

For ex:

```
const path = require(`path`)

exports.createPages = async ({ graphql, actions }) => {
  const { createPage } = actions
  const words = await graphql(`
    query {
      articles: allMdx(filter: { fileAbsolutePath: { regex: "/content/words/" } }) {
        edges {
          node {
            id
            fields {
              slug
            }
          }
        }
      }
    }
  `).then(res => res.data )

  words.articles.edges.forEach(({ node }) => {
		createPage({
			path: node.fields.slug,
			component: path.resolve(`./src/templates/article.js`),
			context: {
				slug: node.fields.slug,
				id: node.id,
			},
		})
  })

  const otherContent = await graphql(`
		query {
			articles: allMdx(
				filter: { fileAbsolutePath: { regex: "/content/some-other-content/" } }
			) {
				edges {
					node {
						id
						fields {
							slug
						}
					}
				}
			}
		}
  `).then(res => res.data)

  otherContent.articles.edges.forEach(({ node }) => {
		createPage({
			path: node.fields.slug,
			component: path.resolve(`./src/templates/other-template.js`),
			context: {
				slug: node.fields.slug,
				id: node.id,
			},
		})
  })
}
```

There are ways to make the code more concise, but for now, try building the query for each in Graphiql, and then console.log() the responses to figure out how to write your logic.

and for onCreateNode you can do something like this:

```
const { createFilePath } = require(`gatsby-source-filesystem`)

exports.onCreateNode = ({ node, actions, getNode }) => {
	const { createNodeField } = actions
	const value = createFilePath({ node, getNode })
	if (node.internal.type === `Mdx`) {
		createNodeField({
			name: `slug`,
			node,
			value: `/words${value}`,
		})
	} else if (node.internal.type === `SomethingElse`) {
		createNodeField({
			name: `slug`,
			node,
			value: `/music${value}`,
		})
	}
}
```

What you query and how you create your pages and node fields will be different depending on the requirements of your project, but the code above should give you a general idea of where to start. console.log and graphiql will be your best friends in figuring out how to write your logic. Let me know if you need any more help!
