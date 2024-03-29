# Range
Middleware for serving static files

[![NPM Downloads per month](https://img.shields.io/npm/dm/@ceicc/range?logo=npm&style=flat-square&color=088)](https://npmjs.com/package/@ceicc/range)
[![Module type](https://img.shields.io/badge/Module-ESM-informational?style=flat-square)](https://gist.github.com/sindresorhus/a39789f98801d908bbc7ff3ecc99d99c)

## Installation
```bash
npm i @ceicc/range
```

## Usage

add `range` to an existence express app

```js
import { range } from "@ceicc/range"

app.get('/public/*', range())

app.listen(3000)
```

This will serve every request starts with `/public/` with `range`.

The base directory will be `.` or the current working directory, unless specified in the `options` object.

## Options Object

#### `maxAge`

  - default: `10800`
  - type: `number`

  caching period in seconds.

#### `etag`

  - default: `true`
  - type: `boolean`

  add Etag header.

#### `lastModified`

  - default: `true`
  - type: `boolean`

  add last-modified header.

#### `conditional`

  - default: `true`
  - type: `boolean`

  whether to respect conditional requests or not.

#### `range`

  - default: `true`
  - type: `boolean`

  accept range request.

#### `notFound`

  - default: `true`
  - type: `boolean|string`

  a handler for non existing files

  `notFound: false` `next` will be called.

  `notFound: true` empty body with status code '404' will be sent.

  `notFound: <string>` send a file with status code '404', the given string is the path to file.

  if the path doesn't led to a file, `next` will be called.

  ***Note:*** The path is relative to the `baseDir` path.

#### `implicitIndex`

  - default: `true`
  - type: `boolean|Array<string>`

  Check for index files if the request path is a directory.

  Pass an array of extensions to check against. e.g. _`["html", "css"]`_

  Or simply pass `true` to check for html extension only.

#### `baseDir`

  - default: `'.'`
  - type: `string`

  the base dirctory.

#### `hushErrors`

  - default: `false`
  - type: `boolean`

  Whether to ignore errors and reply with status code `500`, or pass the error to `next` function.

#### `trailingSlash`

  - default: `true`
  - type: `boolean`

  Redirect directory requests to add trailing slash.

  disabling this option will led to relative path issues. [see #9](https://github.com/Ceicc/range/issues/9)

  `implicitIndex` must be `true`

#### `compression`

  - default: `false`
  - type: `false|Array<string>`

  Compress the response body with one of the compression algorithm given in the array.

  availabel compression methods are:
  1. `"br"`
  1. `"gzip"`
  1. `"deflate"`

  the compression method will be determined based on the request's [`accept-encoding`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/accept-encoding) header using npm package [`negotiator`](https://npmjs.com/package/negotiator).

#### `dateHeader`

  - default: `true`
  - type: `boolean`

  send `date` response header, `new Date().toUTCString()` function will be used to get the current date.

  [learn more](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Date) about the `date` header


## Real World Example

```js
import { fileURLToPath } from "node:url"
import { join, dirname } from "node:path"
import express from "express"
import { range } from "@ceicc/range"

const app = express()

const __dirname = dirname(fileURLToPath(import.meta.url))

app.get('*', range({ baseDir: join(__dirname, "public") }))

app.use((error, req, res, next) => {
  console.error(error)
  res.sendStatus(500)
})

app.listen(3000, () => console.log("server listening on http://localhost:3000"))
```