# Product images to generate (5)

These are the product photos for the upcoming **Asset & Product Intelligence** and **Treatment Intelligence** pages (the large picture at the top of an asset). Until the files exist, the pages use a placeholder tile, so nothing breaks while you generate them.

**How to deliver:** save each image with the exact file name below into `assets/products/` in this repo. No code change is needed. As soon as a file is there, the page picks it up.

---

## Rules that apply to every image

- **One consistent set.** Generate all five with the same style, lighting and camera so they look like one catalogue.
- **No real brands.** No logos, brand names, model numbers or readable text on the product. Use a plain, unbranded industrial design. (The mock you shared shows a branded drill. We must not copy that.)
- **Format:** PNG or WebP, **1600 × 1200 px (4:3)**, under 400 KB each if possible.
- **Background:** soft warm off-white studio background, close to `#FAF8F5`, with a gentle floor shadow. A clean cut-out on a transparent background also works.
- **Framing:** product centred, filling about 70% of the frame, with clear empty margin on all sides. A slight three-quarter angle from the front-left, camera at product height.
- **Look:** realistic studio product photo, soft diffused light from the top left, sharp focus, true-to-life colours, light signs of real use (small scuffs, a little dust), not brand new and not damaged.
- **Colour accents:** where a product needs an accent colour, use a deep charcoal body with **yellow `#FFDD00`** details so it sits well in the app.
- **Add to every prompt as a negative prompt:** `logo, brand name, text, watermark, label with readable text, people, hands, cluttered background, cartoon, illustration, 3D render look, extreme wear, rust, broken parts`

---

## 1. Industrial electric motor

- **File name:** `assets/products/motor.webp`
- **Used for:** Industrial motor assets (the largest asset class).
- **Prompt:**
  > Studio product photograph of a three-phase industrial electric motor, 55 kW, IE3 efficiency class, cast-iron ribbed housing painted deep charcoal grey with small yellow accents, foot-mounted, visible terminal box on top, shaft with a keyway and a plain metal flange at the front, cooling fan cover at the back. Three-quarter view from the front-left, centred on a warm off-white seamless studio background, soft diffused top-left lighting, gentle floor shadow, light signs of real use such as tiny paint scuffs, sharp focus, realistic, 4:3, high resolution. No logos, no readable text.

## 2. Hydraulic pump

- **File name:** `assets/products/pump.webp`
- **Used for:** Hydraulic pump assets.
- **Prompt:**
  > Studio product photograph of an axial piston hydraulic pump, machined aluminium and dark steel body with a yellow dust-cap on one port, two threaded hydraulic ports on the side, a splined drive shaft at the front, a mounting flange with four bolt holes, small bleed screw on top. Three-quarter view from the front-left, centred on a warm off-white seamless studio background, soft diffused top-left lighting, gentle floor shadow, light signs of use such as faint oil staining on the casting, sharp focus, realistic, 4:3, high resolution. No logos, no readable text.

## 3. Industrial control unit (PLC)

- **File name:** `assets/products/control.webp`
- **Used for:** Control unit / PLC assets.
- **Prompt:**
  > Studio product photograph of a modular industrial PLC controller, DIN-rail mounted, charcoal-grey plastic housing with a yellow accent stripe, a row of small status LEDs, a block of green and grey screw-terminal connectors along the top and bottom, a short section of metal DIN rail under it, two slim I/O modules snapped on to the right. Three-quarter view from the front-left, centred on a warm off-white seamless studio background, soft diffused top-left lighting, gentle floor shadow, light signs of use such as faint scuffs on the housing, sharp focus, realistic, 4:3, high resolution. No logos, no readable text on the unit.

## 4. Cordless power tool (rotary hammer drill)

- **File name:** `assets/products/powertool.webp`
- **Used for:** Power tool assets, and also for tool-fleet batches (the page shows it with a "× quantity" badge).
- **Prompt:**
  > Studio product photograph of a cordless professional rotary hammer drill, compact body in matte charcoal black with bright yellow overmoulded grips and trigger, an SDS-plus chuck with a plain steel drill bit fitted, a side handle, a slide-in battery pack at the bottom, ventilation slots on the housing. Three-quarter view from the front-left, centred on a warm off-white seamless studio background, soft diffused top-left lighting, gentle floor shadow, light signs of real use such as small scuffs on the rubber and a little dust, sharp focus, realistic, 4:3, high resolution. No logos, no brand name, no readable text anywhere on the tool or battery.

## 5. Rugged laptop (IT electronics)

- **File name:** `assets/products/it.webp`
- **Used for:** IT electronics assets.
- **Prompt:**
  > Studio product photograph of a semi-rugged 14-inch business laptop, open at about 110 degrees, matte charcoal magnesium-alloy body with rubber corner bumpers, a plain dark screen showing a soft abstract gradient with no text, full keyboard with blank neutral keys, small yellow accent on the hinge cover. Three-quarter view from the front-left, centred on a warm off-white seamless studio background, soft diffused top-left lighting, gentle floor shadow, light signs of use such as faint scuffs on the corners, sharp focus, realistic, 4:3, high resolution. No logos, no brand name, no readable text anywhere.

---

## Quick checklist

| # | File | Asset class | Done |
|---|---|---|---|
| 1 | `assets/products/motor.webp` | Industrial motor | ☑ |
| 2 | `assets/products/pump.webp` | Hydraulic pump | ☑ |
| 3 | `assets/products/control.webp` | Control unit / PLC | ☑ |
| 4 | `assets/products/powertool.webp` | Power tool and tool-fleet batch | ☑ |
| 5 | `assets/products/it.webp` | IT electronics | ☑ |

**Delivered.** All five were generated as 1200 × 896 PNGs (about 1–1.6 MB each). For the app they were converted to **WebP** (about 60–120 KB each), which is what the pages load. The original PNGs are kept in `assets/products/originals/`, which is not tracked by git. If you regenerate an image, drop the new PNG into `originals/` and re-export the WebP at the same name.
