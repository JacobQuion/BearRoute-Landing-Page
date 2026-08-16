# BearTracks Website

Static marketing site for BearTracks, mirrored from the Figma Sites build at
`beartracks.figma.site` so it can be hosted on Vercel.

## Structure

```
public/
  index.html      entry point (page shell + meta tags)
  assets/
    index-*.js         compiled React bundle — all page content lives here
    index-*.css        compiled styles (imports Fraunces + DM Sans from Google Fonts)
    image-*.png        campus map, dining screenshot
    app-demo.mp4       demo screen recording shown inside the phone mockup
    app-demo-poster.jpg first frame, shown while the video loads
    favicon-*.png, social-image-*.png
vercel.json       static build config + asset caching
```

There is no build step. Vercel serves `public/` directly as static files.

## Local preview

```sh
python3 -m http.server 8000 --directory public
# then open http://localhost:8000
```

## Deploying

Vercel auto-detects this as a static site via `vercel.json`
(`outputDirectory: "public"`). Push to `main` and Vercel redeploys.

## Notes / TODO

- **`og:image` needs your real domain.** The Open Graph and Twitter image tags in
  `public/index.html` point at `https://your-domain.vercel.app/...`. Link previews
  won't render until you swap in the actual deployed domain — relative paths don't
  work for social crawlers.
- **The "Download Free" button links to `#`.** It's a placeholder in the Figma
  original too. Point it at the App Store listing when you have one.
## Demo video

The demo inside the phone mockup was originally a 10 MB GIF. It is now an
812 KB H.264 MP4 (`crf 28`, `+faststart`, no audio) — about 12x smaller, and
visually better than the GIF, which had been quantized to 256 colors.

This required patching the compiled bundle: the `<img>` was swapped for a
`<video autoplay muted loop playsinline preload="auto">` with a poster frame.
A `ref` callback also forces `muted = true` and calls `.play()`, because React
sets `muted` as a DOM property rather than an attribute, and some browsers check
the attribute when deciding whether to allow autoplay.

**If you ever re-mirror the site from Figma, this patch is lost and must be
reapplied.** To regenerate the video from a GIF:

```sh
ffmpeg -i input.gif -c:v libx264 -preset veryslow -crf 28 \
  -pix_fmt yuv420p -movflags +faststart -an app-demo.mp4
ffmpeg -i input.gif -frames:v 1 -q:v 4 app-demo-poster.jpg
```

## Editing content

The page content is inside the minified bundle `public/assets/index-*.js`, so it
isn't practical to edit by hand. To change copy or layout, edit the original in
Figma and re-mirror, or rebuild the page as source (e.g. a Vite + React project)
if you want it to be maintainable long term.
