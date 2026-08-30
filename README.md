# plane-moving-targets (aligned, inverted)

A Voronoi Tessellation Population (VTP) agent simulation with moving targets —
a MATLAB model and a dependency-light browser port of the same dynamics.
This is the inverse of the original aligned-targets version: there, you chose
a number of straight-line targets and one oscillating target was always
added on top; here, you choose a number of **independent oscillating
targets** and one **straight-line target** is always added on top instead.

**[Live site →](#)** (enable GitHub Pages, see below)

## What it is

Agents ("cells") move under three local forces computed from their Delaunay
neighborhood:

- **repulsion** from the nearest neighboring agent
- **alignment** toward neighbors heading the same direction
- **homing** toward whichever target is currently closest

Each step is capped by an estimate of the agent's own Voronoi cell size, so a
crowded agent can never leap past its neighbors. Two constants shape that
balance and are live-editable on the *live simulation* tab (and in the MATLAB
control panel):

- **&nu;** — alignment strength (weight of the alignment force relative to
  repulsion + homing)
- **L** — interaction length scale (sets the distance at which repulsion/
  homing hand off, and caps how far a crowded agent can move per step)

### Target motion

You choose a number of **oscillating targets** (0–5). Each one moves
sinusoidally in y — **independently**: its own height (oscillation center),
amplitude, and &omega; (angular frequency), each with its own slider — while
moving horizontally like everything else. One more target is always added on
top: a single **straight-line** target, holding a constant height.

Every target — including the straight-line one — shares a single horizontal
speed and a single shared x-coordinate. There's only one `x(t)` in the whole
system; each target differs only in `y(t)`. That means every target is
aligned (exactly the same x) at every instant, not merely at the start —
exactly the same invariant as the non-inverted version, just with the roles
of "the many" and "the one" swapped.

## Structure

```
index.html              site shell — tabs for about / live simulation / matlab version
assets/style.css        site styling
assets/sim.js           the simulation engine (Delaunay neighbor graph via d3-delaunay, force law, independent-oscillator + one-straight-line target motion, canvas rendering)
assets/app.js           page wiring — tabs, sliders, MATLAB source viewer
assets/matlab_src.json  bundled MATLAB source (for the in-page code viewer)
matlab/                 original MATLAB implementation
```

## Running the web version

No build step. Either open `index.html` directly, or serve the folder:

```
python3 -m http.server 8000
```

then visit `http://localhost:8000/`.

## Running the MATLAB version

Requires base MATLAB only (`delaunayTriangulation`, `polyshape` — no extra
toolboxes):

```
cd matlab
matlab -r dynamics
```

or open `matlab/dynamics.m` in the MATLAB editor and run it. You'll be
prompted for the number of oscillating targets (0–5); one straight-line
target is always added on top. An interactive figure then opens with a
control panel (agent speed, &nu;, L, the shared horizontal speed, each
oscillating target's height/amplitude/&omega; — independently adjustable per
target — and the straight-line target's height — Apply / Pause / Reset
Targets).

## Publishing to GitHub Pages

1. Create a new GitHub repository and push this folder to it (see commands
   below).
2. In the repo, go to **Settings → Pages**.
3. Under **Build and deployment**, set **Source** to `Deploy from a branch`,
   branch `main`, folder `/ (root)`.
4. Save — the site will be published at
   `https://<your-username>.github.io/<repo-name>/` within a minute or two.

```bash
cd plane-moving-targets-aligned-inverted
git init
git add .
git commit -m "Initial commit: VTP aligned moving-targets site (inverted)"
git branch -M main
git remote add origin https://github.com/<your-username>/<repo-name>.git
git push -u origin main
```

## Note on the JS port

The browser port implements the same force law as `dynamics.m` (repulsion +
alignment + homing, weighted by the `expReciprocal` transition function over
the Delaunay graph), with the same live-editable &nu; and L. One piece is
approximated for simplicity: the MATLAB version caps an agent's step by
ray-casting its intended direction onto the exact boundary of its Voronoi
cell (`voronoiProjectToBoundary.m`); the JS version approximates that cap as
half the distance to the nearest Delaunay neighbor. Visually and
qualitatively the dynamics match; if you need the exact cap, port
`voronoiProjectToBoundary.m` into `assets/sim.js`.
