# Reuse wx-kit (Electron + TypeScript) instead of building a fresh Python desktop app

wx-kit (monkeychen/wx-kit) already ships a desktop GUI with QR login, batch crawl, whole-article download (md/html/pdf/meta), and a local library. Building the same in Python would re-spend months of edge-case work (message-type parsing, list-API quirks, paywall/unavailable handling) — reuse and extend it instead.
