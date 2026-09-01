# Building

## Requirements

- Git with submodule support
- Hugo Extended 0.165.0 or later

## Checkout

Initialize the NexT theme after cloning the repository:

```bash
git submodule update --init --recursive
```

## Local Preview

Start the development server from the repository root:

```bash
hugo server
```

The site is available at <http://localhost:1313/> by default.

## Production Build

Generate the complete static site:

```bash
hugo --cleanDestinationDir
```

Hugo writes the generated site to `public/`. Generated files are build
artifacts and should not be committed.
