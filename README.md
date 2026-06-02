# Cubes Hub Client Runtime Config Assets

Generated: 2026-06-01T23:27:22Z

This ZIP contains complete testing configs for the new non-backward-compatible CRC architecture.

## Included clients

- demo-client
- ef
- apqc

## URL pattern

After deployment, these should work:

```txt
/clients/demo-client/config/client.config.json
/clients/ef/config/client.config.json
/clients/apqc/config/client.config.json
```

Example:

```txt
https://<your-cloudflare-domain>/clients/demo-client/config/client.config.json
```

## New architecture

The old fields were intentionally removed:

```txt
apis
modules
license.allowedModules
modules.*.enabled
modules.*.apiBaseUrlRef
```

The new fields are:

```txt
auth
license.licenseToken
license.devUnsignedPayload      # dev/test only
moduleConfig.audit.apiBaseUrl
moduleConfig.audit.capabilities
```

## License

The configs use:

```json
"licenseToken": "dev-unsigned"
```

This is for development/testing only. Production should replace this with a real signed token and ignore/remove `devUnsignedPayload`.

## URLs

The configs preserve the testing URLs from the uploaded demo-client config:

```txt
Auth:
https://qc09.cubescpm.com/Cubes/Auth/Token

Audit:
https://qc02.cubescpm.com/AuditManagement/BE/api
```

For EF/APQC, the same testing backend URLs are used because no separate EF/APQC backend URLs were provided in the chat.
