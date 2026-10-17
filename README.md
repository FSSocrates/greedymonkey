# Greedymonkey

A [Violentmonkey](https://github.com/violentmonkey/violentmonkey) fork that removes the `GM_*` ceiling. `@grant` proxies to the real WebExtension API — no companion extension, no stub, no limit.

## Why

Standard userscript managers (Tampermonkey, Violentmonkey, Greasemonkey) cap scripts at `GM_*` helpers. If a script needs `browser.webRequest` or `browser.debugger`, you have to install a separate extension and coordinate between them.

Greedymonkey eliminates that split. The manager *is* the extension. It ships with all permissions and `<all_urls>` host access. When you write `@grant browser.webRequest`, the script gets the actual API.

## What changed from Violentmonkey

| | Violentmonkey | Greedymonkey |
|---|---|---|
| `@grant GM_setValue` | ✅ | ✅ |
| `@grant browser.tabs` | ❌ (stub or ignored) | ✅ (real API) |
| `@grant browser.webRequest` | ❌ | ✅ |
| `@grant browser.debugger` | ❌ | ✅ |
| `@grant browser.proxy` | ❌ | ✅ |
| `@grant browser.nativeMessaging` | ❌ | ✅ |
| Permission prompts | n/a | None — all granted at install |

Everything else — the dashboard, editor, import/export, sync, `@require`, `@resource`, `@connect`, auto-update, script ordering, sourcing from Greasy Fork / GitHub / URL / paste / file — is inherited from Violentmonkey unchanged.

## Usage

```js
// ==UserScript==
// @name         Intercept and log requests
// @match        *://*.example.com/*
// @grant        browser.webRequest
// @grant        browser.storage
// @grant        GM_setValue
// ==/UserScript==

browser.webRequest.onBeforeRequest.addListener(
  (details) => {
    console.log('Intercepted:', details.url);
    // Modify, block, log — your call.
  },
  { urls: ['*://*.example.com/*'] }
);

browser.storage.local.set({ lastRun: Date.now() });
```

That's it. The `@grant` line is the only declaration you need. No manifest, no companion extension, no bridge.

## Install

Build from source and load as an unpacked extension:

```bash
pnpm install
pnpm run build
# Load dist/ via about:debugging (Firefox) or chrome://extensions (Chrome/Edge)
```

> **Note:** This extension requests all WebExtension permissions and `<all_urls>`. Any script you install can use any API it declares via `@grant`. Use a dedicated profile if you're running untrusted scripts.

## Scope

This is a **personal tool**. It is not designed for:

- Public distribution (AMO, Chrome Web Store)
- Replacing a full extension for production use

The trust model is: **you choose what to install, you read the code, you own the machine.**

## License

MIT (inherited from Violentmonkey).

## Credits

Forked from [Violentmonkey](https://github.com/violentmonkey/violentmonkey) by <a href="https://github.com/daijiarui">daijiarui</a> and contributors.   
