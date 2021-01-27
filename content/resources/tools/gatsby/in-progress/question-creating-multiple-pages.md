# CREATING DIFFERENT PAGE TYPES QUESTION

should we grab `allMdx` and then filter in our js? or should we create multiple filtered queries? For the latter, see [this message board response](https://github.com/gatsbyjs/gatsby/issues/20159):

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