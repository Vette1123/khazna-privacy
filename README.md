# khazna-privacy

The published privacy policy for **Khazna (خزنة)**, a pocket vault for links, notes and
checklists (`com.khazna.vault`).

- **Live policy:** https://vette1123.github.io/khazna-privacy/
- **App on Google Play:** not listed yet — this URL goes in the listing and the Data
  safety form when it is

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
| `u.expo.dev` | Over-the-air JS updates (platform, not a feature) | No |
| `play.google.com` / App Store | Installation and updates, under their own policies | No |

There is no server of ours anywhere in that table, and that is the point of it.

### The claim the app makes about analytics, and why it is checkable

Section 5 says an analytics value may be a number, a boolean, or one of a fixed set of
words the app chose — never free text. That is not a promise in prose; it is a type
constraint in `lib/analytics/events.ts` in the app repo, enforced by
`__tests__/analytics/no-content.test.ts`, which fails the build if a bare `string` ever
appears in an event payload. Titles, URLs, note bodies, checklist labels and search
queries are all strings, so none of them can reach an event without deleting that test.

If that ever stops being true, section 5 is wrong and has to change in the same commit.

### Permissions disclosed

Section 12 lists internet and network state, notifications (asked only at the first
reminder), biometric authentication (only with app lock on), vibrate, and boot-completed
plus wake lock so a reminder survives a restart. It also states what is explicitly blocked
in `app.json` and therefore cannot be in a released build: microphone, camera, fine and
coarse location, activity recognition, and reading images or video from the library.

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

Data Safety should declare: app activity and diagnostics collected for analytics, tied to
a random per-install id, not linked to identity, and with an in-app opt-out; no data
shared with third parties; no data collected from the vault itself. All traffic is over
TLS.

## Contact

boogado@yahoo.com
