# SHOW YOUR WORK #
work in progress for 20201219.

## MAIN PROJECTS
experimenting with a new system. We need one of these to be "public" and one to be "private" and home-facing. But how do we determine which is which? where they go? how to keep this straight?

polish off linkedInLearning gatsby tutorial.todo
* plugins and transformations
	* 
* data
* creating pages dynamically
* going live
	* gatsby-plugin-manifest for progressive web app functionality

airtable with gatsby.todo
* create airtable
* learn graphql basics
* connect to airtable
* create page template for resources
* styling

maybe.todo
* styling
* react with graphiql
* setup express server?
* create bok schemas in GraphQL SDL?
* automate the generation of this file with a startup script
* try to pull posts from Slack to populate a `show-your-work-YYYYMMDD.md` doc that gets sent to user?
* when creating an image record in Airtable, add the image as an attachment
* 


### STATIC HTML FILES IN GATSBY

Looked a bit at the issues reported [here](https://github.com/gatsbyjs/gatsby/issues/13072) and figured out that the thing to do for now is to copy all the files into a `/static` folder at the root of the project, then `gatsby build` and `gatsby develop` will let you preview this on your machine. We'll need to watch out for naming collisions, so maybe a prefix for all the static stuff we bring in this way would be appropriate?


### PROJECT: SHOW YOUR WORK
a concept for the Spring: create a markdown workbook folder in the show, then make theShow a gatsby site?

### GATSBY NOTES
* plugins installed
	* styled components
	* [gatsby-source-filesystem](https://www.gatsbyjs.com/plugins/gatsby-source-filesystem/)
* need to work on the preprocessing external images tutorial at some stage, and to figure out the protocol for referencing images in markdown files
* 

## LINKS

### TODAY'S LINKS

* [great article on pagination relevant to GraphQL](https://www.apollographql.com/blog/understanding-pagination-rest-graphql-and-relay-b10f835549e7/#.lor7ia8hk)
* [article on GraphQL's SDL => Schema Definition Language](https://www.prisma.io/blog/graphql-sdl-schema-definition-language-6755bcb9ce51)
* [video 2 of the GraphQL video series](https://www.howtographql.com/basics/1-graphql-is-the-better-rest/)
* [Pinterest Developer guidelines](https://policy.pinterest.com/en/developer-guidelines)
* [Zapier integration for sending pins to Airtable](https://zapier.com/apps/airtable/integrations/pinterest/198655/create-airtable-records-for-new-pinterest-pins)
* [REST vs SOAP infographic](https://nordicapis.com/rest-vs-soap-nordic-apis-infographic-comparison/)
* [Gatsby Conceptual Guides](https://www.gatsbyjs.com/docs/conceptual/)
* [Sitepoint article on realtime data pagination](https://www.sitepoint.com/paginating-real-time-data-cursor-based-pagination/)
* [Markdown with Visual Studio Code](https://code.visualstudio.com/docs/languages/markdown)
* [Gatsby Airtable Listing Starter](https://www.gatsbyjs.com/starters/wkocjan/gatsby-airtable-listing)
* [Advanced GraphQL Usage in Gatsby Websites](https://www.smashingmagazine.com/2020/09/advanced-graphql-usage-gatsby-websites/)
* [Gatsby Plugin: Styled Components](https://www.gatsbyjs.com/plugins/gatsby-plugin-styled-components/)
* [react bootstrap rounded rectangles in Gatsby](https://www.youtube.com/watch?v=IQLBK9fwvEI)
* [preprocessing external images in Gatsby](https://www.gatsbyjs.com/docs/how-to/images-and-media/preprocessing-external-images/)
* [using blackmagic capture devices as webcams](https://www.youtube.com/watch?v=iyNSr1Xu5WI)
* [documentation on template literals and tagged templates](https://exploringjs.com/impatient-js/ch_template-literals.html) . . . came across this while working on GraphQL and Styled Components in Gatsby
* [and another link on tagged template literals](https://www.freecodecamp.org/news/es6-tagged-template-literals-48a70ef3ed4d/)
* 


### LINKS FOR REFERENCE
* [the show repo](https://github.com/mkuzmick/the-show)


## NOTES

working on using Visual Studio Code for the Gatsby work, and it is not so bad actually. It's time to learn more cross-platform and full-on Windows stuff this year, so we can't simply avoid this because it's Microsoft.



