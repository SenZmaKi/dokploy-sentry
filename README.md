# Dokploy Sentry

A GitHub Action to trigger a Dokploy deployment (application or compose), poll for the result, and surface API errors clearly.

## Usage

Add this to your deployment workflow:

```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4 # Required to extract commit metadata

      # Deploy an application
      - name: Deploy to Dokploy
        uses: SenZmaKi/dokploy-sentry@main
        with:
          dokploy-url: ${{ secrets.DOKPLOY_URL }}
          dokploy-api-key: ${{ secrets.DOKPLOY_API_KEY }}
          dokploy-app-id: ${{ secrets.DOKPLOY_APP_ID }}

      # — or — deploy a compose service
      - name: Deploy to Dokploy
        uses: SenZmaKi/dokploy-sentry@main
        with:
          dokploy-url: ${{ secrets.DOKPLOY_URL }}
          dokploy-api-key: ${{ secrets.DOKPLOY_API_KEY }}
          dokploy-compose-id: ${{ secrets.DOKPLOY_COMPOSE_ID }}
```

> **Important:** This action requires the `.git` directory to read commit metadata. You **must** include `actions/checkout` before calling this action.

> **Note:** `dokploy-app-id` and `dokploy-compose-id` are mutually exclusive — provide exactly one.

### Application deployments

Deployment records are labeled using the latest commit message as the Dokploy title, with the short SHA in parentheses. The description includes the GitHub build number and short SHA. The action matches this exact title and description when polling, so re-runs on the same commit are handled correctly.

### Compose deployments

The Dokploy compose API does not accept a custom title or description. Instead, the action records a timestamp immediately before triggering and matches the first deployment record created at or after that time when polling.

## Inputs

| Input                | Description                                                             | Required | Default |
| -------------------- | ----------------------------------------------------------------------- | -------- | ------- |
| `dokploy-url`        | The base URL of your Dokploy instance                                   | Yes      |         |
| `dokploy-api-key`    | API Key for Dokploy                                                     | Yes      |         |
| `dokploy-app-id`     | Application ID to deploy (mutually exclusive with `dokploy-compose-id`) | No       |         |
| `dokploy-compose-id` | Compose ID to deploy (mutually exclusive with `dokploy-app-id`)         | No       |         |
| `max-wait-minutes`   | Maximum time to wait for deployment in minutes                          | No       | `5`     |

## Error handling

The action exits with a non-zero code and prints structured error details for all failure modes:

- **Missing or conflicting inputs** — validation runs before any API call is made.
- **Network errors** — the curl exit code and error message are printed.
- **HTTP errors** — the status code, request URL, raw response body, and an extracted human-readable message are all printed.
- **Deployment failure or cancellation** — the full deployment record (including `logPath` if present) is printed alongside the extracted error detail.
- **Timeout** — prints the deploy type and ID so you know exactly what to check in the Dokploy UI.
