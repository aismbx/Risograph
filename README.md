# Two Drum Press — Risograph Maker

A browser-based risograph poster maker. Design a two-colour layout, pick your inks and paper, key in the number of copies and press **Start**. Every copy comes out with halftone dots, grain, uneven inking and slight misregistration, so no two prints are the same.

It's a single self-contained `index.html`: no build step, no dependencies, no server.

## Features

### Machine
- **Copies:** key in up to 999 on the keypad. **C** clears it.
- **Drums:** choose inks for drum 1 and drum 2 from the 79 standard Risograph inks (from the RizzCraft colour guide), including fluorescents, metallics and white. White ink covers the paper rather than tinting it, so it shows up on coloured stocks.
- **Third colour (optional):** in the Drum picker, **+ Add a third colour** adds ink 3, printed on a second pass the way riso shops print 3-colour jobs. Pass 2 has its own halftone angle and drifts a little more than pass 1. Removing the third colour moves anything in ink 3 onto ink 2.
- **Paper:** 15 stocks, including Bright White, Natural, Fleck, Newsprint, Kraft, Fluo and Clear Film.
- **Texture overlay:** a recycled-card paper texture is added to every print (and the design preview). Turn it on/off and set its strength at the bottom of the Paper picker. Each copy gets the texture flipped and shifted at random, so the grain differs from copy to copy. It isn't applied to Clear Film.
- **Speed knob:** faster printing means more misregistration and more speckle.
- **Density knob:** controls how heavily the ink is laid down.
- **Registration arrows:** nudge drum 2 to fine-tune how the layers line up. With a third colour, tap the registration display (or press `R`) to switch to adjusting ink 3.
- **Feed:** prints a single proof copy.
- **Start / Stop:** run or interrupt a print job.

### Design
- Starts with a **blank page**.
- **Undo / Redo** and **Clear** buttons at the top of the design panel (Clear can be undone).
- Add **text** and **images**, then drag them to move them.
- **Size** and **Turn** buttons. You can also pinch, or use the scroll wheel (hold Shift to rotate).
- **Ink stops:** **Darker** / **Lighter** step images and text through 10 ink levels: 100%, 90% … 10%, like a riso tint chart.
- Text: 10 fonts, alignment, and edit (double-tap the text or press Edit). Line spacing (tight / normal / loose) is in the Edit sheet.
- Images print in one ink (1, 2 or 3), or **Two Color**, which splits the photo across inks 1 and 2. Text can use any of the inks.
- Front/Back layering, Copy (duplicates the selected item) and Delete.
- **Paste** button: imports an image you've copied in another app (your browser may ask permission the first time). `Ctrl/Cmd + V` does the same.
- Text line **Spacing**.

### Mask
Select an image and press **Mask** to hide parts of it.
- **Keep** adds visible areas; **Erase** removes them. If your first shape is Keep, everything outside it is hidden.
- Tools: **Lasso**, **Box**, **Oval** and **Brush** (with adjustable size).
- **Invert** flips the mask; **Ghost** shows the hidden parts faintly while you work; **Reset** removes the mask.
- Masks move, resize and rotate with the image.

### Image import
- Shows the original and a proof side by side.
- **Cut out** removes a plain background, with an adjustable tolerance.
- Darker / Lighter (10 ink stops) / Invert.
- You can also add images by drag and drop or by pasting.

### Output tray
- Printed copies slide into the tray and stack up.
- Tap the stack to browse copies and zoom to full size. Use your keyboard's ← → keys (or swipe) to flip through them.
- **Save PNG** exports a copy at 1275 × 1650 px (Letter size at 150 dpi). Clear Film copies export with a transparent background.

## Keyboard shortcuts

Click **? Shortcuts** at the top of the screen (or press `?`) to see every shortcut. The main ones:

| Where | Key | Action |
|---|---|---|
| Machine | `0`–`9` / `Enter` | Enter copies / start printing |
| Machine | `D` `I` `P` `F` `V` | Design, inks, paper, feed, view prints |
| Machine | Arrow keys / `R` | Registration / switch between drum 2 and ink 3 |
| Viewer | `←` `→` | Previous / next copy |
| Viewer | `Home` `End` `Z` `S` `Esc` | First, last, zoom, save, close |
| Design | `Ctrl/Cmd + Z`, `Shift + Ctrl/Cmd + Z` | Undo, redo |
| Design | `T` / `I` | Add text / image |
| Design | Arrow keys (`Shift` = bigger) | Move selection |
| Design | `-` `+` / `[` `]` | Resize / rotate |
| Design | `,` / `.` | Darker / lighter ink stop |
| Design | `1` `2` `3` | Print selection in ink 1, 2 or 3 |
| Design | `Ctrl/Cmd + V` | Paste an image |
| Design | `Ctrl/Cmd + D`, `Delete` | Duplicate, delete |
| Design | `Shift + Ctrl/Cmd + Delete` | Clear page |
| Design | `Ctrl/Cmd + Enter` | Run / print |
| Mask | `M` `K` `E` `X` `G` | Mask mode, keep, erase, invert, ghost |
| Mask | `L` `R` `O` `B`, `[` `]` | Lasso, box, oval, brush, brush size |

## Deploying to GitHub Pages

1. Create a new repository on GitHub.
2. Add `index.html` (and this `README.md`) to the root of the repository.
3. Go to **Settings → Pages**.
4. Under **Build and deployment**, set **Source** to *Deploy from a branch*, choose `main` and `/ (root)`, then click **Save**.
5. After a minute or so the site is live at `https://<your-username>.github.io/<repo-name>/`.

Or from the command line:

```bash
git init
git add index.html README.md
git commit -m "Add risograph maker"
git branch -M main
git remote add origin https://github.com/<your-username>/<repo-name>.git
git push -u origin main
```

Then turn on Pages as described in steps 3–4.

## Running locally

Open `index.html` in a browser. If you'd rather serve it:

```bash
python3 -m http.server 8000
```

Then visit `http://localhost:8000`.

## How it works

- **Separation:** single-colour images are converted by darkness. Two-colour images are split by a least-squares fit in optical-density space against the two chosen inks.
- **Printing:** each drum is screened with a rotated halftone (15° and 75°), then perturbed by low-frequency noise and roller banding, random dropouts and speckle. The drums are multiplied onto the paper colour, and each drum gets a small random offset and rotation scaled by the speed setting.
- **Texture:** the two texture layers from the recycled-card PSD are embedded in `index.html` as JPEGs (about 900 KB). Like the PSD, the fibre texture is multiplied over the print and the highlight layer is screened on top at 50%.
- **Cut out:** removes background pixels that are connected to the image edges and close to the median edge colour, then softens and crops the result.

## Limitations

- Cut out works best on plain or evenly coloured backgrounds. It won't separate a subject from a busy scene.
- Designs aren't saved between sessions: reloading starts with a blank page.
- Fonts load from Google Fonts, so text falls back to a system font when offline.
- Ink colours are the screen values from the RizzCraft colour guide; real prints will vary.

## License

Add a license of your choice (e.g. MIT) before sharing publicly.
