# Copilot Instructions for Cortés López Lab Website

## Project Overview
This is a **Hugo static site** for the Cortés López Lab at Helmholtz Munich/LMU. The site is published to GitHub Pages and showcases research, team, publications, and recruitment.

- **Framework**: Hugo 0.128.0+
- **Theme**: Custom `cortes-lab` theme with Helmholtz Munich/LMU branding
- **Deployment**: GitHub Pages via GitHub Actions
- **DNS**: https://cortes-lopez-lab.github.io/

## Architecture & Key Directories

### Content Structure (`/content`)
- **`_index.md`**: Homepage – do NOT edit without understanding the hero section
- **`people/_index.md`**: Team page with `.people-grid` and `.person-card` components (embedded HTML)
- **`research/`, `publications/`, `news/`, `join/`, `contact/`**: Section pages using standard Hugo templates
- **Pattern**: All section pages use `title` frontmatter; homepage has `description` as well

### Theme (`/themes/cortes-lab`)
- **`layouts/index.html`**: Homepage-specific layout (hero, section cards, callout)
- **`layouts/_default/baseof.html`**: Base template (header, main, footer)
- **`layouts/_default/list.html` & `single.html`**: Standard content layouts
- **`partials/site-nav.html`, `site-footer.html`**: Reusable header/footer components
- **`static/css/style.css`**: Single unified stylesheet (~363 lines)

### Static Assets (`/static/images`)
- **`people/`**: Team member photos referenced in `people/_index.md`
- Naming convention: lowercase with hyphens (e.g., `mariela-cortes-lopez.jpg`)

## Design System & Styling

The theme uses **CSS custom properties** for a bold Helmholtz/LMU palette:

```css
--hm-pink: #ff506e      /* Primary accent */
--hm-purple: #69005f    /* Institutional color */
--lmu-green: #007e36    /* LMU branding */
--ink: #0a0f1c          /* Text */
--paper: #f4f2ee        /* Background tint */
--mist: #e7eef7         /* Subtle backgrounds */
```

**Component classes**:
- `.section-grid`, `.section-card`: Link cards on homepage
- `.people-grid`, `.person-card`, `.person-photo`: Team member layout
- `.hero`, `.hero-content`, `.hero-panel`, `.hero-actions`: Homepage hero
- `.btn.primary`, `.btn.ghost`: Button styles
- `.site-header`, `.site-main`, `.site-footer`: Layout containers

**Key CSS patterns**:
- Grid layout for responsive design
- Sticky header with backdrop blur
- Rounded font stack: `var(--font-rounded)` as primary
- `.reveal` class likely for animations (check animation rules if extending)

## Hugo Configuration & Content Structure

**`config.toml`** key settings:
- `baseURL`: Points to GitHub Pages URL (auto-managed by workflow)
- `theme: "cortes-lab"`: Custom theme name
- `unsafe = true` under `[markup.goldmark.renderer]`: Allows embedded HTML in Markdown (used in `people/_index.md`)
- `[menu.main]`: Navigation defined here with weights (10–60); add new sections by adding menu entries

**Content workflow**:
1. Create new section: Add folder in `/content/` with `_index.md`
2. Add to navigation: Add `[[menu.main]]` entry in `config.toml` with weight
3. Hugo auto-generates routes: `/research/`, `/people/`, etc.

## Build & Deployment

**Local development**:
```bash
hugo server          # Serve on localhost:1313
hugo                 # Build to /public
```

**CI/CD** (`.github/workflows/hugo.yml`):
- Triggers on pushes to `main` branch
- Installs Hugo 0.128.0 + Dart Sass
- Runs `hugo --minify --baseURL ${{ steps.pages.outputs.base_url }}/`
- Deploys `/public` to GitHub Pages
- **Note**: `npm ci` is called if `package-lock.json` exists (currently unused but ready for JS tooling)

## Common Patterns & Conventions

### Embedded HTML in Markdown
The `people/_index.md` embeds HTML directly for grid layout (enabled via `unsafe = true`). When editing:
- Use semantic HTML (`<article>`, `<img alt>`)
- Add ARIA labels to social links: `aria-label="Platform"`
- Maintain class names for CSS targeting (e.g., `.person-card`, `.person-photo`)

### Partial Usage
Use `{{ partial "site-nav.html" . }}` and `{{ partial "site-footer.html" . }}` in templates. Keep partials lightweight; complex logic belongs in content or config.

### URL Structure
- Use `relURL` filter: `{{ "css/style.css" | relURL }}` to handle baseURL correctly
- Content URLs: Use Hugo's automatic routing; `/content/people/_index.md` → `/people/`
- Asset paths: Root-relative (e.g., `/images/people/photo.jpg`)

## Team & Brand Context

- **Principal Investigator**: Dr. Mariela Cortés López
- **Research focus**: Single-cell RNA, long-read sequencing, pediatric disease, AI integration
- **Institutional partners**: Helmholtz Munich (ICB), LMU (Dr. von Hauner Children's Hospital), DZKJ
- **Social media**: Links to Bluesky, Twitter, GitHub (update in `people/_index.md` if needed)

## Common Tasks for AI Agents

1. **Add team member**: Edit `people/_index.md`, add `<article class="person-card">` block, upload photo to `/static/images/people/`
2. **Update research description**: Edit `content/research/_index.md`
3. **Add new section**: Create `/content/section/_index.md`, add menu entry in `config.toml` with weight
4. **Modify colors**: Update CSS custom properties in `style.css` `:root` block
5. **Fix broken links**: Check for mismatched `relURL` usage or outdated `/content/` paths
6. **Review deployment**: Check `.github/workflows/hugo.yml` for Hugo version or env changes

## Key Files Reference

| File | Purpose |
|------|---------|
| `config.toml` | Hugo config + site metadata + navigation |
| `themes/cortes-lab/static/css/style.css` | All styling (no SCSS used) |
| `themes/cortes-lab/layouts/index.html` | Homepage template |
| `themes/cortes-lab/layouts/_default/baseof.html` | Base HTML structure |
| `content/people/_index.md` | Team page with embedded HTML |
| `.github/workflows/hugo.yml` | Deployment pipeline |

## Notes for Future Development

- **Currently unused**: `npm ci` hook in workflow suggests potential for JS tooling (tailwind, build assets, etc.)
- **No submodules**: Theme is in version control, not a Git submodule
- **Single CSS file**: Consider if scale increases (160+ team members, complex interactions)
- **Font stack**: Relies on system fonts; consider bundling custom fonts for consistency
