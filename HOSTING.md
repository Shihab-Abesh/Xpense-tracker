# Hosting Xpense yourself

The app now runs in two modes and picks one automatically.

| | Served by Apps Script | Hosted on GitHub Pages |
|---|---|---|
| URL | long Google URL | `yourname.github.io/xpense` |
| Who can reach it | only your Google account | anyone with the URL **and** your passcode |
| Updating the screen | Deploy → New version | `git push` |
| Installs as an app | "Add to Home screen" | proper PWA with its own icon |
| Opens offline | no | yes, the shell is cached |

You do not have to choose permanently. The same `index.html` works both ways.

---

## Read this before you switch

Hosting the page outside Google means your phone talks to the script over the
open internet. For that to work, the deployment has to be set to **Anyone**,
not "Only myself". Google will no longer be the thing standing between your
data and a stranger.

What replaces it is a passcode you set yourself. Every request carries it, and
the script rejects anything without it, then locks the API for an hour after
ten wrong guesses.

That is genuinely fine for personal expense data, on two conditions:

1. **Make the passcode long.** Twelve or more characters, not `1234`. It is the
   only thing protecting your data now.
2. **Never commit it.** It lives in `Code.gs` (which stays private in your Apps
   Script project) and in your phone's local storage. It must not appear in the
   repo, in `index.html`, or in a config file.

If that trade sounds like more risk than the nicer URL is worth, stay on the
Apps Script URL. It is the more private option and it costs you nothing.

---

## Step 1: update the script

In the Apps Script editor, open `Code.gs` and change one line near the top:

```javascript
const API_TOKEN = 'change-this-to-a-long-private-passcode';
```

Pick something long and private. Save.

## Step 2: redeploy so the outside world can reach it

**Deploy → Manage deployments → pencil icon**

- Version: **New version**
- Execute as: **Me**
- Who has access: **Anyone**

Deploy, then copy the **Web app URL**. It ends in `/exec`.

> "Anyone" here means anyone who has that exact URL and passes your passcode
> check. It does not list your app anywhere or make the sheet public.

## Step 3: put the files in your repo

Copy these into the repo root:

```
index.html
manifest.webmanifest
sw.js
icon-192.png
icon-512.png
icon-maskable-512.png
```

Commit and push.

```bash
git add .
git commit -m "Host Xpense as a static PWA"
git push
```

## Step 4: turn on GitHub Pages

Repo → **Settings → Pages**

- Source: **Deploy from a branch**
- Branch: **main**, folder: **/ (root)**

Save. A minute later the site is live at `https://YOURNAME.github.io/REPO/`.

## Step 5: connect your phone

Open that URL on the phone. You get a Connect screen asking for two things:

- the **Web app URL** from step 2
- the **passcode** from step 1

Both are stored on that phone only. Tap Connect and the app loads.

Then **⋮ → Install app** (or Add to Home screen). It installs with its own icon
and opens without browser chrome.

To change either value later, tap the **⚙** next to the Xpense logo.

---

## Updating it later

- **Changed `index.html`?** Push to GitHub. Also bump `CACHE = 'xpense-v3'` in
  `sw.js` to something new, otherwise phones keep serving the cached copy.
- **Changed `Code.gs`?** Deploy → Manage deployments → New version, as always.

---

## Other free hosts

GitHub Pages is the least work because your repo is already there. These are
the alternatives worth knowing:

**Cloudflare Pages** is the one I would pick if you want it fast in Dhaka.
Connect the same GitHub repo, it deploys on every push, and Cloudflare has
servers much closer to you than GitHub's. Free tier is generous, and custom
domains cost nothing extra. Slightly more setup than GitHub Pages, better result.

**Netlify** and **Vercel** work the same way and are equally free for this.
No real advantage over the other two for a static page this small.

### If you want a nicer address than `yourname.github.io/xpense`

- **`is-a.dev`** gives developers a free subdomain like `abesh.is-a.dev`. You
  open a pull request on their repo with a small JSON file. Free forever, takes
  a day or two to merge.
- **`js.org`** does the same for JavaScript projects: `xpense.js.org`.
- **A real domain** is cheap if you would rather not wait. `.xyz` domains often
  run one or two dollars for the first year at Porkbun or Namecheap. Cloudflare
  Registrar sells at wholesale with no markup after that.

Avoid Freenom and the other "free .tk/.ml" providers. They have a long history
of reclaiming domains without warning.
