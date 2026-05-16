# enginyears — Link in Bio

> **Electronics · DIY · Maker Projects**  
> A self-hosted, zero-dependency portfolio page that lives in your Instagram bio and connects every reel to its code.

---

## What this is

This is a single `index.html` file — no frameworks, no build step, no server needed. You drop it into a GitHub repository alongside a `projects.csv` spreadsheet, enable GitHub Pages, and paste the URL into your Instagram bio. Anyone who taps that link lands on a living portfolio of all your projects, each with direct links to the Instagram reel, the YouTube Short, and the specific GitHub repo for the code.

**The "database" is `projects.csv` — a plain spreadsheet you open in Excel or Google Sheets.** One row is one project. Adding a new project means typing a new row. Updating stats after checking Instagram Insights means changing three numbers. The page fetches and parses this file at load time using PapaParse, so the data and the presentation are cleanly separated. Even with 100+ rows, the spreadsheet stays easy to manage — you can sort by date, filter by tag, or search for a project in seconds using your spreadsheet app's built-in tools.

---

## File structure

```
your-repo/
├── index.html          ← Page layout, design, and render logic (rarely needs editing)
├── projects.csv        ← Your project database — edit this to add/update projects
├── thumbnails/         ← Your reel/video thumbnails go here
│   ├── train.jpg
│   ├── garden.jpg
│   └── ...
└── README.md
```

The philosophy here is that `index.html` is infrastructure — you set it up once and rarely touch it — while `projects.csv` is your living data that you update regularly.

---

## Setup guide

### Step 1 — Edit your profile in `index.html`

Open `index.html` and find the `const PROFILE = { ... }` block near the bottom. It's the only thing you'll ever need to change in the HTML file itself:

```javascript
const PROFILE = {
  name:       "enginyears",
  tagline:    "Electronics · DIY · Maker",
  bio:        "Your bio here...",
  avatar:     "",           // URL to your profile picture
  avatarEmoji:"⚡",         // Shown if no avatar URL is set
  instagram:  "https://instagram.com/enginyears.me",
  youtube:    "https://youtube.com/@enginyears",
  github:     "https://github.com/enginyears",
};
```

### Step 2 — Add your projects in `projects.csv`

Open `projects.csv` in Excel, Google Sheets, or any spreadsheet app. Each row is a project. The columns are described in the table below:

| Column | What to put | Example |
|---|---|---|
| `id` | A unique slug, no spaces | `live-train-dash` |
| `title` | Display name of the project | `Live Train Dashboard` |
| `description` | 1–2 sentences | `Real-time board for Naihati–Sealdah...` |
| `thumbnail` | Relative path or full URL | `thumbnails/train.jpg` |
| `thumbEmoji` | Fallback if no thumbnail | `🚂` |
| `tags` | Pipe-separated categories | `Raspberry Pi\|React\|Web` |
| `difficulty` | `beginner`, `intermediate`, or `advanced` | `intermediate` |
| `date` | Publish date | `2025-11-20` |
| `featured` | `true` makes the card span full width | `true` |
| `likes` | Approximate likes (update from Insights) | `312` |
| `views` | Approximate views | `5800` |
| `comments` | Approximate comments | `24` |
| `instagram` | Direct reel URL | `https://instagram.com/reel/XXXXX/` |
| `youtube` | YouTube Shorts URL | `https://youtube.com/shorts/XXXXX` |
| `yt_video` | Full YouTube video URL | `https://youtube.com/watch?v=XXXXX` |
| `github` | Specific repo URL for this project | `https://github.com/you/repo` |
| `components` | Pipe-separated hardware/software | `Raspberry Pi\|React\|CORS Proxy` |
| `notes` | Optional extra note shown in italic | `Part of a longer series` |

**Important — the pipe separator.** Because a single project can have multiple tags and multiple components, those two columns use `|` as a separator within a single cell. So if your project uses three tags, you type `ESP32|Home Automation|MQTT` in the `tags` cell. Excel and Google Sheets handle this fine — it's just text in a cell.

**Important — commas in descriptions.** If your description contains a comma (almost all of them will), your spreadsheet app will automatically wrap it in double quotes when you save as CSV. This is normal and correct behaviour. You never need to think about it.

To add a new project, just add a new row at the bottom of the spreadsheet. The page automatically sorts everything by date, so a new row with today's date will appear at the top of the grid regardless of where it sits in the CSV.

### Step 3 — Add thumbnails

