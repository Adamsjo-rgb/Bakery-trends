name: Daily Trend Fetch

on:
  schedule:
    # Run at 3:00 AM UTC every day
    - cron: '0 3 * * *'
  # Allow manual trigger for testing
  workflow_dispatch:

jobs:
  fetch-trends:
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
      
      - name: Setup pnpm
        uses: pnpm/action-setup@v2
        with:
          version: 9
      
      - name: Install dependencies
        run: pnpm install
      
      - name: Run daily trend fetch
        env:
          SOCIAVAULT_API_KEY: ${{ secrets.SOCIAVAULT_API_KEY }}
          NEWSAPI_KEY: ${{ secrets.NEWSAPI_KEY }}
          DATABASE_URL: ${{ secrets.DATABASE_URL }}
        run: pnpm exec ts-node scripts/daily-cron-orchestrator.ts
      
      - name: Log execution
        if: always()
        run: echo "Cron job completed at $(date)"

