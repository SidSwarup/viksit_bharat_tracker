# Countdown to Viksit Bharat

A public-good website tracking India's position on 15 major global indices, with 15-year trends and per-index deep dives. It is a static site: three files, no server, no database, no running costs beyond an optional domain name.

## What's in this folder

| File | What it is | How often you touch it |
|---|---|---|
| `index.html` | The whole website (layout, charts, logic). | Almost never. |
| `data.json` | Every number and every paragraph shown on the site. | Once a month or so, when a new index is released. |
| `README.md` | This file. | Never (it isn't published). |

The design principle: **all content lives in `data.json`. You update data, never code.** The charts redraw themselves from whatever numbers are in the JSON.

---

## Part 1 — Put it online (one time, ~20 minutes)

You do not need any coding tools, Supabase, or a database. Just a free GitHub account.

1. **Create a GitHub account** — go to github.com and sign up (free).
2. **Create a repository** — click **New**, name it `viksit-bharat-tracker`, choose **Public**, click **Create**.
3. **Upload the files** — on the repo page click **Add file > Upload files**, drag in `index.html` and `data.json` (not the README unless you want to), then **Commit changes**.
4. **Turn on GitHub Pages** — go to **Settings > Pages**, under *Source* pick the `main` branch and `/ (root)`, click **Save**. Wait ~1 minute. Your site is live at `https://<your-username>.github.io/viksit-bharat-tracker/`.

That address is already a real, shareable, public website. Everything below is optional.

### Optional — use your own domain (~₹500–1,500/year)

5. Buy a domain (e.g. `viksitbharat2047.in`) from Namecheap, GoDaddy or Cloudflare.
6. In **Settings > Pages > Custom domain**, type your domain and save. GitHub shows you the DNS records to add.
7. At your domain provider, add those records (four `A` records for the apex domain, or one `CNAME` pointing to `<your-username>.github.io`). Propagation takes minutes to a few hours. Tick **Enforce HTTPS** once it appears.

---

## Part 2 — Update when a new index is released

Each index publishes roughly once a year. Updating one takes about two minutes.

1. On GitHub, open `data.json` and click the **pencil (Edit)** icon.
2. Find the index (search for its `"id"`, e.g. `"cpi"`).
3. **Add the new year to `series`** — e.g. add `[2027, 88]` to the end of the array.
4. **Update `current`** — the `rank`, `of` and `year`.
5. **Update `above` and `below`** — the five countries on each side, from the new report.
6. Refresh the `trend` / `trendLabel` if the direction changed, and edit `conclusion` if the story changed.
7. Update `meta.lastUpdated` at the top to today's date.
8. Scroll down, **Commit changes**. The site republishes automatically in about a minute.

### Doing it with Claude (recommended)

Paste `data.json` into Claude with a message like:

> Here's my data.json. The 2027 Corruption Perceptions Index is out at <link>. Update the `cpi` entry: add 2027 to the series, update current rank/of/year, refresh the five countries above and below, and revise the conclusion if the trend changed. Give me back only the updated `cpi` block.

Then paste the block back in, replacing the old one. **Always check the new number against the original report before committing** — that verification is the whole value of the site.

### One number can auto-update

Only **GDP per capita** has a clean machine-readable source (IMF WEO / World Bank API). The other 14 indices publish as PDFs or web pages with no data feed, so they are best updated by hand with a quick source-check. Scraping them is fragile and risks silently publishing a wrong number, which is the fastest way to lose credibility on a sensitive topic.

---

## Part 3 — Add a whole new index

Copy any existing block inside `"indices": [ ... ]` and change the fields. Required fields:

```json
{
  "id": "shortcode",                     // unique, lowercase, no spaces
  "name": "Full Index Name",
  "short": "Sidebar Label",
  "src": "Publisher",
  "link": "https://official-source-url",
  "current": { "rank": "88", "of": "180", "year": 2027 },
  "trend": "down",                       // "up" | "down" | "mixed"
  "trendLabel": "Declining",
  "dir": "lower",                        // "lower" = lower rank is better; "higher" = higher value is better
  "unit": "rank",
  "tblUnit": "Rank (2027)",
  "above": [["Country", "84"], ["Country", "85"], ["Country", "86"], ["Country", "87"], ["Country", "87"]],
  "below": [["Country", "89"], ["Country", "90"], ["Country", "91"], ["Country", "92"], ["Country", "95"]],
  "series": [[2015, 80], [2016, 82], [2027, 88]],
  "measures": "One or two sentences on what the index measures.",
  "conclusion": "A paragraph on the trend and what drives it."
}
```

Optional fields: `"approx": true` (adds an "≈ approximate" note to the chart), and `"tblSelf": "$2,813"` (shows a custom value for India's row in the table instead of the rank — used for GDP).

The overview cards and the left-hand tabs generate themselves, so a new block automatically appears everywhere. No limit of 15; add as many as you like.

---

## Part 4 — Annual release calendar

Rough months each index publishes, so you know when to check. Dates drift year to year — confirm on the publisher's site.

| Month | Index | Publisher |
|---|---|---|
| Jan / Feb | Corruption Perceptions Index | Transparency International |
| Feb / Mar | Freedom in the World | Freedom House |
| Mar | Liberal Democracy (V-Dem) | V-Dem Institute |
| Mar | World Happiness Report | UN SDSN |
| Apr / Oct | GDP per capita (WEO, twice yearly) | IMF |
| May | World Press Freedom Index | RSF |
| Jun | Global Peace Index | IEP |
| Jun | Global Gender Gap Index | World Economic Forum |
| Jun | Henley Passport Index (updated through year) | Henley & Partners |
| Sep | Global Innovation Index | WIPO |
| Oct | Global Hunger Index | Concern / Welthungerhilfe |
| Oct / Nov | Rule of Law Index | World Justice Project |
| ~ May (biennial) | Environmental Performance Index | Yale / Columbia |
| Annual (report lag) | Human Development Index | UNDP |
| Jan (Democracy Index) | Economist Intelligence Unit | EIU |

---

## Notes on running it as a public good

- **Transparency:** because the repo is public, anyone can see exactly where every number came from, and every edit is a dated, visible commit — a permanent public changelog.
- **Corrections:** invite them. Add a line to the footer or an About page with a contact email, and let people flag errors. On GitHub, knowledgeable users can even propose fixes as pull requests you approve.
- **Framing:** the data belongs to the publishers, not to you — you're presenting it, not asserting it. The footer already says this. Keep conclusions sourced and even-handed; the site is strongest when it simply shows the numbers and lets readers judge.
- **The data download** link in the footer serves `data.json` directly, which is what makes this a genuine open-data resource rather than just a webpage.

---

## Testing locally (optional)

Opening `index.html` directly from your computer (double-clicking) will show a load error, because browsers block a page from reading a local data file for security. Two easy fixes:

- Just upload to GitHub and test the live site, **or**
- Run a tiny local server: open a terminal in this folder and run `python3 -m http.server 8000`, then visit `http://localhost:8000` in your browser.
