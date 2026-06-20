---
name: Deriv bot template init flow
description: Two-level loading gating and fallback timer pattern for when WS is unavailable
---

**Two-level loading in app-root.tsx → app-content.jsx:**
1. `AppRoot` shows `AppRootLoader` ("Loading...") until `api_base.init()` completes → then renders `AppContent`
2. `AppContent` shows its own ChunkLoader ("Initializing Deriv Bot account...") while `is_loading=true`
   - `is_loading` goes false when `connectionStatus === OPENED` → `is_api_initialized=true` → `changeActiveSymbolLoadingState()` → `active_symbols.retrieveActiveSymbols()` resolves

**Lazy import causes Suspense stall under broken HMR:** `app-root.tsx` originally used `React.lazy` for AppContent. When rsbuild's HMR WebSocket fails (common in Replit), the lazy chunk update stalls and the Suspense boundary shows "Loading..." forever. Fix: convert to a direct eager import (no Suspense wrapper).

**Fallback timers added to AppContent:**
- 8s: forces `is_api_initialized=true` if WS never opens
- `Promise.race([symbolsPromise, 10s fallback])` in `retrieveActiveSymbols` wrapper
- 25s: ultimate `setIsLoading(false)` safety net (handles `trading_times.initialise()` hangs)

**Why:** `trading_times.initialise()` and `api_base.getActiveSymbols()` both hang indefinitely when there is no WS connection, so explicit time-bounded Promise.race guards are essential.
