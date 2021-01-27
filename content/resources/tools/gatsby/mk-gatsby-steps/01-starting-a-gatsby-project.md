---
title: "Starting a Gatsby Project"
---

# STARTING UP A GATSBY PROJECT

There are many ways to get going. Here is a simple script we've been using in January 2021:

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

- it should be running. If you want, host it on Netlify by signing in with Github and connecting a new site to your new Github repo.
- also don't forget to add new metadata to `gatsby-config.js`:
  ```
  siteMetadata: {
      title: `MK Gatsby`,
      description: `Another daily Gatsby project.`,
      author: `mk`,
    },
  ```
