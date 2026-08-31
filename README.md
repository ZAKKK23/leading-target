# plane-moving-targets

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

