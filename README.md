# Isomorphic web apps in minutes with NextJS

An interactive workshop (~1 hour)

> Let's build a ReactJS web app with server-side rendering, routing and code splitting like it's PHP in the early 2000s, again. We'll just drop in some page component files and are ready to serve the first pages. Then we add a layout and some custom routes with params.

Basic JavaScript and ReactJS skills will be helpful to follow but not necessary. Basic web development skills required.

Please have NodeJS and NPM installed.

## Intro

 - [NextJS](https://nextjs.org/) vs [Create React App](https://github.com/facebook/create-react-app)
 - Isomorphic web apps (aka universal)

## Setup

To start, create a sample project by running the following commands:

```
mkdir hello-next
cd hello-next
npm init -y
npm install --save react react-dom next
mkdir pages
```

Then open the "package.json" in the hello-next directory and add the following NPM script.

```
{
  "scripts": {
    "dev": "next"
  }
}
```

Now everything is ready. Run the following command to start the dev server:

```
npm run dev
```

## Add some pages

`pages/index.js`:

```jsx
const Index = () => (
  <div>
    <p>Hello Next.js</p>
  </div>
)

export default Index
```


`pages/about.js`:

```jsx
export default () => (
  <div>
    <p>This is the about page</p>
  </div>
)
```

## Navigation

`pages/index.js`:

```jsx
import Link from 'next/link'

const Index = () => (
  <div>
    <Link href="/about">
      <a>About Page</a>
    </Link>
    <p>Hello Next.js</p>
  </div>
)
```

## URL Params and data fetching

```
npm install --save express isomorphic-unfetch
```

`package.json`:

```
"scripts": {
  "dev": "node server.js"
}
```

`server.js`:

```javascript
const express = require('express')
const next = require('next')

const dev = process.env.NODE_ENV !== 'production'
const app = next({ dev })
const handle = app.getRequestHandler()

app.prepare()
.then(() => {
  const server = express()

  server.get('/show/:id', (req, res) => {
    const queryParams = { id: req.params.id }
    app.render(req, res, '/show', queryParams)
  })

  server.get('*', (req, res) => {
    return handle(req, res)
  })

  server.listen(3000, (err) => {
    if (err) throw err
    console.log('> Ready on http://localhost:3000')
  })
})
.catch((ex) => {
  console.error(ex.stack)
  process.exit(1)
})
```

`pages/index.js`:

```jsx
import Link from 'next/link'
import fetch from 'isomorphic-unfetch'

const Index = (props) => (
  <div>
    <h1>Batman TV Shows</h1>
    <ul>
      {props.shows.map(({show}) => (
        <li key={show.id}>
          <Link as={`/show/${show.id}`} href={`/show?id=${show.id}`}>
            <a>{show.name}</a>
          </Link>
        </li>
      ))}
    </ul>
  </div>
)

Index.getInitialProps = async function() {
  const res = await fetch('https://api.tvmaze.com/search/shows?q=batman')
  const data = await res.json()

  console.log(`Show data fetched. Count: ${data.length}`)

  return {
    shows: data
  }
}

export default Index
```

`pages/show.js`:

```jsx
import fetch from 'isomorphic-unfetch'

const Show = (props) => (
  <div>
    <h1>{props.show.name}</h1>
    <p>{props.show.summary.replace(/<[/]?p>/g, '')}</p>
    <img src={props.show.image.medium}/>
  </div>
)

Show.getInitialProps = async function (context) {
  const { id } = context.query
  const res = await fetch(`https://api.tvmaze.com/shows/${id}`)
  const show = await res.json()

  console.log(`Fetched show: ${show.name}`)

  return { show }
}

export default Show
```

## Outro

 - [NuxtJS](https://nuxtjs.org/) - same but VueJS
