---
name: Deriv WS URL fix
description: The V2 staging API rejects unregistered domains; use the V3 classic API for any-domain access
---

The new Deriv V2 API (`staging-api.derivws.com/trading/v1/options/ws/public`) returns HTTP 403 for any domain not registered with Deriv. This causes the entire app to hang on "Initializing Deriv Bot account..." forever.

**Fix:** Change `WS_SERVERS` in `src/components/shared/utils/config/config.ts` to use the classic Deriv V3 API:
```ts
const DERIV_WS_APP_ID = process.env.DERIV_WS_APP_ID || '36300';
export const WS_SERVERS = {
    STAGING: `wss://ws.binaryws.com/websockets/v3?app_id=${DERIV_WS_APP_ID}`,
    PRODUCTION: `wss://ws.binaryws.com/websockets/v3?app_id=${DERIV_WS_APP_ID}`,
};
```

**Why:** `ws.binaryws.com` (V3 API) accepts connections from any domain. `DerivAPIBasic` is already designed for V3. The numeric `app_id` is separate from the OAuth `CLIENT_ID` (which is alphanumeric for the new auth2 system).

**How to apply:** Any time this project needs a working WS connection in development/Replit, use `ws.binaryws.com` with a numeric app_id. For production white-label, the user's own registered Deriv app_id should replace `36300`.
