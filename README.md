# renovate-config

Collection of [Renovate config presets](https://docs.renovatebot.com/config-presets/) used for Side Inc. projects

## Use

1. Enable [Renovate](https://github.com/renovatebot/renovate) and [renovate-approve](https://github.com/renovatebot/renovate-approve-bot) Github apps on the Repo (approve is important for auto merge!)
1. Take note of the team/users assigned to `*` in `CODEOWNERS`
1. Create a `renovate.json` in the base of the repo with the following (replace `<TEAM>` with the team/users from previous step without `@`):

    ```json
    {
      "extends": ["github>reside-eng/renovate-config", ":assignee(<TEAM>)"]
    }
    ```

1. Add the following to `CODEOWNERS`:

    ```
    # Skip assigning dep updates (handled by Renovate)
    package.json
    yarn.lock
    ```
