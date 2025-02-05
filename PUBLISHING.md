# PUBLISHING

A reference guide on how to do releases of the VF [monorepo](https://www.toptal.com/front-end/guide-to-monorepos)'s tools and components.

## Notes

- We publish to npm with [Lerna](https://github.com/lerna/lerna#about)
- [Versions in `vf-core`](https://stable.visual-framework.dev/developing/guidelines/versioning/)
- [Updating component versions](https://stable.visual-framework.dev/developing/components/updating-a-component/)

## Prerequisites
- Have installed Lerna globally
- Installed dependencies in `/tools/vf-core/` with `yarn install`
- Installed dependencies in root folder with `yarn install`
- Have logged in to a npm account belonging to the visual-framework organization

## Release workflow

### 1. Component pre-release

1. select the `develop` branch
    - reminder: [we don't use `master`](https://github.com/visual-framework/vf-core/blob/master/README.md)
1. see a list of changed packages
    - `lerna changed`
1. ensure all packages to be released have ready an updated changelog and to which version are going to be bumped
    - verify the new versions of each package doesn't exist on npm
1. test publish
    - `yarn run lerna:test`

### 2. Component release

1. publish to npm
    - `yarn run lerna:publish`
    - lerna will prompt the versions of all packages twice
        - 1st one to publish the packages to npm
        - 2nd one to update package.json files of all packages published

### 3. Communications

1. update the expected new tag version to `/tools/vf-component-library/src/site/_data/siteConfig.js`
    - see last tag `git describe --abbrev=0 --tags`
1. Remove `dev` from `vfVersionPrefix` in `/tools/vf-component-library/src/site/_data/siteConfig.js`, this is needed only for development
1. generate an update
    - `yarn run releasenotes`
    - format and review the newly made file at `tools/vf-component-library/src/site/updates`
        - copy structure from previous release notes
        - for each component, put latest changelog updates and link it with `releaseLinks.notes`

### 4. Post-release

1. commit and push changes to the `develop` branch
    - commit message in a format of: `Component release 20211130-01`
1. add a tag
    - see last tag `git describe --abbrev=0 --tags`
    - add a semantic versioned tag `git tag -a v2.5.2-beta.5 -m 'Release of precompiled CSS, JS, assets'`
    - Why like this?
       - We do not add tags per individual component version. However, Lerna needs a named tag to see what has changed. With our method we get one tag per release bundle and avoid tag spamming in the release history.
       - Trigger a deploy to the CDN (i.e. `v2.5.0`) https://assets.emblstatic.net/vf/v2.4.10/css/styles.css
1. push the tag
    - `git push origin --tags`
1. add release notes to the tag and link to the new blog post
    - https://github.com/visual-framework/vf-core/releases
    - https://stable.visual-framework.dev/updates/

## Appendix of useful Lerna commands

- `lerna ls` list all packages known to lerna
- `lerna ls --since af04cb2a` list all changed packages since a commit (sadly not combinable with lerna publish)
- `lerna diff` all changes since last publish
- `lerna changed` all packages changed
