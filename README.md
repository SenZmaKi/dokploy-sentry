# Dokploy Trigger and Poll

A GitHub Action to trigger a Dokploy deployment, poll for the result, and surface trigger-time API errors clearly.

## Usage

Add this to your deployment workflow:

```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4 # Required to extract commit metadata
      - name: Deploy to Dokploy
        uses: SenZmaKi/dokploy-sentry@main
        with:
          dokploy-url: ${{ secrets.DOKPLOY_URL }}
          dokploy-api-key: ${{ secrets.DOKPLOY_API_KEY }}
          dokploy-app-id: ${{ secrets.DOKPLOY_APP_ID }}
```

> **Important Note:** This action requires the `.git` directory to generate deployment titles and descriptions. You **must** include the `actions/checkout` step before calling this action.

Deployment entries are labeled with the latest commit message as the Dokploy title, followed by the short commit SHA in parentheses. The Dokploy description contains the GitHub build number and short commit SHA, which keeps the UI cleaner while still letting the action match the exact deployment record for polling.

If Dokploy rejects the trigger request, credentials are missing, the API is unreachable, or the deployment itself fails, the action exits with an explicit error message in the GitHub Actions log.

## Inputs

| Input              | Description                                    | Required | Default |
| ------------------ | ---------------------------------------------- | -------- | ------- |
| `dokploy-url`      | The base URL of your Dokploy instance          | Yes      |         |
| `dokploy-api-key`  | API Key for Dokploy                            | Yes      |         |
| `dokploy-app-id`   | The Application ID to deploy                   | Yes      |         |
| `max-wait-minutes` | Maximum time to wait for deployment in minutes | No       | `5`    |
