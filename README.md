# renovate-config

Collection of [Renovate config presets](https://docs.renovatebot.com/config-presets/) used for Side Inc. projects

## Use

1. Enable [Renovate](https://github.com/renovatebot/renovate) and [renovate-approve](https://github.com/renovatebot/renovate-approve-bot) Github apps on the Repo (approve is important for auto merge!)
1. Take note of the team/users assigned to `*` in `CODEOWNERS`
1. Create a `renovate.json` in the base of the repo with the following (replace `<TEAM>` with the team/users from previous step without `@`):

    ```json
    {
      "extends": [
        "github>reside-eng/renovate-config",
        ":reviewer(<TEAM>)"
      ]
    }
    ```

1. Add the following to `CODEOWNERS`:

    ```
    # Skip assigning dep updates (handled by Renovate)
    package.json
    yarn.lock
    ```

## Config Templates

NOTE: all non-default templates are used by name within `extends`. For example, for the template named "service" you would use the following:

    ```json
    {
      "extends": [
        "github>reside-eng/renovate-config:service"
      ]
    }
    ```

### Default

* Labels NPM and Github Actions PRs
* Requires 3 days of stability for npm dependencies (not dev) which are not managed by Side Inc. - during this window npm packages can be un-published which can break builds
* Sets commit type and scope for Github Actions dependency updates
* Sets timezone to `America/Los_Angeles` to match Side's Office for all schedules
* Maintains lock file weekly on Monday morning
* Groups common dependencies including:
  * `config` and `@types/config` updates
  * `@testing-library` monorepo
* Skips Faker updates since it is no longer supported

### Service

For applications that are using continuous delivery including backend services and UIs

* Auto-merges non-major NPM dev dependencies off business hours - prevents overlap and need for update with developer's PRs during the day
* Auto-merges patch NPM dependencies on weekday mornings before the day starts (after 5am before 8am) - Engineers will be around if bugs arise, but still prevents overlap with daytime PRs
* Auto-merges non-major Github Actions off business hours - prevents overlap and need for update with developer's PRs during the day

### Library

For npm libraries

* Groups minor/patch npm dependencies into 1 weekly release monday morning - to prevent release for every dependency update
* Auto-merges non-major dev dependencies
* Auto-merges non-major Github Actions

### Action

For custom Github Action

* Extends Library
* Builds bundle before commit (since dist is part of git tracking)
