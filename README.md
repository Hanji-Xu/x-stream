# 03 Water X Stream

**Observation (Hanji):** a smooth stream from a tap shows alternating X-shaped lines along its length.
([r/askscience thread](https://www.reddit.com/r/askscience/comments/34ne9l/what_causes_x_shaped_lines_on_the_stream_when_i/))

**Piece:** one self-contained HTML file. Open it in a browser that has WebGL2 (any current Chrome, Safari, Firefox, Edge). No network needed.

## What you see

- **Left strip:** the whole stream from the spout down, at true scale. The blue box is the close-up window. Click or drag in it to move the window.
- **Close-up:** the stream ray-traced at true scale (no horizontal stretch; the 1 cm scale bar is real). Drag sideways to walk around the stream, drag up and down or scroll to move along it. Blue "round" ticks mark the nodes where the cross-section passes through a circle.
- **Side panel:** sliders for the tap, spout shape, water and view; live physics readouts; plots of radius, local wavelength and shape deformation along the stream; a cross-section of the stream at the cursor.

## Why the X appears

A spout is never perfectly round. The jet leaves with the spout's slightly oval (or triangular, square) cross-section. Surface tension pulls it toward a circle, inertia makes it overshoot, so the cross-section oscillates: an oval flips 90°, flips back, and so on. The water moves down while it oscillates, so the oscillation is frozen into a **steady pattern along the stream**. The stream looks like a chain of links. The curved faces that reflect and refract the room lights swap sides at each node, so the bright lines from one link cross the lines from the next. That crossing is the X. Gravity accelerates and thins the jet, so the X's get longer further down. Eventually the Rayleigh–Plateau instability breaks the stream into drops.

This is the classic **oscillating jet** of Rayleigh (1879) and Bohr (1909). Bohr used exactly this wavelength to measure the surface tension of water.

## Model, equations and constants

Coordinates: z is the distance below the spout (down), θ is the angle around the jet axis. SI units throughout.

### 1. Free fall and thinning of the jet
- Exit speed `U₀ = Q / (π a₀²)`, with `a₀ = D/2` the spout radius.
- Speed `U(z) = √(U₀² + 2 g z)` (Bernoulli, no air drag).
- Radius `a(z) = a₀ √(U₀ / U(z))` (continuity, Q constant).
- Travel time `T(z) = (U(z) − U₀)/g`.

### 2. Shape modes (the X pattern)
The surface is `R(θ,z) = a(z) · [1 + Σₙ cₙ(z) cos n(θ − θₙ)] / √(1 + ½ Σ cₙ²)` for n = 2 (oval), 3 (triangle), 4 (square). The divisor keeps the cross-section area constant to second order.

- **Rayleigh's dispersion relation** (inviscid liquid column, mode n, axial wavenumber k, x = k a):
  `ω² = (σ / ρ a³) · x Iₙ'(x)/Iₙ(x) · (n² − 1 + x²)`
  Iₙ is the modified Bessel function. For x → 0 this reduces to `ω² = n(n² − 1) σ / (ρ a³)` (6σ/ρa³ for the oval).
- **Steady pattern:** in the lab frame the wave is frozen, so `k U = ω(k)`. The page solves this for the lowest root at every height (scan + bisection). The local wavelength is `λₙ = 2π/kₙ`.
  - At low flow the long-wave formula is *wrong*: at 1 L/min through 8 mm, k a ≈ 1.35 at the spout and the full Bessel form shortens λ₂ from ≈2.5 cm to 1.86 cm. The page always uses the full form.
  - If `k U = ω(k)` has no root (the flow is slower than the ripple), the page says so and draws the long-wave value there.
  - Check against Bohr: expanding the Bessel term for n = 2 gives `ω² = (6σ/ρa³)(1 + 5π²a²/3λ²)`, which is the finite-wavelength factor in Bohr's surface-tension formula.
- **Phase:** `φₙ(z) = ∫₀ᶻ kₙ dz'`. The spout sets the starting shape with zero rate of change, so `cₙ(z) = εₙ(z) cos φₙ(z)`.
- **Bohr's finite-amplitude correction** (oval only): `λ₂ → λ₂ · √(1 + (37/24) ε₂²)`, from Bohr's formula `σ = 4ρQ²(1 + 37b²/24r²) / [6 r λ² (1 + 5π²r²/3λ²)]` where b is the amplitude of the radius variation. (Quoted from the oscillating-jet tensiometry literature from memory. The coefficient checks out against the finite-wavelength term above. Not re-derived here.)
- **Spout shape → starting amplitude:** an oval with axis ratio AR gives `ε₂ = (AR − 1)/(AR + 1)`. Triangularity ε₃ and squareness ε₄ are set directly as fractions of the radius.
- **Viscous damping** (weak-viscosity limit): `βₙ = 2n(n − 1) ν / a²`. Derived here from the dissipation of the potential flow `φ ∝ rⁿ cos nθ` in a 2-D column, the cylinder analogue of Lamb's `(n − 1)(2n + 1)ν/a²` for a drop. Amplitude factor `exp(−∫ βₙ/U dz)`. For water at 1 L/min through 8 mm this removes about 20 % of the amplitude over the first 30 cm, and roughly cancels the stretching growth below.
- **Stretching:** as the jet thins, the relative amplitude grows as `εₙ ∝ a^(−1/4)` (adiabatic invariant E/ω conserved for a fluid slice; ESTIMATE, WKB argument, see "estimates").

### 3. Breakup into drops (Rayleigh–Plateau)
- Growth rate of an axisymmetric disturbance (inviscid): `s² = (σ / ρ a³) x (1 − x²) I₁(x)/I₀(x)` for x = k a < 1.
- Tap vibration seeds disturbances of fixed angular frequency w at the spout. They are carried down with the water, so the local wavenumber is `k = w/U(z)`, and the amplitude is `δ₀ exp(∫ s/U dz)`.
- The page scans 40 frequencies, keeps the one that reaches δ = 1 first, and draws it with two neighbours (×0.93, ×1.08). Breakup is estimated where the summed amplitude reaches the radius.
- These ripples travel, so they are the only moving part. The X pattern itself is steady.

### 4. Optics (every pixel)
- Orthographic camera, rays hit the exact surface R(θ,z,t) (ray march + bisection). The normal is the numerical gradient of `|r| − R`, which includes the slope dR/dz. That slope is what tilts the highlights.
- **Snell refraction** in and out, **Fresnel reflection** (Schlick approximation), total internal reflection, n = 1.333.
- Light comes from a simple room at infinity: wall tiles, a window, an under-cabinet strip and a ceiling light (Kitchen); two tall strip lights (Dark room); a window behind the stream (Backlit). The rooms are invented; the optics is not.
- Water absorption per mm is included but negligible at this size, which is why a clean stream is so hard to see.
- **Food dye** (default 35 %) adds Beer–Lambert absorption along each ray's path inside the water, `exp(−α L)` with α = dye × (0.35, 0.20, 0.04) per mm for red, green, blue (a blue dye; the coefficients are illustrative, not a measured dye). Thicker parts of the stream look darker, so the shape reads at a glance. Set it to 0 for clear water.
- **Checkered card** (optional): a real 3 mm checkerboard plane 4 cm behind the stream. Refraction through the jet distorts it like a glass rod, which is the standard way to photograph clear jets.
- **Exposure** only scales the image brightness before tone mapping.
- Performance: the image redraws only when something changes (or while breakup ripples are on screen), at half resolution while you drag. If the GPU resets, the page rebuilds the renderer instead of going blank.

### Constants
| Quantity | Value | Source |
|---|---|---|
| g | 9.81 m/s² | standard |
| Surface tension σ(T) | `0.2358 τ^1.256 (1 − 0.625 τ)` N/m, τ = 1 − T/647.096 K (73.0 mN/m at 18 °C) | IAPWS 2014 release |
| Viscosity μ(T) | `2.414×10⁻⁵ · 10^(247.8/(T − 140))` Pa·s, T in K | Vogel-type fit, ~2 % |
| Density ρ(T) | `1000 [1 − (t + 288.9414)/(508929.2 (t + 68.12963)) (t − 3.9863)²]` kg/m³, t in °C | Thiesen-type fit |
| Refractive index | 1.333 | water, visible |
| Dish soap σ | 45 mN/m | **ESTIMATE** (see below) |

## Estimates and limits (flagged)
- **Spout shape is unknown for any real tap.** The "Ordinary tap (guess)" preset (axis ratio 1.25, small triangular and square parts) is a guess chosen so the X is as visible as in real life. Deformations around 10 % of the radius match what people see; a few % gives a much fainter pattern.
- **Spout exit:** uniform (plug) velocity, no vena contracta, no velocity-profile relaxation. A sharp-edged orifice would contract the jet to roughly 0.6 of its area; a pipe-like spout does not. Profile relaxation shifts the first wavelength slightly near the spout.
- **Laminar jet assumed.** A warning appears above Reynolds number ≈ 4000 (rule of thumb). Turbulent spouts give a rough surface that smears the X.
- **Dripping warning** below Weber number ≈ 4 is a rule of thumb.
- **Amplitude growth with stretching** (a^−1/4) and the damping formula are leading-order estimates. At default settings they are each about ±25 % over 30 cm and roughly cancel.
- **Bohr's 37/24 coefficient** is quoted from memory of the literature, consistent with the check above, not re-derived.
- **Dish soap:** a jet surface is only milliseconds old, so the surfactant has not fully adsorbed. The effective dynamic surface tension sits between clean water (72) and the soap's equilibrium value (~25–30). 45 mN/m is an estimate.
- **Breakup:** linear growth, stretching effects on the ripple amplitude ignored, starting noise level is a slider (no real data). Drops after pinch-off are drawn schematically (the linear surface simply goes through zero).
- **Air drag** and the air's own effect on the waves are neglected (fine below a few m/s).
- **Room lighting** is invented. Only the geometry and optics are computed.

## References
- Lord Rayleigh, "On the capillary phenomena of jets", Proc. R. Soc. Lond. 29 (1879).
- N. Bohr, "Determination of the surface-tension of water by the method of jet vibration", Phil. Trans. R. Soc. A 209 (1909).
- [Experimental observation of Bohr's nonlinear fluidic surface oscillation](https://arxiv.org/abs/1504.05105), Sci. Rep. 6, 19805 (2016).
- H. Lamb, *Hydrodynamics*, §275 and §355 (oscillations and damping of a liquid mass).
- IAPWS, "Revised Release on Surface Tension of Ordinary Water Substance" (2014).
- J. Eggers & E. Villermaux, "Physics of liquid jets", Rep. Prog. Phys. 71 (2008).

---

Part of **Art Insights**, a series of interactive pieces by Hanji Xu. Open `index.html` in a current browser; it is a single self-contained file with no network access or libraries. See also [01 Stubble Field](https://github.com/Hanji-Xu/stubble-vector-field).
