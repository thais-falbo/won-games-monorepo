# 🎮 Won Games Monorepo

Monorepo version of project Won Games from the Udemy course [React Avançado](https://www.udemy.com/course/react-avan%C3%A7ado/) by [Willian Justen](https://github.com/willianjusten) and  [Guilherme Louro](https://github.com/guilhermelouro).

## Tech Stack
- [Next.js 15](https://nextjs.org/)
- [Tailwind CSS](https://tailwindcss.com/)
- [Strapi 5](https://strapi.io/)
- [TypeScript](https://www.typescriptlang.org/)
- [Biome](https://biomejs.dev/)
- [Turborepo](https://turbo.build/)
- [Lefthook](https://github.com/evilmartians/lefthook)
- [Commitizen](https://github.com/commitizen/cz-cli)
- [Commitlint](https://commitlint.js.org/)

## Setup

### Prerequisites
- [Node.js 22](https://nodejs.org/)
- [Pnpm 10](https://pnpm.io/)

### Installation

```sh
# In root directory
pnpm install
```

This will install all dependencies for all `apps/` and `packages/` directories.

###  Turborepo scripts

After installing, you can use Turbo scripts to manage the project:

```sh
# In root directory

# Lint
pnpm biome # lint
pnpm biome:fix # lint and fix

# Build
pnpm build # Build all apps
pnpm build:strapi # Build Strapi app
pnpm build:nextjs # Build Next.js app

# Dev
pnpm dev # Run dev server for all apps
pnpm dev:strapi # Run dev server for Strapi app
pnpm dev:nextjs # Run dev server for Next.js app
```

# Lefthook tasks

1. **Pre Commit:** Runs biome to lint and format code
2. **Prepare Commit:** Runs commitzen to create a commit message
3. **Commit message:** Lints commit message
