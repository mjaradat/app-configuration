You are working on my Ionic Vue + TypeScript + Capacitor mobile app.

Implement a Runtime Client Configuration feature.

This is not only branding anymore. The client key can load:
- branding
- auth configuration
- API/module base URLs
- enabled modules
- public license metadata
- default language/direction/theme
- feature flags
- support/runtime settings

Cloudflare config base URL:

https://lucky-bonus-6e1f.tjaradat1.workers.dev

Preferred config URL pattern:

{CONFIG_BASE_URL}/clients/{clientKey}/config/client.config.json

Backward-compatible fallback config URL pattern:

{CONFIG_BASE_URL}/clients/{clientKey}/config/branding.config.json

Available test keys:

demo-client
apqc
ef

Critical screen-flow requirement:
1. The first thing the user must see is the Client Runtime Configuration screen.
2. This screen must appear before the existing Configuration page.
3. The Client Runtime Configuration screen must allow:
   - entering a client key and applying it
   - continuing without a client key
4. The second page should be the existing Configuration page when:
   - the user skipped entering the client key, or
   - the user entered a valid client key and the client runtime config was fetched successfully
5. If the user enters a key and fetching fails:
   - show a friendly warning
   - keep default app configuration
   - allow the user to continue to the existing Configuration page
6. Do not send the user directly to login before this flow if the current app has a Configuration page that normally appears first.
7. Do not make client key required.
8. Do not block the app because client runtime config failed.

Important:
- All client-specific assets and configs are hosted remotely on Cloudflare.
- Do NOT put client branding assets or client-specific config inside the app.
- Do NOT hardcode client-specific logos, colors, images, fonts, auth URLs, or API URLs inside the app.
- Only the single CONFIG_BASE_URL may be configured in the app.
- Do NOT implement custom splash screen branding now.
- Do NOT redesign the app.
- Do NOT make unrelated refactors.
- Apply changes directly to the existing project files.

Environment/config:
Use:

VITE_CLIENT_CONFIG_BASE_URL=https://lucky-bonus-6e1f.tjaradat1.workers.dev

If the project already has a better environment/config pattern, follow it. Avoid scattering the URL in multiple files.

Fetch behavior:
- Validate the client key with this pattern:

  ^[a-z0-9-]+$

- Trim the key.
- Convert it to lowercase.
- Reject spaces, slashes, URLs, special characters, and path traversal.
- Do not allow the user to enter a full URL.
- Build the URL inside the app.

Fetch order:
1. Fetch client.config.json:

   {CONFIG_BASE_URL}/clients/{clientKey}/config/client.config.json

2. If it returns 404, fallback to branding.config.json:

   {CONFIG_BASE_URL}/clients/{clientKey}/config/branding.config.json

3. If both fail, keep default app config and default styles.

Startup behavior:
- Start with default Cubes styles/config immediately.
- Check if a previously valid client key/config exists in storage.
- If saved config exists, it may be used as a cache.
- Still try to refresh the latest config from Cloudflare.
- If refresh succeeds, apply latest config and update cache.
- If refresh fails but cached config exists, use cached config.
- If no cache exists, use default app config.
- Do not block the first screen forever while checking remote config.

Existing Configuration page behavior:
- The Client Runtime Configuration screen appears before it.
- When the user skips the client key, navigate to the existing Configuration page using default config.
- When the user enters a valid key and config loads successfully, navigate to the existing Configuration page and prefill/apply the fetched auth/API/module defaults where the existing page supports it.
- If the existing Configuration page allows setting auth/API URLs, values from remote config should become defaults, but the user can still review/change them if current app behavior allows that.
- Do not remove the existing Configuration page.

Config data currently available in client.config.json:
- auth.baseUrl: https://qc09.cubescpm.com/Cubes
- auth.tokenEndpoint: https://qc09.cubescpm.com/Cubes/Auth/Token
- apis.audit.baseUrl: https://qc02.cubescpm.com/AuditManagement/BE
- apis.audit.apiBaseUrl: https://qc02.cubescpm.com/AuditManagement/BE/api
- modules.audit.enabled: true
- license: public test metadata only

Security:
- The Cloudflare config is public.
- Do not put real private license secrets in the app or config.
- Treat license info from Cloudflare as public metadata only.
- Real license enforcement must be server-side later.

