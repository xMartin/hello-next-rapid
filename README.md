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

```
const Index = () => (
  <div>
    <p>Hello Next.js</p>
  </div>
)

export default Index
```


`pages/about.js`:

```
export default () => (
  <div>
    <p>This is the about page</p>
  </div>
)
```

## Navigation

`pages/index.js`:

```
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

## Shared header

`components/Header.js`:

```
import Link from 'next/link'

const linkStyle = {
  marginRight: 15
}

const Header = () => (
  <div>
    <Link href="/">
      <a style={linkStyle}>Home</a>
    </Link>
    <Link href="/about">
      <a style={linkStyle}>About</a>
    </Link>
  </div>
)

export default Header
```

`pages/about.js`:

```
import Header from '../components/Header'

export default () => (
  <div>
    <Header />
    <p>This is the about page</p>
  </div>
)
```

`pages/index.js`:

```
import Link from 'next/link'
import Header from '../components/Header'

const Index = () => (
export default () => (
  <div>
    <Link href="/about">
      <button>Go to About Page</button>
    </Link>
    <Header />
    <p>Hello Next.js</p>
  </div>
)

export default Index
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

```
const express = require('express')
const next = require('next')

const dev = process.env.NODE_ENV !== 'production'
const app = next({ dev })
const handle = app.getRequestHandler()

app.prepare()
.then(() => {
  const server = express()

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

```
import Link from 'next/link'
import fetch from 'isomorphic-unfetch'

const Index = (props) => (
  <div>
    <Header />
    <h1>Batman TV Shows</h1>
    <ul>
      {props.shows.map(({show}) => (
        <li key={show.id}>
          <Link as={`/p/${show.id}`} href={`/post?id=${show.id}`}>
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

`pages/post.js`:

```
import fetch from 'isomorphic-unfetch'

const Post =  (props) => (
  <div>
    <Header />
    <h1>{props.show.name}</h1>
    <p>{props.show.summary.replace(/<[/]?p>/g, '')}</p>
    <img src={props.show.image.medium}/>
  </div>
)

Post.getInitialProps = async function (context) {
  const { id } = context.query
  const res = await fetch(`https://api.tvmaze.com/shows/${id}`)
  const show = await res.json()

  console.log(`Fetched show: ${show.name}`)

  return { show }
}

export default Post
```

