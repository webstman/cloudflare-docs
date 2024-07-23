---
pcx_content_type: navigation
title: TypeScript
weight: 2
meta:
  title: Write Cloudflare Workers in TypeScript
---

## TypeScript

TypeScript is a first-class language on Cloudflare Workers. Cloudflare publishes type definitions to [GitHub](https://github.com/cloudflare/workers-types) and [npm](https://www.npmjs.com/package/@cloudflare/workers-types) (`npm install -D @cloudflare/workers-types`). All APIs provided in Workers are fully typed, and type definitions are generated directly from [workerd](https://github.com/cloudflare/workerd), the open-source Workers runtime.

### Dynamic Runtime Types (Experimental)

Your runtime APIs are dynamic - their exact configuration depends on:

1. The `compatibility_date` defined in your `wrangler.toml`.
2. Any `compatibility_flags` defined in your `wrangler.toml`.

Until now, there was always the potential that the types defined in `@cloudflare/workers-types` might not exactly match your project's runtime APIs. As of `wrangler 3.66.1` there is now [an experimental command](https://developers.cloudflare.com/workers/wrangler/commands/#types) to dynamically generate the correct types for your project:

```bash
npx wrangler types --experimental-include-runtime

# You could also use the alias
npx wrangler types --x-include-runtime
```

This will generate a `d.ts` file and (by default) save it to `.wrangler/types/runtime.d.ts`. You will be prompted in the command's output to add that file to your `tsconfig.json`'s `compilerOptions.types` array.

If you would like to commit the file to git, you can provide a custom path, e.g.:

```bash
npx wrangler types --x-include-runtime="./types.d.ts"
```

Note that the `--x-include-runtime` command replaces the need for the `@cloudflare/workers-types` package, so that should be uninstalled to avoid any potential for conflict.

### Known issues

#### Transitive loading of `@types/node` overrides `@cloudflare/workers-types`

You project's dependencies may load the `@types/node` package on their own. As of `@types/node@20.8.4` that package now overrides `Request`, `Response` and `fetch` types (possibly others) specified by `@cloudflare/workers-types` causing type errors.

The way to get around this issue currently is to pin the version of `@types/node` to `20.8.3` in your `package.json` like this:

```json
---
filename: package.json
---
{
	"overrides": {
		"@types/node": "20.8.3"
	}
}
```

For more information, refer to [this GitHub issue](https://github.com/cloudflare/workerd/issues/1298).

### Resources

- [TypeScript template](https://github.com/cloudflare/workers-sdk/tree/main/templates/worker-typescript)
- [@cloudflare/workers-types](https://github.com/cloudflare/workers-types)
- [Runtime APIs](/workers/runtime-apis/)
- [TypeScript Examples](/workers/examples/?languages=TypeScript)
