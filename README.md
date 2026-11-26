# Asynchronous VLM-Supervised Selective Grasping


## Deploy
1. Create a repo (e.g. `vlm-selective-grasping`) and push these files to the default branch:
   ```
   index.html
   assets/
   ```
2. On GitHub: **Settings → Pages → Build and deployment → Source: Deploy from a branch**,
   pick your branch and `/ (root)`, Save.
3. The site goes live at `https://<user>.github.io/<repo>/` within a minute.

Preview locally: open `index.html`, or `python3 -m http.server` in this folder.

## Edit before publishing
- **Authors / affiliation** — in the hero (`.authors`, `.affil`). Current list is a placeholder.
- **Links** — the Paper / Code / Model / Dataset buttons and the resource cards all use `href="#"`.
  Point them at your arXiv, GitHub, and Hugging Face release pages.
- **BibTeX** — update venue/year in the `<pre id="bib">` block.
- **Figures** — in `assets/`; replace any with higher-res exports if you have them.

No build step, no dependencies — just HTML + the figure PNGs. Fonts load from Google Fonts;
colors adapt to light/dark automatically.
