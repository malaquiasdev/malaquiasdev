---
title: "Tilde or Caret in package.json - Stop copying and start choosing"
date: '2024-02-18'
tags:
    - nodejs npm
description: Tilde or Caret, What should I use?
---

[Thanks to masahiro miyagi @masamasa3 for making this photo available on Unsplash 🎁](https://unsplash.com/photos/q20F_rFKdCw)

## TL;DR

| Syntax      | Versions | Dependency | Impacts|
| ----------- | ----------- | ----------- | ----------- |
| tilde (~)      | 1.6.X       | ~1.6.7 | only bug fix can be upgrade | 
| caret(^)   | 1.X.X        | ^1.6.7 | backwards compatible, deprecated and operational functionally, refactors, bug fix  |

- **Tilde (~):** Use it for libraries where stability is crucial. Example: express framework.
- **Caret (^):** Use it for libraries where you want to benefit from new features and bug fixes. Example: Jest, Prettier.
## SEMVER 

SemVer is a convention, not a law. What happens when each number changes:
- **Major:** Something big has changed, and previous versions maybe will not work. 
- **Minor:** New features or improvements, things should still work mostly the same.
- **Patch:** Solves small issues without changing the structure.

## Tilde (~)

- Only allows **patch updates**.
- Useful for getting the latest bug fixes and security patches while maintaining stability.
- Use it for libraries where stability is crucial. Example: express framework.

## Caret (^)

- Allow **both minor and patch updates**.
- Useful for staying up-to-date with new features and improvements, however, we have a risk of potentially breaking changes.
- Use it for libraries where you want to benefit from new features and bug fixes. Example: Jest, Prettier.

## How to update my dependencies?

```
npm outdated
```

This command will scan your code for outdated packages compare with the latest version in the [npm registry](https://www.npmjs.com/) and no extra downloads needed because it is built into npm.

```
npm update
```

This command will update the dependencies in package.json and package-lock.json using the "wanted" version.
## Resources

[Semantic Versioning 2.0.0](https://semver.org/#semantic-versioning-200)
[Node.js - Peer Dependencies](https://nodejs.org/en/blog/npm/peer-dependencies)
[Node.js - An introduction to the npm package manager](https://nodejs.org/en/learn/getting-started/an-introduction-to-the-npm-package-manager)