Asset path rule:
The config contains relative asset paths like:

../assets/logos/login-logo.svg

Resolve every asset URL relative to the config URL.

Correct:

new URL(config.branding.assets.loginLogo, configUrl).toString()

Wrong:

config.branding.assets.loginLogo

This is critical because the app may run inside Capacitor using a different origin such as capacitor://localhost.

Apply branding:
- Apply branding by setting CSS variables on document.documentElement.
- Map remote branding values into the existing CSS variable system where appropriate.
- Do not break existing default styles.
- Branding only overrides default styles when valid remote config is loaded.

Use variables like:
--brand-primary
--brand-primary-hover
--brand-primary-foreground
--brand-secondary
--brand-secondary-foreground
--brand-accent
--brand-accent-foreground
--brand-background
--brand-surface
--brand-surface-muted
--brand-text
--brand-text-muted
--brand-border
--brand-input
--brand-ring
--brand-success
--brand-warning
--brand-danger
--brand-radius-sm
--brand-radius-md
--brand-radius-lg
--brand-radius-xl
--brand-font-en
--brand-font-ar

Theme/language:
- Respect existing app theme/language preferences.
- Do not override logged-in user preferences.
- Use client defaults only before login or when no user preference exists.
- Apply document.documentElement.dir only when appropriate and without breaking existing RTL/LTR logic.
- If theme changes later, re-apply the correct light/dark client branding tokens.

Logos/images:
- Use resolved remote URLs for loginLogo, headerLogo, collapsedLogo, and loginBackground only where the app already has or can cleanly support those locations.
- If remote logo fails, fall back to default app logo.
- Do not remove default logo.

Fonts:
- Do not add Google Fonts.
- Do not add unlicensed font files.
- Apply only font-family CSS variables from config.
- Keep fallback fonts.

Persistence:
Use the storage approach already used in the project. If Capacitor Preferences is already used, use it. If localStorage is already used, use localStorage.

Store:
- clientConfig.clientKey
- clientConfig.config
- clientConfig.loadedAt
- clientConfig.configUrl

Only save the key after a successful config load.
Do not save invalid keys.

Clear/reset:
Implement a clean reset function:
- remove saved client key
- remove cached config
- restore default app CSS variables/logos/config
- return app to default Cubes style/config

Suggested names:
- clearClientConfig()
- resetToDefaultClientConfig()

Recommended file naming:
Prefer client-config naming, not branding-only naming.

Suggested structure, adapted to existing project style:
src/client-config/client-config.types.ts
src/client-config/client-config.service.ts
src/client-config/client-config.storage.ts
src/client-config/client-config.state.ts
src/client-config/client-config.constants.ts

Required helpers:
- loadClientConfigByKey(clientKey)
- applyClientConfig(config)
- applyBranding(config.branding)
- getAuthTokenEndpoint()
- getApiBaseUrl(moduleKey)
- isModuleEnabled(moduleKey)
- getActiveClientConfig()
- clearClientConfig()
- resetToDefaultClientConfig()

TypeScript interfaces:
Create proper interfaces for ClientRuntimeConfig, BrandingConfig, BrandingColorTokens, BrandingAssets, BrandingFontConfig, and ResolvedClientRuntimeConfig.

Acceptance criteria:
1. First app screen is Client Runtime Configuration, before the existing Configuration page.
2. User can skip client key and continue to the existing Configuration page with default app config.
3. User can enter demo-client, fetch config, apply branding/client config, then continue to the existing Configuration page.
4. User can enter apqc and apply APQC config.
5. User can enter ef and apply EF config without breaking RTL/language behavior.
6. Invalid key such as "APQC Client" is rejected before fetch.
7. Unknown key falls back safely to default config.
8. Network/server failure does not crash the app.
9. Cached valid config can be used if refresh fails.
10. Clear/reset removes remote client config and restores defaults.
11. Existing Configuration page remains available and is not removed.
12. No client assets/config files are bundled inside the app.
13. No custom splash branding is implemented.

Before coding:
- Inspect the current project structure.
- Identify the existing first-launch flow, Configuration page, login page, theme handling, RTL/LTR handling, storage mechanism, and logo usage.
- Reuse existing patterns where possible.
- Then apply changes directly.
