# Project Summary — Pensiones España (Interactive Web)

> Handoff document for the next AI agent / developer. Captures the full context of what
> this project is, what was built, the data decisions made, and what's left to do.

## 1. What this project is

A single-file interactive website (`index.html`) that explains the **viability of the
Spanish Social Security / pension system** (la viabilidad de la Seguridad Social en España).

It is written in **Spanish**, targets a general audience, and uses storytelling + interactive
charts to explain how the system went from solid to structurally challenged.

- **Stack:** plain HTML + CSS + vanilla JS, no build step. All in one `index.html`.
- **Charts:** [Chart.js 4.4.4](https://cdn.jsdelivr.net/npm/chart.js@4.4.4) + the
  [chartjs-plugin-annotation 3.0.1](https://cdn.jsdelivr.net/npm/chartjs-plugin-annotation@3.0.1)
  plugin, both loaded via CDN.
- **Design:** dark theme, gradient hero, fixed nav dots on the right, scroll-based section
  highlighting via `IntersectionObserver`. CSS variables defined in `:root`.

## 2. Hosting

- Hosted on **GitHub Pages**.
- Repo: `https://github.com/bucanero2010/pensiones-espana`
- Live URL: `https://bucanero2010.github.io/pensiones-espana/`
- Pages config: Deploy from a branch → `main` → `/ (root)`. **Custom domain left empty.**
  (User initially pasted the URL into the Custom domain field by mistake — that field must
  stay blank.)
- Git author configured locally for this repo only: `Sebastian Linares <a20160299@pucp.edu.pe>`.

## 3. Content structure (chapters)

The page is organized into sections, each with a nav dot:

1. **Hero** — title + 4 headline stats (10.5M pensions, 14.336M€ monthly payroll, 2.32
   contributors/pensioner, 84yr life expectancy).
2. **Orígenes del Sistema** — timeline 1900→1995 (INP 1908, Retiro Obrero 1919, Ley de Bases
   1963, system live 1967, Constitution art. 41 1978, Pacto de Toledo 1995). Explains the
   pay-as-you-go (reparto) principle.
3. **La Revolución Demográfica** — natality collapse chart, life expectancy chart, population
   pyramid chart, European comparison table.
4. **Anatomía del Sistema** — contributors/pensioner ratio, pension spending (% GDP), pension
   vs salary, **real growth indexed chart (NEW)**, Reserve Fund ("hucha") depletion.
5. **Las Crisis y las Reformas** — timeline of 2008 crisis + 2011/2013/2021-23 reforms, deficit chart.
6. **La Ola del Baby Boom** — births 1950-2000 chart (1958-1977 highlighted), retirement
   calendar table, ratio projection to 2050.
7. **Simulador Interactivo** — 5 sliders (retirement age, fertility, immigration, employment
   rate, contribution rate) → live computed 2050 ratio + verdict.
8. **¿Qué Nos Espera?** — reform options (more income / less spending / structural), central
   dilemma block, GDP spending projection to 2070.

(Footer cites sources: Seguridad Social, INE, Eurostat, BBVA Research, Fedea, IESE, AIReF.)

## 4. Charts inventory (12 total, all Chart.js)

| ID | Canvas | What it shows |
|----|--------|---------------|
| 1 | `chartNatalidad` | Birth rate (‰) + fertility (children/woman), 1960-2024, 2.1 replacement line |
| 2 | `chartEsperanza` | Life expectancy + expected years drawing pension, 1960-2024, age-65 line |
| 3 | `chartPiramide` | Age structure % for 1960 / 1990 / 2025 |
| 4 | `chartRatio` | Contributors per pensioner 1970-2026, 2.5 sustainability line |
| 5 | `chartGasto` | Pension spending as % of GDP, 1995-2025 |
| 6 | `chartPensionMedia` | Avg retirement pension vs avg gross salary (€/month) |
| 6B | `chartRealGrowth` | **NEW** — pension vs salary REAL growth, indexed 2006=100, CPI-adjusted |
| 7 | `chartHucha` | Reserve Fund balance (B€), 2000-2023 |
| 8 | `chartDeficit` | System balance (B€), 2008-2025 |
| 9 | `chartBabyBoom` | Births 1950-2000, 1958-1977 boom highlighted |
| 10 | `chartProyeccion` | Ratio projection 2010-2050 (real / base / optimistic scenarios) |
| 11 | `chartFuturo` | GDP spending projection 2020-2070 (3 reform scenarios) |

## 5. Key data points used (sourced via web search, 2024-2026 figures)

- Contributors per pensioner: **2.32** (June 2024); historical low 2.29 (2013); ~4.2 in 1970.
- Pension payroll: record **14,336M€/month** (April 2026); ~10.5M contributory pensions, ~9.5M people.
- Avg retirement pension: **~1,570€/month** (2026); first crossed 1,500€ in early 2025.
- Avg gross salary (INE): **2,386€/month** (2024), 2,273€ (2023).
- Fertility rate: **1.10** children/woman (2024); birth rate 6.49‰; 318,005 births in 2024.
- Life expectancy: **84.0 years** (2024); was 69.1 in 1960 (+~15 years).
- Pension spending: **13.1% of GDP** (2023), ~30% of all public spending; projected up to 17-18% by 2050.
- Reserve Fund: peaked **66,815M€ (2011)** → ~2,150M€ (2023), ~97% depleted.
- Baby boom: **~14M births 1958-1977** (>650k/year); starts retiring ~2023-2025, peak 2030-2045.
- Dependency ratio: 33% today → ~57% by 2050. Ratio projected to fall to **1.6** by 2050.
- System in structural deficit since **2010**.

## 6. Important implementation notes / gotchas

- **JS decimal bug fixed:** an early version wrote numbers like `1.000` meaning 1,000 — but in
  JS `1.000 === 1`. All thousands values are now plain integers (e.g. `1002`, `2386`). Watch
  for this if adding new data.
- **Mobile responsiveness** was a major focus. `const isMobile = window.innerWidth < 768;` is
  computed once near the top of the `<script>` block and drives per-chart options:
  - smaller `pointRadius`, compact legend `boxWidth`, abbreviated labels, smaller fonts
  - `aspectRatio` ~0.9-1.3 on mobile (taller/squarer) vs `2` on desktop, so charts get more
    vertical space in portrait
  - axis titles hidden on mobile (`display: !isMobile`)
  - dense charts (baby boom, hucha, deficit) use `autoSkip` + `maxTicksLimit` to avoid label overlap
  - CSS: `.chart-container canvas { max-height: none; }` on mobile (removed old 300px cap)
- `isMobile` is evaluated at load only — it does NOT re-evaluate on resize/orientation change.
  A future improvement would be to re-render charts on resize.
- The simulator (`updateSimulator()`) uses a **simplified illustrative model**, not an actuarial
  one. It's meant to convey intuition, not precise forecasts.

## 7. Git history (chronological)

1. `feat: Add interactive web about Spanish Social Security viability` — initial single-page site.
2. `fix: Improve mobile charts and correct pension/salary data` — natality/life-expectancy mobile
   tuning + fixed pension/salary data (the JS decimal bug + INE salary figures).
3. `feat: Add inflation-adjusted growth chart and improve all mobile charts` — added chart 6B
   (real growth indexed to 2006=100) + mobile optimization across all 12 charts.
4. (this) `docs: Add project summary for handoff`.

## 8. Possible next steps / backlog

- Re-render charts on window resize so `isMobile` toggling works without reload.
- Add real data sources as inline citations/links per chart (currently only a general footer).
- Verify/refresh figures periodically — several are 2026 projections that will date.
- Consider extracting CSS/JS into separate files if the project grows (currently intentionally
  single-file for zero-build GitHub Pages hosting).
- Optional: language toggle (ES/EN).

## 9. How to work on it

- Just edit `index.html`. No build, no dependencies to install.
- Open the file directly in a browser to preview, or rely on the GitHub Pages deploy.
- To publish changes: commit to `main` and `git push`. GitHub Pages redeploys automatically
  (allow 1-2 min; hard-refresh with Cmd+Shift+R to bypass cache).
