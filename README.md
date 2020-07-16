# Gatsby Tech Talk
Notes, commands &amp; supporting files for my Gatsby tech talk / demo

### tech pre-reqs
- Node >=10.13.0
- Gatsby CLI
- Headless CMS (optional for data consumption)

### knowledge pre-reqs
- React
- GraphQL
- Docker (optional for headless CMS & geospatial engine)

### notes / snippets

show node/npm & gatsby CLI versions

```bash
$ cat <( node -v ) <( npm -v ) <( gatsby -v )
```

```bash
$ gatsby new yummy-cafe https://github.com/gatsbyjs/gatsby-starter-default
```

```bash
$ cd yummy-cafe gatsby && gatsby develop
```

show live browser running

show project file structure

mention routing

create new file in `/pages`

```javascript
import React from "react"

export default function About() {
  return <div>Yummy cafe is all about delicious, organic and yummy dishes and drinks. Yum.</div>
}
```

go back to browser and load http://localhost:8000/about

now show how the graphql tagging works

```javascript
import { Link, graphql } from "gatsby"

<p>Built on {data.site.buildTime}</p>

export const query = graphql`
  query MyQuery {
    site {
      buildTime
    }
  }
`
```

mention the varieties of graphql queries

1. dynamic page queries with tagging (shown above ^)
2. static queries using `<StaticQuery />` or `useStaticQuery`

show how dynamic data fetching would work

```javascript
import React, { useState, useEffect } from "react"
import { Link } from "gatsby"

import Layout from "../components/layout"
import SEO from "../components/seo"

const API_BASE = 'http://localhost:1337'

const SecondPage = () => {
  const [restuarants, setRestuarants] = useState([])
  useEffect(() => {
    fetch(`${API_BASE}/restaurants`)
      .then(response => response.json())
      .then(resultData => {
        setRestuarants(resultData)
      })
  }, [])

  return (
    <Layout>
      <SEO title="Page two" />
      <h1>Hi from the second page</h1>
      <p>Welcome to page 2</p>
      {restuarants.map((r, i) => {
        return (
          <div key={i}>
            <h1>{r.name}</h1>
            <img alt="cover example" src={API_BASE + r.cover_photo.url} />
          </div>
        )
      })}
      <Link to="/">Back to the homepage</Link>
    </Layout>
  )
}



export default SecondPage
```

### dynamic vs build-time data
discuss the two and the pros/cons of each

dynamic data
- pro: always fresh
- pro: no need to re-run build
- con: slower w/ network latency

build data
- pro: really fast -- lives on the edge
- pro: w/ gatsby/graphql easy and powerful query possibilities
- con: static & therefore requires a re-build to refresh



```bash
$ npm i @rebass/preset emotion-theming --save
```

create strapi project

```
npx create-strapi-app yummy-cafe-backend --quickstart
```

create a docker-compose.yml

```yaml
version: '3'
services:
  strapi:
    image: strapi/strapi
    volumes:
      - ./:/srv/app
    ports:
      - '1337:1337'
```

prove S3 is empty to show this is all happening live

to deploy, install gatsby s3 plugin

```bash
$ npm i gatsby-plugin-s3 --save
```

deploy to s3 add plugin in `gatsby-config.js`

```json
{
  resolve: `gatsby-plugin-s3`,
  options: {
    bucketName: "glenselle.com",
    protocol: "https",
    hostname: "glenselle.com",
  },
}
```

add deploy script in `package.json`

```
"deploy": "npm run build && gatsby-plugin-s3 deploy"
```

now run deploy

```bash
$ npm run deploy
```

show live site on S3

show how the edge is behind

add following to deploy script

```bash
$ aws cloudfront create-invalidation --distribution-id $CLOUDFRONT_DISTRIBUTION --paths \"/*\""
```