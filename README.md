# weaviate-github-issue-triage

GitHub Action to detect duplicate issues and assist in triaging new ones efficiently by leveraging Weaviate's semantic search.

## Prerequisite: Vectorize Your Issues

Before using this action, you must first vectorize all your GitHub issues into your Weaviate instance. Use the [weaviate/github-issues-to-weaviate](https://github.com/weaviate/github-issues-to-weaviate) GitHub Action to do this. This action fetches all issues from your repository and stores them as vectors in Weaviate, enabling semantic search for triage.

---

## Usage

Add this action to your workflow to automatically check new GitHub issues for semantic similarity against a Weaviate instance. If a similar issue is found, the action will comment on the issue with a link and explanation.

### Example Workflow

```yaml
name: Issue Similarity Triage

on:
  issues:
    types: [opened]

jobs:
  triage:
    runs-on: ubuntu-latest
    steps:
      - name: Check for similar issues
        uses: weaviate/weaviate-github-issue-triage@v1
        with:
          issue_body: ${{ github.event.issue.body }}
          issue_number: ${{ github.event.issue.number }}
          weaviate_url: ${{ secrets.WEAVIATE_URL }}
          weaviate_api_key: ${{ secrets.WEAVIATE_API_KEY }}
          github_token: ${{ secrets.GITHUB_TOKEN }}
          collection_name: GitHubIssues  # optional
          github_repository: ${{ github.repository }}  # optional
```

## Inputs

| Name               | Required | Description                                                                                       | Default         |
|--------------------|----------|---------------------------------------------------------------------------------------------------|-----------------|
| `issue_body`       | Yes      | The body content of the newly opened GitHub issue. Used as the query for semantic similarity.     | —               |
| `issue_number`     | Yes      | The number of the newly opened GitHub issue. Used to post comments if a duplicate is found.       | —               |
| `weaviate_url`     | Yes      | The URL of your Weaviate instance.                                                                | —               |
| `weaviate_api_key` | Yes      | API key for authenticating with your Weaviate instance.                                           | —               |
| `github_token`     | Yes      | GitHub token with permission to post comments on issues. Usually `${{ secrets.GITHUB_TOKEN }}`.   | —               |
| `collection_name`  | No       | Name of the Weaviate collection to search for similar issues.                                     | `GitHubIssues`  |
| `github_repository`| No       | Repository in `owner/repo` format. If not set, defaults to the current repository context.        | —               |

### Parameter Explanations

- **issue_body**: The content/body of the issue that was just opened. This is used as the query for semantic similarity search in Weaviate.
- **issue_number**: The number of the issue that was just opened. This is used to post a comment if a similar issue is found.
- **weaviate_url**: The endpoint of your Weaviate instance (e.g., `https://my-weaviate-instance.com`).
- **weaviate_api_key**: Secret key for accessing your Weaviate instance securely.
- **github_token**: Used to authenticate with the GitHub API to post comments on issues. Should have `repo` scope.
- **collection_name**: (Optional) The name of the collection in Weaviate where GitHub issues are stored. Defaults to `GitHubIssues`.
- **github_repository**: (Optional) The full name of the repository in the format `owner/repo`. If omitted, the action uses the repository from the workflow context.

## Outputs

This action does not produce explicit outputs, but will comment on the issue if a similar one is found.

## Requirements

- A running Weaviate instance with a collection of GitHub issues.
- The following Python dependencies are used (see `requirements.txt`):
  - `weaviate-client[agents]`
  - `requests`

## License

MIT
