# khazna-privacy

The published privacy policy for **Khazna (خزنة)**, a pocket vault for links, notes and
checklists (`com.mohamedgado.khazna`).

- **Live policy:** https://vette1123.github.io/khazna-privacy/
- **App on Google Play:** in closed testing under `com.mohamedgado.khazna` — this URL is
  what the listing and the Data safety form point at

The URL is referenced from inside the app (`Settings → Privacy policy`) and from the store
listing, so it must always resolve.

## What Khazna is

One input takes anything. Paste a URL and it becomes a link, type a few lines and it
becomes a note, write a list and it becomes a checklist — and converting between the three
never loses what you wrote. Arabic-first with English, dark and light, Android-first.

No accounts, no ads, no backend of ours. Everything you save lives in the app's own
storage on the device.

## What lives in this repo

| File | Purpose |
|---|---|
| `index.html` | The policy. Arabic and English, both in the DOM, one visible at a time |
| `404.html` | Meta-refresh to the policy, so no old or mistyped link dead-ends |
| `robots.txt` | Allow all, points at the sitemap |
| `sitemap.xml` | The one real page |

There is no account-deletion page, and the stores do not require one: the app has no
accounts and we hold no copy of anyone's data. Deletion is covered by section 14 of the
policy (uninstall, or clear app storage).

## Why a separate repo

A privacy policy has to be reachable permanently and fixable in seconds without shipping
an app build, so it lives here, served free by GitHub Pages, with the git history as its
change log. It is also the only public artefact of the app, which is why it is written to
be read rather than to be complied with.

## How the page works

- **Self-contained.** No web fonts, no CDN, no scripts from other hosts, no cookies, no
  analytics. A privacy page should not be the thing that tracks you.
- **Bilingual, both in the DOM.** Arabic and English ship in the same document, so
  crawlers and reader modes see the full policy in both languages. The toggle only flips
  visibility plus `lang` and `dir`, and stores the choice in `localStorage`
  (`khazna-privacy-lang`).
- **Language resolution order:** an `#en-*` or `#ar-*` section anchor wins, then the
  stored choice, then the browser language, then English.
- **Light and dark** via `prefers-color-scheme`, using the app's own charcoal and brass
  tokens.
- **Print styles** so "save as PDF" produces a plain document with URLs spelled out.
- **JSON-LD** `PrivacyPolicy` block for search engines.
- **The mark** — the three slips, one per kind the app holds — is inline SVG in the header
  and a `data:` URI favicon. No image file to 404.

## What the policy covers

Sections 1 to 19: scope, the no-accounts and no-server posture, on-device storage, the two
requests the app makes, usage measurement in detail, how things get into the vault,
opening a saved link, reminders, app lock and screen privacy, backup and restore, updates,
permissions, security, retention and deletion, rights, children, third parties, changes,
contact.

### Outbound hosts disclosed (keep this in sync with the app)

| Host | Why | Switchable |
|---|---|---|
| The site of a link the user saved | Read its `<title>` so the item is readable | Yes — Settings → Links |
| `eu.i.posthog.com` | Anonymous usage measurement, EU region | Yes — Settings → Privacy |
| `play.google.com` / App Store | Installation and updates, under their own policies | No |

There is no server of ours anywhere in that table, and that is the point of it.

`u.expo.dev` was in that table until 22 October 2026 and is not any more: the shipped build
has `expo.modules.updates.ENABLED=false` and no update URL, so the app never asks Expo's
update service for anything. The app ships as a fixed binary from the store. If that ever
changes, section 11 and the table above change in the same commit — the same week, not the
same year.

### The claim the app makes about analytics, and why it is checkable

Section 5 says an analytics value may be a number, a boolean, or one of a fixed set of
words the app chose — never free text. That is not a promise in prose; it is a type
constraint in `lib/analytics/events.ts` in the app repo, enforced by
`__tests__/analytics/no-content.test.ts`, which fails the build if a bare `string` ever
appears in an event payload. Titles, URLs, note bodies, checklist labels and search
queries are all strings, so none of them can reach an event without deleting that test.

If that ever stops being true, section 5 is wrong and has to change in the same commit.

### Permissions disclosed

Section 12 lists the complete set the released Android build holds: internet and network
state, notifications (asked only at the first reminder), biometric authentication (only
with app lock on), vibrate, boot-completed plus wake lock so a reminder survives a
restart, screen-capture detection on Android 14 and later (the screen-privacy feature
registers an observer with the operating system, and Android grants no observer to an app
without the permission), and install launcher shortcuts. It also states what is explicitly
blocked in `app.json` and therefore cannot be in a released build: microphone, camera,
fine and coarse location, activity recognition, reading images or video from the library,
and the badge permissions a notification library would use to put a number on your icon.

The app repo asserts that set at build time: `scripts/build-release-aab.ps1` fails the
build if the merged manifest holds any permission outside the list, so a permission added
by a library cannot reach a release without section 12 changing with it.

### The widget, and the lock

Section 3 says the home-screen widget shows up to three recent items by title, and draws
nothing while the vault is locked. The launcher cannot ask whether the vault is locked — it
is another process — so the app hands it nothing to draw instead. That is checked in the
app repo by `__tests__/widget.test.ts`.

## Maintaining it

There is no build step. Edit the HTML, commit, push. GitHub Pages serves `main`.

When the app changes, the policy changes in the same session as the feature, not later:

1. A new outbound host, SDK or third party goes into the section 17 table plus the table
   above, **in both languages**.
2. A new permission goes into section 12, in both languages.
3. A new on-device store goes into section 3.
4. A new analytics field goes into the section 5 table — and if it could carry anything a
   user typed, it does not get added at all.
5. Bump the `Last updated` date and the covered app version in both language blocks, the
   `dateModified` in the JSON-LD, and `lastmod` in `sitemap.xml`.

The app repo holds the matching contract at `docs/PRIVACY.md`. That file and this page say
the same things to two audiences; if they disagree, this page is what the user was shown,
so this page wins and the app changes.

## Google Play

- Privacy policy URL: `https://vette1123.github.io/khazna-privacy/`
- Data deletion URL: not applicable, no accounts

### Data safety

The form has to answer for the binary that is actually on the store, and one thing about
that binary is decided at build time rather than in the source: `EXPO_PUBLIC_POSTHOG_KEY`
is injected into the build and is **not** in the repo. Without it, the app never
constructs an analytics client and collects nothing at all.

The app repo's release build prints which of the two it is, so the answer is never a
guess:

- **Build without the key → "Does your app collect or share any of the required user
  data types?" → NO.** Nothing is collected, nothing is shared, and the encryption and
  deletion questions are not applicable. The vault is on-device; Settings → Erase removes
  it.
- **Build with the key → YES**, and declare exactly this and nothing more: app activity
  and diagnostics collected for analytics, tied to a random per-install id, not linked to
  identity, not shared with third parties, encrypted in transit, and removable by
  switching analytics off. No data is collected from the vault itself.

All traffic is over TLS either way.

## Contact

boogado@yahoo.com