Create a `thumbnails/` folder in the repo. Export a frame from your reel or take a screenshot, crop it to 16:9 (1280×720px is ideal), save as JPEG, and drag it into the folder. Then reference it in the CSV as `thumbnails/your-image.jpg`. If you don't have a thumbnail yet, just leave the `thumbnail` column blank — the `thumbEmoji` will display instead.

### Step 4 — Set up GoatCounter analytics

GoatCounter gives you a full visitor dashboard — page views, referrers, countries, device types, and custom link-click events — for free, with no cookies and no GDPR complications.

Sign up at [goatcounter.com](https://www.goatcounter.com) (about 90 seconds). Choose a site code such as `enginyears`. Your private dashboard will live at `https://enginyears.goatcounter.com`.

Then in `index.html`, find this line near the top and replace `YOUR_SITE_CODE`:

```html
data-goatcounter="https://YOUR_SITE_CODE.goatcounter.com/count"
```

That's everything GoatCounter needs. It automatically captures page views, referrer URLs, countries, and device types.

### Step 5 — Enable GitHub Pages

Go to your repository's **Settings → Pages**. Under "Source", choose your `main` branch and set the folder to `/ (root)`. Hit Save. GitHub will provide a URL like `https://yourusername.github.io` within a minute or two.

---

## Traffic source tracking — the `?ref=` system

The page automatically detects where a visitor came from by reading the URL parameter and the HTTP referrer, then logs it as a GoatCounter custom event. To get a precise breakdown, use a different URL in each platform:

| Platform | URL to use |
|---|---|
| Instagram bio | `https://yourusername.github.io?ref=instagram` |
| YouTube video description | `https://yourusername.github.io?ref=youtube` |
| GitHub profile README | `https://yourusername.github.io?ref=github` |
| Twitter / X | `https://yourusername.github.io?ref=twitter` |

In your GoatCounter dashboard you'll see custom events like `source/instagram` alongside your page views, giving you a clean split of which platform is actually driving traffic.

Every time someone clicks a Reel, Shorts, or Code button on a project card, that click also fires a GoatCounter event such as `click/live-train-dash/github`. This tells you not just who visits but which projects and platforms generate the most real engagement.

---

## Updating stats

The `likes`, `views`, and `comments` columns are intentionally manual. Once a week, open Instagram Insights for each reel, copy the numbers, update the rows in `projects.csv`, and push to GitHub. The stats strip at the top of the page (total views, total likes, categories) auto-calculates from these values and stays in sync automatically.

This also means you have a permanent offline record of your growth over time, which Instagram's own analytics do not preserve.

---

## Testing locally

Because `index.html` fetches `projects.csv` over HTTP, opening the HTML file directly from your desktop (`file://` protocol) will be blocked by the browser's security rules. To test locally, run a simple server in the repo folder:

```bash
# Python (comes pre-installed on macOS and most Linux systems)
python -m http.server

# Node.js alternative
npx serve .
```

Then open `http://localhost:8000` in your browser. On GitHub Pages, everything works automatically without this step.

---

## Managing the CSV at scale

The main advantage of a spreadsheet over a JavaScript array or JSON file becomes clear around 20–30 projects, and it pays dividends at 100+. In your spreadsheet app you can freeze the header row so column names are always visible, sort all projects by date or views in two clicks, filter to see only your ESP32 projects or only projects missing a GitHub link, and color-code rows by difficulty or category at a glance. None of this is possible when the data lives inside a JavaScript file.

On the page itself, the search bar and tag filters mean visitors never need to scroll through all 100 cards — they search for "ESP32" and see only the relevant subset immediately. If the grid ever starts to feel overwhelming at 150+ projects, adding pagination to `renderCards()` in `index.html` is a small, self-contained change that doesn't touch the CSV at all.

---

## Customisation reference

To change the accent colour, find `--accent: #00e5a0;` in the `:root` CSS block in `index.html` and replace it with any hex value. Every button, glow, badge, and border scales from that single variable.

To add a new filter tag, simply include it in any project's `tags` cell in the CSV. The filter bar generates itself from whatever tags exist in the data — there's no separate list to maintain.

The filter bar has a **collapse toggle** — tap the "Tags" button to hide or show it. When collapsed, if a filter other than "All" is active, a small label shows the active tag so the user always knows the grid is filtered. This is useful on mobile where vertical space is limited.

To pin a project at the top regardless of date, set `featured` to `true`. The featured card spans the full width of the grid and is always rendered before all others.

The "New" badge appears automatically on any project whose `date` is within the last 30 days, and disappears on day 31 without any action needed.

---

## License

MIT — feel free to fork, adapt, and use this for your own creator profile.
