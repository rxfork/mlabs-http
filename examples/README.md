# Examples

## Configuration

Set required and optional configuration options in `examples/local.json`, e.g.,

```json
{
  "logLevel": "debug"
}
```

Override any option with the corresponding environment variable:

  - `LOG_LEVEL` (optional)

## Running locally

List all runnable examples with

```
$ yarn run example
```

Run provided examples with, e.g.,

```
$ yarn run example -- is-true | yarn run bunyan
```

or more compactly with, e.g.,

```
$ yarn example is-true | yarn bunyan
```

Pass arguments to examples with

```
$ yarn run example is-true false | yarn bunyan
```

Automatically watch and rerun an example on changes with, e.g.,

```
$ yarn run example:watch -- is-true | yarn run bunyan
```

### Debugging

Debug examples with, e.g.,

```
$ yarn run example:inspect -- is-true | yarn run bunyan
```

For examples which run a single process and then exit,
create a breakpoint by adding the statement `debugger`
to the top of the example function, e.g.,

```js
export default ({log}) => async () => {
  debugger
  // ...
}
```

Automatically watch and rerun an debugging example on changes with, e.g.,

```
$ yarn run example:inspect:watch -- is-true | yarn run bunyan
```

### Shell function aliases

In bash or zsh, you may define convenience functions for the above with

```bash
function yrx () { yarn run example $@ | yarn run bunyan; }
function yrxw () { yarn run example:watch $@ | yarn run bunyan; }
function yrxi () { yarn run example:inspect $@ | yarn run bunyan; }
function yrxiw () { yarn run example:inspect:watch $@ | yarn run bunyan; }
```

## Importing

All examples are included with this package as importable modules.

_Imported examples are not supported as a stable API._

_Some examples may use devDependencies
which need to be installed as dependencies
by any package which imports them._

Create and run an example with

```js
import { createExample } from '@meltwater/makenew-node-lib'

// createExample(exampleName, options)(...args)
createExample('is-true')().catch(err => { console.error(err) })
```

or import them directly with

```js
import createLogger from '@meltwater/mlabs-logger'
import { examples } from '@meltwater/makenew-node-lib'

const isTrue = examples.isTrue({log: createLogger()})

isTrue().then(data => { console.log(data) }).catch(err => { console.error(err) })
```

## Writing examples

1. Create a new file in `examples`.
   All exported functions can take options and arguments with defaults, e.g.,

   ```js
   /* examples/query-api.js */
   import request from 'request-promise'

   export default ({
     log,
     fooApi = 'https://example.com'
   }) => async (query = 'foo', page = 1) => {
     const qs = {page: parseInt(page)}
     log.debug({query, qs})
     return request(`${fooApi}/query`, {qs})
   }
   ```

2. Import and add the example to `examples/index.js`, e.g.,

   ```js
   /* examples/index.js */
   import queryApi from './query-api'

   export const examples = {
     queryApi,
     // ...
   }
   ```

3. Add any new options to this README and in `examples/index.js`, e.g.,

   ```js
   /* examples/index.js */
   export const envVars = [
     'LOG_LEVEL',
     'FOO_API',
     // ...
   ]
   ```