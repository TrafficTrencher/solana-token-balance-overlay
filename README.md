# Solana Token Balance GitHub Pages Overlay

This repository deploys a transparent, real-time Solana token balance overlay to GitHub Pages. The overlay displays your SPL token balance in USD and token amounts, fetched securely server-side via GitHub Actions.

## Features

- 🌐 **Transparent Overlay** – Displays token balance with clean, modern UI
- 🔄 **Live Updates** – Fetches balance every 30 seconds (client-side)
- ⚙️ **Server-Side Fetching** – Uses GitHub Actions to query Helius RPC & Jupiter price API (no client-side RPC calls)
- 📊 **Real-Time Pricing** – Shows USD value alongside token amount
- 🔐 **Secure** – API keys stored as repository secrets, never exposed to client

## Setup Instructions

### 1. Add Helius API Key Secret

1. Go to your repository settings: `Settings` → `Secrets and variables` → `Actions`
2. Click **New repository secret**
3. Create a secret named `HELIUS_API_KEY`
4. Paste your Helius API key (get one at [helius.dev](https://helius.dev))

### 2. Enable GitHub Pages

1. Go to `Settings` → `Pages`
2. Set **Source** to `GitHub Actions`
3. Save

### 3. Trigger the Workflow (First Run)

1. Go to `Actions` tab
2. Select **Update Balance & Deploy Pages** workflow
3. Click **Run workflow** → **Run workflow**
4. Wait 1–2 minutes for completion

### 4. Access Your Overlay

Once deployed, your overlay will be live at:

```
https://<USERNAME>.github.io/<REPO_NAME>/
```

For example, if your username is `TrafficTrencher` and repo is `solana-token-balance-overlay`:

```
https://trafficTrencher.github.io/solana-token-balance-overlay/
```

**JSON Data URL** (for reference):
```
https://trafficTrencher.github.io/solana-token-balance-overlay/balance.json
```

### 5. Use in PRISM Browser Widget

1. Copy your overlay URL from Step 4
2. In PRISM streaming settings, add the overlay URL to your browser source/widget config
3. The overlay will now display your live Solana token balance

## How It Works

### Server-Side (GitHub Actions)

Every 5 minutes (and on manual trigger), the workflow:

1. **Queries Helius RPC** – Fetches SPL token balance for your wallet:
   - Wallet: `CviKehFyefZVe4CV7D5ZoDsmwubtpjwysG4oxxDYPqGP`
   - Mint: `BwWdcMtBRz8R38sbjA3GnkR35wz6z3xz3GFiS1yGpump`

2. **Fetches Jupiter Price** – Gets current USD price for the token

3. **Generates `balance.json`** – Writes output containing:
   - `balance` – Token amount (float)
   - `price_usd` – Current USD price (or null if unavailable)
   - `total_usd` – Balance × Price (or null if price unavailable)
   - `updated_at` – ISO timestamp of last fetch

4. **Deploys to Pages** – Publishes `site/` directory with `index.html` and `balance.json`

### Client-Side (index.html)

The overlay:

- Fetches `balance.json` every 30 seconds (with cache-busting query param)
- Displays USD value, token amount, and last-updated timestamp
- Shows "USD: N/A" gracefully if price is unavailable
- Uses transparent background + blur effect for overlay use

## Customization

### Change Wallet or Mint

Edit `.github/workflows/pages-balance.yml` and update:

```yaml
WALLET = 'YOUR_WALLET_ADDRESS'
MINT = 'YOUR_MINT_ADDRESS'
```

### Change Update Frequency

In `.github/workflows/pages-balance.yml`, modify the cron schedule:

```yaml
schedule:
  - cron: '*/5 * * * *'  # Every 5 minutes (adjust as needed)
```

Cron syntax: `minute hour day-of-month month day-of-week`

### Customize Overlay Display

Edit `index.html` and modify:

- Colors (currently green: `#00ff88`)
- Font sizes
- Background opacity
- Refresh interval (currently 30 seconds)

## Troubleshooting

### "USD: N/A" is displayed

- Check that `HELIUS_API_KEY` secret is set correctly
- Verify the Jupiter API endpoint is responding (token may not be listed)
- Check workflow logs: `Actions` → **Update Balance & Deploy Pages** → Latest run

### Overlay not updating

- Ensure Pages deployment is complete (`Settings` → `Pages` shows "Your site is live")
- Check browser console for fetch errors (F12 → Console)
- Verify `balance.json` exists at `https://<USERNAME>.github.io/<REPO_NAME>/balance.json`

### Workflow fails

1. Go to `Actions` → **Update Balance & Deploy Pages** → Latest run
2. Expand failed step to see error logs
3. Common issues:
   - `HELIUS_API_KEY` not set or invalid
   - Wallet/Mint addresses are incorrect
   - Network timeout (temporarily retry manually)

## Files

- **`index.html`** – Transparent overlay HTML/CSS/JS
- **`.github/workflows/pages-balance.yml`** – GitHub Actions workflow for fetching balance & deploying
- **`balance.json`** – Generated automatically by workflow (contains balance, price, timestamp)

## Security Notes

- ✅ API keys stored as **secrets**, never committed to repo
- ✅ RPC calls made **server-side only** (GitHub Actions), not from client browser
- ✅ Public repo is safe – no sensitive data is exposed

## Support

For issues with:

- **Helius API** – Visit [helius.dev](https://helius.dev)
- **Jupiter Prices** – Check [price.jup.ag](https://price.jup.ag)
- **GitHub Pages** – See [GitHub Docs](https://docs.github.com/en/pages)

---

**Live at:** `https://<USERNAME>.github.io/<REPO_NAME>/`