# Dokploy Trigger and Poll

A GitHub Action to trigger a Dokploy deployment and poll for the result.

## Usage

Add this to your `.github/workflows/deploy.yml`:

```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Deploy to Dokploy
        uses: your-username/dokploy-trigger-and-poll@v1
        with:
          dokploy-url: ${{ secrets.DOKPLOY_URL }}
          dokploy-api-key: ${{ secrets.DOKPLOY_API_KEY }}
          dokploy-app-id: ${{ secrets.DOKPLOY_APP_ID }}
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `dokploy-url` | The base URL of your Dokploy instance | Yes | |
| `dokploy-api-key` | API Key for Dokploy | Yes | |
| `dokploy-app-id` | The Application ID to deploy | Yes | |
| `max-wait-minutes` | Maximum time to wait for deployment in minutes | No | `10` |
