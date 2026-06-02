# Server Setup Guide

Upload this package to Cloudflare Pages or Workers Static Assets.

After deployment, test:

```text
https://lucky-bonus-6e1f.tjaradat1.workers.dev/health.json
https://lucky-bonus-6e1f.tjaradat1.workers.dev/clients.index.json
https://lucky-bonus-6e1f.tjaradat1.workers.dev/clients/demo-client/config/client.config.json
https://lucky-bonus-6e1f.tjaradat1.workers.dev/clients/apqc/config/client.config.json
https://lucky-bonus-6e1f.tjaradat1.workers.dev/clients/ef/config/client.config.json
```

The mobile app should use:

```env
VITE_CLIENT_CONFIG_BASE_URL=https://lucky-bonus-6e1f.tjaradat1.workers.dev
```

Do not use real secret license keys in this public static config.
