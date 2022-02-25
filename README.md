<div align="center">
    <h1>Side Renovate Config</h1>
    <div>Collection of <a href="https://docs.renovatebot.com/config-presets/">Renovate config presets</a> used for Side Inc. projects</div>
    </br>
</div>

<div align="center">

[![Build Status][build-status-image]][build-status-url]

</div>

## Use

1. Enable [Renovate](https://github.com/renovatebot/renovate) and [renovate-approve](https://github.com/renovatebot/renovate-approve-bot) Github apps on the Repo (approve is important for auto merge!)
1. Add the following to `CODEOWNERS`:

   ```
   # Skip assigning dep updates (handled by Renovate)
   package.json
   yarn.lock
   ```

1. Take note of the team/users assigned to `*` in `CODEOWNERS`
1. Update `renovate.json` in the base of the repo to have team/users from previous step in place of `platform-tools` (seperated by commas):

   ```json
   {
     "extends": [
       "github>reside-eng/renovate-config",
       ":reviewer(team:my-team)" // was :reviewer(team:platform-tools)
     ]
   }
   ```

1. Disable Status check requirement for review by code owners
1. Remove Dependabot config and any workflows which are duplicate because of Dependabot

## Config Presets

All non-default presets are used by name within `extends`. For example, for the template named "service" you would use the following:

```json
{
  "extends": ["github>reside-eng/renovate-config:service"]
}
```

**NOTE**: Presets which are within the `bases` SHOULD NOT BE USED OUTSIDE OF THIS PROJECT. This folder is meant only for use within other presets in this Repo and will most likely go away in the future.

### Default

Used by other presets

- Extends [`config:base`](https://docs.renovatebot.com/presets-config/#configbase) which includes auto grouping
- Labels NPM and Github Actions PRs
- Sets commit type and scope for Github Actions dependency updates
- Sets timezone to `America/Los_Angeles` to match Side's Office for all schedules
- Maintains lock file weekly on Monday morning
- Groups common dependencies which are not already automatically grouped in [recommended](https://docs.renovatebot.com/presets-group/#grouprecommended) and [monorepos](https://docs.renovatebot.com/presets-group/#groupmonorepos) presets including:
  - All packages in [Side lint-config](https://github.com/reside-eng/lint-config)
  - `config` and `@types/config` updates
  - `@testing-library` monorepo
- Requires 3 days of stability for npm dependencies (not dev) which are not managed by Side Inc., Google, Apollo, Datadog, or another trusted organization - during this window npm packages can be un-published which can break builds
- Locks Docker file Node version updates to 16 (other versions will be supported in the future)
- Skips `faker` and `@types/fake` updates since it is no longer supported
- Ignores Side Inc. private docker image updates (registry auth not yet setup) [PLAT-1660](https://residenetwork.atlassian.net/browse/PLAT-1660)
- Support for Side Inc. private NPM dependency support

### Service

For applications that are using continuous delivery including backend services and UIs

- Auto-merges non-major NPM dev dependencies off business hours - prevents overlap and need for update with developer's PRs during the day. Not grouped so that breaks clearly indicate the breaking dependency and new releases aren't triggered.
- Groups and auto-merges non-major Github Actions off business hours - prevents overlap and need for update with developer's PRs during the day. Grouping since changes aren't likely to be breaking
- Groups and auto-merges patch NPM dependencies on weekday mornings before the day starts (after 5am before 8am) - Engineers will be around if bugs arise, but still prevents overlap with daytime PRs. Grouped since new release is triggered.
- Groups minor npm dependencies weekly on Monday morning - this will create a single minor release

### Library

For npm libraries

- Groups minor/patch npm dependencies into 1 weekly release monday morning - to prevent release for every dependency update
- Auto-merges non-major dev dependencies
- Auto-merges non-major Github Actions
- Auto-merges `examples` folder non-major npm dependencies weekly on Monday morning
- Groups `examples` folder major npm dependencies weekly on Monday morning

**NOTE**: [`ignoreModulesAndTests`](https://docs.renovatebot.com/presets-default/#ignoremodulesandtests) is within `ignorePresets` since it includes ignoring of the `examples` folder. `ignorePaths` is used to ignore node modules and tests:

```json
"ignorePresets": [":ignoreModulesAndTests"],
"ignorePaths": [
  "**/node_modules/**",
  "**/__tests__/**",
  "**/test/**",
  "**/tests/**"
],
```

### Action

For custom Github Action

- Extends Library Base (library settings without private npm)
- Builds bundle before commit (since dist is part of git tracking)

### Take Home Assignment

For take home assignment repos

- Automerges all non-major npm and Github Actions dependencies

## FAQ

### Why `bases` folder?

`action` preset for custom Github actions must be run on Self Hosted Renovate because of needing to re-build dist as part of commit process. The rebuild requires use of `allowedPostUpgradeCommands` which is currently only available on self-hosted Renovate (which we run in Github actions).

Our current default preset includes `encrypted.npmToken` which is an encrypted version of an npm read token with access to Side Inc. private NPM packages - the encryption is done using Renovate's Public Encrypt app. When running the self hosted version of Renovate we do not have access to the Renovate Cloud private key.

Since there is no way to override the `encrypted` setting, we instead opted to create the bases folder which has base configurations without private npm support and the private-npm preset as it's own file. This way all of the other presets can make use of the base and private-npm where applicable and `action` can opt out of `encrypted.npmToken` then handling including it another way.

This may all go away in the future since we are planning to move to Github Packages instead of private NPM, but this was the best way to prevent needing to update a number of different repos while preserving support for private NPM packages.

[build-status-image]: https://img.shields.io/github/workflow/status/reside-eng/renovate-config/Verify?style=flat-square
[build-status-url]: https://github.com/reside-eng/renovate-config/actions
