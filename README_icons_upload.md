# Scrap Orbit icon upload set (interim set, ships now)

## What is in this folder

* 90 PNGs, one per key in `ConfigArtIcons.lua`, named `so_icon_<key>.png`.
* 2 extras that are not `ConfigArtIcons` keys:
  * `so_icon_rocket_mark.png` (512 x 512): the rocket for the loading screen's RocketImage.
  * `so_icon_title_lockup.png` (1024 x 197, optional): the whole "SCRAP ORB[rocket]T" title as one image.
    Roblox stores images at most 1024 px on a side, so it is already downscaled to 1024 here.
* `icons_upload_manifest.json`: one entry per PNG, `{ key, file, px, family, source, config, url }`.
  `config` is false for the two extras; `url` is the raw GitHub URL the file will have once pushed.

Sizes: every symbol is 512 x 512. That covers scrap, xp, the five Finds, the three tickets, the ten bands, the
badge frame, the six rings, the ray ring, the star, the three wheel faces, the pointer, the three medals and the
crown. The 43 flat white glyphs are 256 x 256 (they are tinted in game with ImageColor3). The ten badge icons are
512 x 512. Every file has a transparent background.

## Where each file came from (manifest `source`)

| source | files | what |
|--------|-------|------|
| v2 | 20 | the ten band patches, six prestige rings, ray ring and badge frame from `finish/icons_v2`, plus the two extras |
| blender | 55 | the 0.9.54 renders from `finish/icons/png` |
| blender-recoloured | 3 | Moon Dust, Belt Ore, Ring Ice re-shaded to their Config colours |
| new | 14 | `helmet` and the thirteen `zone_*` glyphs, which had no file at all |

* **blender**: unchanged art, with two technical adjustments. The 16 symbols rendered at 256 px were scaled to 512
  (LANCZOS on premultiplied alpha). They carry no extra detail, which is fine at the 22 to 72 px they are shown at.
  The ten badge icons had the corners outside the inscribed circle made transparent. The Creator Dashboard crops
  badge icons to that circle anyway, so nothing visible changed.
* **blender-recoloured**: `Config.Economy.Materials` says MOON is pale gold (240,215,120), BELT is blue-grey
  (140,160,190) and RING is ice white (225,240,255). The renders were white, gold and cyan. All five Finds are the
  same crystal model under the same light, so Mars Iron's luminance (clean and not clipped) was used as the
  shading template, mapped through a three-step ramp per colour: shadow at 0.8 x the colour, the colour itself,
  then a highlight toward white. Every facet keeps its shading, and the contrast matches Mars Iron and Storm
  Crystal, which are unchanged.
* **new**: drawn in `finish/icons_v2/so_icons_v2.py` (`build_glyphs`) to the flat set's own rules: pure white, an
  18 px line at 256 px, round caps and joins, fitted edge to edge. The pictures follow the drawn fallbacks in
  `SO_Icons.lua` (`G.helmet`, `G.zone_*`): helmet, horizon with an arrow up (leaving Earth), cloud (sky),
  satellite over the horizon (low orbit), sparkles (deep space), crescent (Moon), zigzags (the long dark),
  planet with two moons (Mars), asteroids (Belt), banded planet with the red spot (Jupiter), ringed planets
  (Saturn, and Uranus with its ring on end), trident (Neptune), spiral (Milky Way).

## Badge frame layering rule (one line in SO_Theme.lua)

`T.Badge` draws `badge_frame` (ZIndex 4) on top of the band image (ZIndex 3). The v2 band PNGs already contain
their frame, rimmed in the band colour, so the steel frame drawn on top would hide it. In `T.Badge`'s `api.Set`,
replace

```lua
slot("Frame", "badge_frame", px, 4)
```

with

```lua
slot("Frame", I.idOf(I.key("band", band)) == 0 and "badge_frame" or nil, px, 4)
```

The frame then shows only while a band has no uploaded image (the drawn fallback glyph needs it), and it goes away
by itself once that band's id is in Config. This form is safe in Lua because `"badge_frame"` is truthy; never
write it as `x and nil or y`, which always returns `y`. Rings and the ray ring need no change: they sit behind the
badge at 1.34x and 1.62x, centred on the same point as the band patch.

## Moderation: write an id only after it is approved

Every uploaded image is moderated by Roblox. Write an id into `ConfigArtIcons.lua` only after that image shows as
approved (Creator Hub, Development Items, Images: the thumbnail shows the picture). An id that is still pending or
was rejected draws a blank square in game, while a 0 keeps the drawn fallback, so leave a key at 0 until its image
is approved.

The ten `badge_*` files are not game images. They are uploaded on the Creator Dashboard when each badge is created
(Badges, Create, icon), and their keys can stay 0.

## Pushing the set to GitHub (Terminal on the Mac)

```bash
cd ~/Desktop/Roblox/ScrapOrbit
git clone git@github.com:igui911/ScrapOrbit.git repo
mkdir -p repo/icons
cp -R finish/icons_upload/. repo/icons/
cd repo
git add icons
git commit -m "Add the Scrap Orbit icon set (interim: v2 bands and rings)"
git branch -M main
git push -u origin main
```

* The `git@github.com` address needs an SSH key added to your GitHub account. Without one, clone
  `https://github.com/igui911/ScrapOrbit.git` instead (git then asks for a token as the password).
* If git asks who you are, run `git config --global user.name "Your Name"` and
  `git config --global user.email "you@example.com"` once, then commit again.
* The repository must be public for the raw URLs to load: `raw.githubusercontent.com` answers 404 for a private
  repository. Each file's address is in the manifest, in the form
  `https://raw.githubusercontent.com/igui911/ScrapOrbit/main/icons/<file>`.

## Notes

* `so_icon_rocket_mark.png` placement: centre line x = 256, baseline y = 362, cap top y = 62 (cap height 300 px).
  To sit it on a title of cap height c px, size the ImageLabel 512·c/300 square and line PNG y = 362 up with the
  text baseline.
* `ConfigArtIcons.lua` comments still say 256 px for the symbols. The images are 512; `Icons.image` uses
  ScaleType Fit, so any square size works.
* To rebuild this folder: `python3 build_icons_upload.py` in `finish/icons_v2` (source paths are at the top of the
  script).
