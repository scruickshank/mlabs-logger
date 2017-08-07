# Examples

## Running Examples Locally

### Local configuration

Set required and optional configuration options in `examples/local.json`, e.g.,

```json
{
  "logLevel": "debug"
}
```

Override any option with the corresponding environment variable:

  - `LOG_LEVEL` (optional)

### Running examples with arguments

List all runnable examples with

```
$ yarn run example
```

Run provided examples with, e.g.,

```
$ yarn run example error | yarn run bunyan
```

or more compactly with, e.g.,

```
$ yarn example error | yarn bunyan
```

Pass arguments to examples with

```
$ yarn run example error fire | yarn bunyan
```

Automatically watch and rerun an example on changes with, e.g.,

```
$ yarn run example:watch error | yarn run bunyan
```

#### Debugging examples

Debug examples with, e.g.,

```
$ yarn run example:inspect error | yarn run bunyan
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

Automatically watch and rerun a debuggable example on changes with, e.g.,

```
$ yarn run example:inspect:watch error | yarn run bunyan
```

### Shell function aliases for running examples

In bash or zsh, you may define convenience functions for the above with

```bash
yrx () { yarn run example $@ | yarn run bunyan; }
yrxw () { yarn run example:watch $@ | yarn run bunyan; }
yrxi () { yarn run example:inspect $@ | yarn run bunyan; }
yrxiw () { yarn run example:inspect:watch $@ | yarn run bunyan; }
```

## Importing Examples as Modules

All examples are included with this package as importable modules.

_Imported examples are not supported as a stable API._

_Some examples may use devDependencies
which need to be installed as dependencies
by any package which imports them._

Create and run an example with

```js
import { createExample } from '@meltwater/mlabs-logger'

// createExample(exampleName, options)(...args)
createExample('error')().catch(err => { console.error(err) })
```

or import them directly with

```js
import { examples } from '@meltwater/mlabs-logger'

const error = examples.error()

error().then(data => { console.log(data) }).catch(err => { console.error(err) })
```

## Writing New Examples

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
     return request(`${fooApi}/search/${query}`, {qs})
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