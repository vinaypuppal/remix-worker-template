# remix-worker-template

> The current template makes use of a non-stable version of Remix (0.17.5) and requires [patches](#patches) to make the build works. It is made just for people who would like to try out this combination until the [official support](https://github.com/remix-run/remix/tree/dev/packages/remix-cloudflare-workers) is ready.

- [Remix Docs](https://docs.remix.run)
- [Customer Dashboard](https://remix.run/dashboard)

## Give it a try

> The deploy button will guide you through the setup for CF_API_TOKEN and CF_ACCOUNT_ID on the UI. But you are still required to provide the REMIX_TOKEN as a repository secret yourself for the deploy action to work properly.

[![Deploy to Cloudflare Workers](https://deploy.workers.cloudflare.com/button)](https://deploy.workers.cloudflare.com/?url=https://github.com/edmundhung/remix-worker-template)

## Patches

The current challenge in running Remix on Cloudflare Workers is mainly caused by the usage of several node only packages and the server build also targeting the `node` platform.

What the [patches](./patches) did are simply (1) removing several cookie & session utilities from the `@remix-run/node` package and (2) switching the server build to target the `browser` platform on the `@remix-run/dev` package instead.

As a result, the following API will be missing from the `@remix-run/node` package:
- createCookie
- isCookie
- createSession
- isSession
- createSessionStorage
- createCookieSessionStorage
- createFileSessionStorage
- createMemorySessionStorage

The only addition is the `createRequestHandler` being patched to simulate the interface of a platform-like API similar to how they are served from `@remix-run/vercel` or `@remix-run/express` etc. With the interface as follow:

```ts
export declare function createRequestHandler({ build, getLoadContext, mode }: {
  build: ServerBuild;
  getLoadContext?: GetLoadContextFunction;
  mode?: string;
}): (request: Request) => Promise<Response>;
```

## Development

To starts your app in development mode, rebuilding assets on file changes, the recommended approach is:

```sh
npm run dev
```

However, if your setup relies on Cloudflare specific products, such as `Workers KV` or `Durable Objects`, you might need to develop with:

```sh
npm run preview # Equivalent to `npx wrangler preview`
```

Both `wrangler preview` or `wrangler dev` will run your worker on an edge server from Cloudflare that operates your Worker in development. This allows full access to Workers KV, Durable Objects, etc

## Deployment

First, build your app for production:

```sh
npm run build
```

Then deploy the worker with Wrangler:

```sh
npm run deploy
```
