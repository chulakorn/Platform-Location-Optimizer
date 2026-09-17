Platform Location Optimizer
A single-file, offline web app that finds the surface platform location minimizing total slant distance to a set of reservoir/well targets — useful for early-stage platform siting, well-cluster planning, and comparing candidate locations against the mathematical optimum.
No build step, no dependencies, no server, no data leaves your browser. Open `index.html` and go.
Live demo (enable GitHub Pages — see below — and put the link here)
What it does
Given a list of targets (Well name, X, Y, TVD), the app computes:
Optimum — the geometric median of the targets: the single point minimizing the sum of slant distances to every target. Found via Weiszfeld's iteration.
Centroid — the plain average of X/Y, shown as a quick reference point.
Per-well horizontal reach and slant distance from the optimum.
Optional comparison against up to 5 candidate platform locations (e.g. an existing wellhead platform), with % difference vs. the optimum.
A plan-view plot with optional connector lines from the optimum to every target.
CSV export of the full results table.
Method
Slant distance from a candidate platform `(px, py, 0)` to a target `(xi, yi, TVDi)`:
```
d = sqrt((px - xi)^2 + (py - yi)^2 + TVDi^2)
```
The point minimizing the sum of these distances across all targets is the geometric median (Fermat–Weber point). It has no closed-form solution for more than a few points, so it's found iteratively with Weiszfeld's algorithm:
Start at the centroid.
Repeat: `px ← Σ(xi/di) / Σ(1/di)`, `py ← Σ(yi/di) / Σ(1/di)`, where `di` is the current distance to target `i`.
Stop when the update moves less than 0.001 m.
The objective is convex, so this always converges to the single global minimum — no local-minimum risk, no parameters to tune. Typical convergence is under 30 iterations regardless of target count.
This was benchmarked against centroid, gradient descent, BFGS, and Nelder–Mead on a 22-target real-world case — all fully-converging methods agree to sub-metre precision, confirming there's one unique optimum. Weiszfeld was chosen for this tool because it needs the fewest iterations and no derivative/solver code, making it easy to keep as ~40 lines of plain JavaScript.
Usage
Open `index.html` in any modern browser (double-click the file — no server required).
Add targets with + Add target (it opens with two blank rows), or Paste from Excel to load many rows at once (tab/comma/space-separated: `Well  X  Y  TVD`, one per line; TVD optional).
Optionally add up to 5 candidate platform locations.
Click Compute optimum.
Click Download results (CSV) to export, or toggle connector lines on the plot.
Ctrl/Cmd + Enter also triggers a recompute.
Input units
Field	Meaning	Units
X	Easting, projected grid coordinate	metres
Y	Northing, projected grid coordinate	metres
TVD	True vertical depth below the platform (platform is at TVD 0)	metres
Coordinates must be a projected grid in metres (e.g. UTM), not latitude/longitude. TVD may be left blank (treated as 0) for a plan-view-only optimization.
Running / hosting
Locally: just open `index.html` — it's fully self-contained (no external scripts, fonts, or network calls).
GitHub Pages: Settings → Pages → Deploy from branch → `main` / root. The app will be live at `https://<user>.github.io/<repo>/`.
Limitations
Minimizes straight-line slant distance as a proxy for well cost — not true measured depth (build rate, kick-off depth, trajectory type are not modelled).
Single-platform optimization only; does not evaluate multi-platform splits.
Does not enforce anti-collision spacing, exclusion zones, or maximum step-out as constraints.
Assumes a single consistent projected coordinate system across all inputs.
Data privacy
No sample or demo data ships with this file. It opens with two blank rows — add your own targets via the UI or Paste from Excel. Nothing is saved, cached, or transmitted anywhere; all computation happens locally in your browser, and closing the tab clears everything.
License
MIT — see LICENSE.
