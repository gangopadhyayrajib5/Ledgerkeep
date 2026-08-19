# Ledgerkeep — installing it on iPhone

## First, the honest bit about "APK"

An `.apk` is an Android package; iPhone will not open one. The iPhone equivalent is an `.ipa`, and it cannot be built here — it needs Xcode on a Mac, an Apple Developer account (US$99/year), a signing certificate, and either TestFlight or the App Store to reach anyone else's phone. Nothing in this conversation can produce a signed iOS binary.

What you have instead is a **Progressive Web App**. Installed, it gets a home-screen icon, launches full-screen with no browser chrome, and runs with the network switched off. On iOS the practical differences from a native app are three: no push notifications (hence the Calendar export on the Bills tab), no Face ID prompt (your passphrase does that job), and no App Store listing.

---

## Route 1 — Host it, then install (recommended)

You need any static host over HTTPS. GitHub Pages is free and takes about five minutes. Because every line of this app runs in the browser and it makes no outbound requests, hosting it exposes nothing about you — the host only ever serves the same public files to everyone.

1. Create a GitHub account, then a new **public** repository called `ledgerkeep`.
2. Upload all seven files from this folder into the repository root: `index.html`, `manifest.webmanifest`, `sw.js`, `icon-192.png`, `icon-512.png`, `icon-maskable-512.png`, `apple-touch-icon.png`.
3. Settings → Pages → Source: **Deploy from a branch**, branch `main`, folder `/ (root)`. Save.
4. Wait a minute or two, then open `https://<your-username>.github.io/ledgerkeep/` **in Safari** on your iPhone. It must be Safari; Chrome on iOS cannot install web apps.
5. Share button → **Add to Home Screen** → Add.

You now have a Ledgerkeep icon. Open it once while online so the service worker caches everything; after that it works in aeroplane mode.

To give it to another iPhone user, send them the URL. They install it and create their own vault with their own passphrase. Your data is not in the app — it is in your encrypted `.lkv` file — so there is nothing of yours for them to see.

## Route 2 — No hosting

Put `index.html` in iCloud Drive, open it from the Files app, and use it in Safari. Everything works except Add to Home Screen. Bookmark it instead.

---

## Where your data lives

- **On the phone:** an encrypted copy in the browser's local storage, written automatically as you type. Safari can clear this if the app goes unused for a long stretch, which is why the next line matters.
- **Your real backup:** tap **Save**, then choose iCloud Drive. That writes `Ledgerkeep-YYYY-MM-DD.lkv` — AES-256-GCM ciphertext. Apple stores and syncs it across your devices and cannot read it. Do this at the end of any session where you entered something you would hate to retype.
- **To move to another device:** open the app there, tap **Open vault file**, pick the `.lkv` from iCloud Drive, enter the passphrase.

Two rules with no exceptions: the passphrase is unrecoverable, and a `.lkv` file is only as private as the passphrase protecting it. Do not send anyone a `.lkv` unless you also intend to give them the passphrase.

---

## Monthly routine

1. Bank emails or you download the statement — CSV if offered, PDF otherwise.
2. Import tab → pick the file → check the column mapping → Import.
3. Bills tab → mark what you paid; each one writes itself into the ledger and rolls to its next date.
4. Metals & FX tab → type in today's gold and currency rates.
5. Save → iCloud Drive.

Set a recurring bill called "Reconcile accounts" with no amount, and the app will remind you to do this.

## PDF statements

The importer reads text-based PDFs directly, including right-aligned debit/credit/balance columns. It cannot read two kinds:

- **Scanned PDFs** (a photograph of a page). Nothing to extract. Ask the bank for CSV.
- **Password-protected PDFs**, which most Indian banks send. Open it with the password, then Print → Save as PDF. The copy has no password.

Always glance at the preview before importing — bank layouts vary enough that the column guess is a guess.
