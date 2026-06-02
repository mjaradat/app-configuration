# Frontend Contract

## First Screen Requirement

The first thing the user sees must be the Client Runtime Configuration screen.

This screen appears before the existing Configuration page.

Actions:
- Enter client key and apply
- Continue without client key

## Navigation Rule

After the first screen:

1. If the user skips the client key:
   - use default app configuration
   - navigate to the existing Configuration page

2. If the user enters a valid key and config fetch succeeds:
   - apply client runtime config
   - navigate to the existing Configuration page
   - prefill/apply any configurable auth/API/module defaults from the fetched config where the current page supports it

3. If the user enters a key and fetch fails:
   - show friendly warning
   - continue with default configuration
   - allow navigation to the existing Configuration page

## Fetch Order

Try first:

```text
{CONFIG_BASE_URL}/clients/{clientKey}/config/client.config.json
```

If 404, fallback to:

```text
{CONFIG_BASE_URL}/clients/{clientKey}/config/branding.config.json
```

## Client Key Validation

Allowed pattern:

```text
^[a-z0-9-]+$
```

Trim and lowercase before validation.

Do not allow full URLs, spaces, slashes, special characters, or path traversal.

## Asset URL Resolution

Correct:

```ts
new URL(relativeAssetPath, configUrl).toString()
```

Wrong:

```ts
relativeAssetPath
```

## Default Behavior

The app must work with default styles and default configuration when no key is entered.
Client runtime configuration is optional.
