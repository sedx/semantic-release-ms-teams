# semantic-release-ms-teams

[semantic-release](https://github.com/semantic-release/semantic-release) plugin to send release notes to a teams channel when the release succeeds.

| Step               | Description                                                   |
| ------------------ | ------------------------------------------------------------- |
| `verifyConditions` | Check the `webhookUrl` option or `TEAMS_WEBHOOK_URL` variable |
| `success`          | Send a slack message to notify of a new release.              |

## Installation

```sh
npm install semantic-release-ms-teams --save-dev
# or
yarn add semantic-release-ms-teams --dev
```

This plugin is using an _incoming webhook_ to notify a teams channel. Here is [some documentation](https://docs.microsoft.com/en-us/microsoftteams/platform/webhooks-and-connectors/how-to/add-incoming-webhook#add-an-incoming-webhook-to-a-teams-channel) to create one.

## Usage

```json
// .releaserc.json
{
  "branches": ["master"],
  "plugins": [
    "@semantic-release/commit-analyzer",
    "@semantic-release/release-notes-generator",
    ["semantic-release-ms-teams", {
      "webhookUrl": "...",
      "title": "A new version has been released",
      "imageUrl": "http://..."
    }]
  ]
}
```

| Variable | Details | Description | 
| --- | --- | --- |
| `webhookUrl` or `TEAMS_WEBHOOK_URL` | **required**, url | The incoming webhook url of the channel to publish release notes to. |
| `title` | _optional_, text | The title of the message. Default: _A new version has been released_ |
| `imageUrl` | _optional_, url | An image displayed in the message, next to the title. The image must be less than 200x200. | 

### Notes
**IMPORTANT**: The `webhookUrl` variable you can use within your plugin configuration is meant to be used only for test purposes.
Because you don't want to publicly publish this url and do let the world know a way to send messages to your teams channel,
you will want to use the `TEAMS_WEBHOOK_URL` instead.

The default value for `imageUrl` is <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/4/4e/Gitlab_meaningful_logo.svg/144px-Gitlab_meaningful_logo.svg.png" width="30" height="30" style="border-radius: 50%; vertical-align: middle" />
_https://upload.wikimedia.org/wikipedia/commons/thumb/4/4e/Gitlab_meaningful_logo.svg/144px-Gitlab_meaningful_logo.svg.png_

The message is sent to Teams during the `success` step which is silenced in `dryRun` mode.

## Screenshots

![preview](docs/screenshot-success-1.png "preview")

## Development

Here are some steps to test the plugin locally:

- checkout the source code:
  ```sh
  git clone git@gitlab-ncsa.ubisoft.org:sragot/semantic-release-ms-teams.git
  cd semantic-release-ms-teams
  npm install
  ```
- add a `.releaserc.json` file at the project's root, copy the code from the [Usage](#usage) section in this new file using the `webhookUrl` property, and add the following properties in the object:
  ```
  "ci": false,
  "dryRun": true,
  ```
- open `index.js` and replace `module.exports = { verifyConditions, success };` by `module.exports = { verifyConditions, generateNotes: success };` to allow the publication within the `dryRun` mode
- run `semantic-release` locally safely:
  ```sh
  npm link
  npm link semantic-release-ms-teams
  ./node_modules/.bin/semantic-release
  ```

## Dependencies

- [`node-fetch`](https://www.npmjs.com/package/node-fetch): Send the message to teams
- [`remark`](https://www.npmjs.com/package/remark): Markdown to JSON
- [`mdast-util-to-markdown`](https://www.npmjs.com/package/mdast-util-to-markdown): JSON to Markdown

Greatly inspired by [semantic-release-slack-bot](https://github.com/juliuscc/semantic-release-slack-bot).
