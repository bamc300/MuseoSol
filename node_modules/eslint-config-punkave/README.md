## Install

This module is for advanced users looking for a shareable configuration across projects for ESLint.

In the project root directory run:

```bash
npm install eslint eslint-plugin-promise eslint-plugin-standard eslint-plugin-react eslint-config-standard eslint-config-punkave --save-dev
```

Also, add the following to `.eslintrc` in the root directory:

```json
{
  "extends": "punkave"
}
```

## Other

If you're using Atom as your editor you can install the [ESLint plugin](https://atom.io/packages/linter-eslint), which provides an interface for ESLint and this configuration.
