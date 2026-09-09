# Run instructions

A REST and GraphQL API in Node.js and TypeScript, built with Clean Architecture, TDD,
design patterns and SOLID principles. It is the reference implementation used to teach
the approach, so the structure is deliberate: every layer boundary is a directory, and
every use case is covered by tests written before the implementation.

## Prerequisites

| | Version |
|---|---|
| Node.js | 16.x (declared in `package.json` → `engines`) |
| MongoDB | reachable via `MONGO_URL`; `docker-compose.yml` provides one |

## Install and run

```bash
npm ci
npm run build      # rimraf dist && tsc -p tsconfig-build.json, then copy static assets
npm start          # node dist/main/server.js
```

With Docker, which brings up MongoDB alongside the API:

```bash
npm run up         # builds, then docker-compose up -d
npm run down       # tears it down
```

The API listens on the port in `MONGO_URL`/`PORT`, serves REST under `/api` and exposes
a GraphQL endpoint with Apollo Server. Static API documentation is copied into
`dist/static` by the `postbuild` step.

## Tests

```bash
npm test                # the whole suite, serially
npm run test:unit       # unit tests, watch mode
npm run test:integration # integration tests, watch mode
npm run test:ci         # with coverage
```

Integration tests need MongoDB running. The suite runs with `--runInBand` because the
integration tests share one database.

## Layout

```
src/domain/         Entities and use-case contracts — depends on nothing
src/data/           Use-case implementations, depending only on domain contracts
src/infra/          MongoDB repositories, cryptography, validators
src/presentation/   Controllers, and the HTTP-shaped request/response contracts
src/main/           Composition root: factories, routes, adapters, middlewares, GraphQL
src/validation/     Validator implementations used by the presentation layer
```

The dependency rule points inward: `main` may reference anything, `domain` references
nothing. Adapters in `src/main/adapters/` are the only place where Express types appear
outside `main`.
