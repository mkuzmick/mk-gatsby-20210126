# STEPS TO MAKE THIS THING

this is just a list of all the steps I took to make this site (so that I don't forget the next time I have to do it).

## CREATING THE PROJECT AND THE REPO

First, start a Github Repo and a Gatsby Project and connect one to the other.

1. Install the Gatsby CLI with `brew install gatsby-cli`.
2. Create a new Gatsby projects with `gatsby new mk-show` (using whatever other name you like, of course). You can also use a different [Gatsby starter](https://www.gatsbyjs.com/starters/?) if you want. In fact, I'm already regretting not using a different one for this site.
3. `cd mk-show` and `gatsby develop` launches the development server and makes everything visible at [localhost:8000](http://localhost:8000/) (and the GraphQL data will be [here](http://localhost:8000/___graphql)).
4. connect to the Github repo with 
    ```
    git remote add origin https://github.com/mkuzmick/mk-show.git
    git branch -M main
    git push -u origin main
    ```


MAKING FIRST BASIC CHANGES TO THE STARTER

Next we'll just reset the starter, add our own site title and placeholder text, changing the folder structure, and tweaking the `gatsby-config.js` to target the new structure.

1. Change the `siteMetadata` in the `gatsby-config.js` file:

    ```
    siteMetadata: {
        title: `The Show`,
        description: `Show Your Work prototype that for now is mainly mk work in progress.`,
        author: `The Learning Lab`,
      },
    ```

    this should change the title you see in the big purple bar at the top of your page.

2. Also in `gatsby-config.js` we'll want to change the options for the `gatsby-plugin-manifest` plugin to match your own content.  you can add a link to your own icon by inserting the path in the options object you find under the `` resolve: `gatsby-plugin-manifest` `` line (we are going to use `/content/images/icon.png`). This icon will show up as the favicon in your browser tab when you change it.
3. Then update the `index.js` file with new text and appropriate links (deleting the link to `using-typescript` for instance). 
4. Then get rid of the `/src/images` folder and create a `/content` folder with `images` and other relevant folders within it (i.e. `/content/images` and `/content/shows`). 
5. Once there are a couple of images in there, change the `Image` component we're importing in `index.js` to match an image that exists. You need to do this in the GraphQL query, where the arguments in the `placeholderImage: file(relativePath: { eq: "bg.jpg" })` line need to match an image that exists (we've chosen `bg.jpg`).
6.  To get Gatsby to see the content in our `content` folders, we'll use `gatsby-source-filesystem` a couple of times in the `gatsby-config.js` file. Right now in the starter's config file you'll see the following
    ```
    {
      resolve: `gatsby-source-filesystem`,
      options: {
        name: `images`,
        path: `${__dirname}/src/images`,
      },
    },
    ```
    We want to duplicate that, and change each copy so that we have something like the following:
    ```
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
        name: `resourcemarkdown`,
        path: `${__dirname}/content/resources`,
      },
    },
    ```
7. Now you'll be able to access everything in `/content/images` and `/content/markdown` with GraphQL queries. To check it out, head over to [localhost:8000/___graphql](http://localhost:8000/___graphql) and play around in the GraphiQL interface. For more on GraphQL, check out [the docs](https://graphql.org/learn/) or [this video series](https://www.youtube.com/watch?v=ZNFY8auhtcI).
8. Now let's start accessing the content in the actual components. Let's start with the images. Head in to the `/src/components/image.js` file and change the query you find at the bottom to point to the relative path of one of YOUR images:
    ```
    const data = useStaticQuery(graphql`
      query {
        placeholderImage: file(relativePath: { eq: "LL.png" }) {
          childImageSharp {
            fluid(maxWidth: 300) {
              ...GatsbyImageSharpFluid
            }
          }
        }
      }
    `)
    ```
    This is confusing when you are first learning GraphQL—we'll try to build out our explanations of what's happening over time.


## READING THE MARKDOWN FILES

1. Now we'll put some markdown files `/content/shows`). Then, following the instructions [here](https://www.gatsbyjs.com/docs/how-to/routing/adding-markdown-pages/), we'll install the markdown transformer with `npm install gatsby-transformer-remark`, and add that to our list of plugins, i.e.
    ```
    plugins: [
      {
        resolve: `gatsby-source-filesystem`,
        options: {
          path: `${__dirname}/content/resources`,
          name: `resources`,
        },
      },
      `gatsby-transformer-remark`,
      // etc.
    ]
    ```
    Once you do this—and really anytime you make changes to the `config.js` file—you may have to stop the dev server (ctrl + C on Mac) and then restart it again with `gatsby develop`. Once you do this, you SHOULD see `allMarkdownRemark` as one of the options on your [localhost:8000/___graphql](http://localhost:8000/___graphql) page. You can poke around clicking boxes for a bit to see the sort of data you can pull together, or you can just paste the following query in to get a sense of things:
    ```
    query MarkdownExcerpts {
      allMarkdownRemark {
        edges {
          node {
            fileAbsolutePath
            excerpt
          }
        }
      }
    }
    ```


## ADDING METADATA FIELDS TO MARKDOWN POSTS

There are at least two ways we can add data fields to our posts.

1. The first and easiest way to add metadata to your markdown files is to add a YAML header that will be read as `frontmatter` when `gatsby-transformer-remark` handles your markdown nodes. For this project we are going to add `title`, `date`, `mainImage`, `author` and a list of `tools` to our YAML frontmatter for each post. We'll generate slugs from the file locations. 
    ```
    ---
    title: "20201226"
    author: "Marlon Kuzmick"
    mainImage: "https://upload.wikimedia.org/wikipedia/commons/9/91/Blacksmith_tools_2.jpg"
    date: 2020-12-26
    tools: [Gatsby, React, GraphQL, Markdown]
    
    ---
    ```
    If you now head in to [localhost:8000/___graphql](http://localhost:8000/___graphql) and query for frontmatter, you should see the values you enter there.
2. In many Gatsby + markdown tutorials the slug is declared in the YAML, but this feel brittle to us (not least because we are working with loads of authors of varying coding backgrounds), so let's just determine the slug based on where the markdown file is in the directory tree. As we create the markdown nodes, let's tack on a new field for `theSlug` that we populate with the relative path of the markdown file. In the `gatsby-node.js` file, add something like the following:
    ```
    const { createFilePath } = require("gatsby-source-filesystem")

    exports.onCreateNode = ({ node, getNode, actions }) => {
        const { createNodeField } = actions
        if (node.internal.type === 'MarkdownRemark') {
          const fileNode = getNode(node.parent)
          const slug = `/${fileNode.sourceInstanceName}${createFilePath({ node, getNode })}` 
          createNodeField({
            node,
            name: 'theSlug',
            value: slug,
          })
        }
      }
    ```
    Once again head over to [localhost:8000/___graphql](http://localhost:8000/___graphql) and you should find a value for `theSlug` in `fields`. Here's a query just in case you want to see all the metadata you've added:
    ```
    {
      allMarkdownRemark {
        edges {
          node {
            fields {
              theSlug
            }
            frontmatter {
              author
              date
              tools
              title
              mainImage
            }
          }
        }
      }
    }
    ```


## CREATING PAGES

Now that we have all that markdown, let's create pages for each and every file. We'll need to create a template, then create a page for each markdown node that uses this template.

1. to create the template, we'll create a new `templates` folder in our `src` directory, and we'll put a `show-template.js` file in it that looks something like this:



 by heading over to the `/gatsby-node.js` file and creating a new function
13. now the resource template

```
import React from "react"
import { graphql } from "gatsby"

export default function Template({
  data, // this prop will be injected by the GraphQL query below.
}) {
  const { markdownRemark } = data // data.markdownRemark holds your post data
  const { frontmatter, html } = markdownRemark
  return (
    <div className="blog-post-container">
      <div className="blog-post">
        <h1>{frontmatter.title}</h1>
        <h2>{frontmatter.date}</h2>
        <div
          className="blog-post-content"
          dangerouslySetInnerHTML={{ __html: html }}
        />
      </div>
    </div>
  )
}

export const pageQuery = graphql`
  query($slug: String!) {
    markdownRemark(fields: { theSlug: { eq: $slug } }) {
      html
      fields {
        theSlug
      }
      frontmatter {
        date(formatString: "MMMM DD, YYYY")
        author
        mainImage
        tools
        title
      }
    }
  }
`

```

14. now style the template
15. where to put markdown images?
16. now up to Netlify?

## NEXT

* adding extra pages
* styling
* interactions
* variables in page-level queries that get passed down to components
* do I need [gatsby-plugin-node-fields](https://www.gatsbyjs.com/plugins/gatsby-plugin-node-fields/) to operate on the node fields?

