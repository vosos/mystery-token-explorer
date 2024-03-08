# Mystery Token Explorer

This GitHub Action is your secret weapon in the ancient art of "Token Archaeology." Designed for the modern developer who finds themselves sifting through the digital ruins of projects past.

This GitHub Action can query the GitHub API and retrieve information about the user associated with a provided GitHub token. It's useful for workflows that require user identification without explicit user input.

## Inputs

### `token`

**Required** The GitHub token, this is used to authenticate and query the GitHub API.

### `jq_filter`

Optional. A JQ filter to customize the output JSON. By default, it returns an object containing the user's name, login, and ID. If you're familiar with JQ syntax, you can modify this to return different user information, currently offering these fields.

```json
{
  "login": "jamesbond",
  "id": 007,
  "node_id": "MI5007=",
  "avatar_url": "https://avatars.githubusercontent.com/u/007?v=4",
  "gravatar_id": "",
  "url": "https://api.github.com/users/jamesbond",
  "html_url": "https://github.com/jamesbond",
  "followers_url": "https://api.github.com/users/jamesbond/followers",
  "following_url": "https://api.github.com/users/jamesbond/following{/other_user}",
  "gists_url": "https://api.github.com/users/jamesbond/gists{/gist_id}",
  "starred_url": "https://api.github.com/users/jamesbond/starred{/owner}{/repo}",
  "subscriptions_url": "https://api.github.com/users/jamesbond/subscriptions",
  "organizations_url": "https://api.github.com/users/jamesbond/orgs",
  "repos_url": "https://api.github.com/users/jamesbond/repos",
  "events_url": "https://api.github.com/users/jamesbond/events{/privacy}",
  "received_events_url": "https://api.github.com/users/jamesbond/received_events",
  "type": "User",
  "site_admin": false,
  "name": "Bond, James Bond",
  "company": null,
  "blog": "https://mi6.com",
  "location": "England",
  "email": null,
  "hireable": null,
  "bio": "Shaking, not stirring.",
  "twitter_username": null,
  "public_repos": 32,
  "public_gists": 1,
  "followers": 17,
  "following": 30,
  "created_at": "1953-03-17T09:28:09Z",
  "updated_at": "2021-03-02T00:05:35Z"
}
```

## Outputs

This action does not explicitly define outputs in the `action.yaml`, but it generates a `user.json` file with the user information and appends a markdown-formatted summary to the `GITHUB_STEP_SUMMARY`, making it visible in the GitHub Actions UI.

## Usage

Here's a basic example of how to use this action in your workflow:

```yaml
jobs:
  identify-user:
    runs-on: ubuntu-latest
    steps:
    - name: Identify GitHub User
      uses: vosos/mystery-token-explorer@v1
      with:
        token: ${{ secrets.GITHUB_TOKEN }}
        jq_filter: '{name: .name, login: .login, id: .id}'
```
Here is a parallel example:
```yaml
jobs:
  user_info:
    name: Reading Token Info
    runs-on: ubuntu-latest
    strategy:
      matrix:
        token_name: [JB_TOKEN, MP_PAT, Q_TK] # List your tokens names here
    steps:
      - name: Retrieving info for ${{ matrix.token_name }}
        env:
          THE_TOKEN: ${{ secrets[matrix.token_name] }} # token names are used as keys for the secrets map
        uses: vosos/mystery-token-explorer@v1
        with:
          token: $THE_TOKEN
          jq_filter: '{name: .name, login: .login, id: .id}'
```
Both examples would return something along the lines of:
```json
{
  "name": "James Bond",
  "login": "jamesbond",
  "id": "MI5007="
}
```