# Cubes Client Runtime Configuration Assets

Upload/deploy this folder as a static website. Do not copy it inside the mobile app.

Cloudflare base URL currently used for testing:

```text
https://lucky-bonus-6e1f.tjaradat1.workers.dev
```

## Preferred URL Pattern

```text
{CONFIG_BASE_URL}/clients/{clientKey}/config/client.config.json
```

## Backward-Compatible Fallback

```text
{CONFIG_BASE_URL}/clients/{clientKey}/config/branding.config.json
```

## Included Test Client Keys

```text
demo-client
apqc
ef
```

## Startup Flow Rule

The first screen the user must see is the Client Runtime Configuration screen.

The second screen is the existing Configuration page when:
- the user skips entering the client key, or
- the user enters a valid key and the client config is fetched successfully.

If fetching fails, show a friendly warning and allow continuing with the default configuration.

## Important Relative Asset Rule

Asset paths inside the config are relative. Resolve them relative to the config URL:

```ts
new URL(config.branding.assets.loginLogo, configUrl).toString()
```

Do not use the raw relative value directly.
