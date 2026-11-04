# FusionFixPreviews

Setting previews for [fusionfix.io](https://fusionfix.io) and FusionFixHub: in-game captures of each setting's
values (stills, full-resolution pictures and sweep clips), shown next to the settings.

## Layout

One folder per plugin, the current capture only:

```
<plugin>/manifest.json                 the capture: per setting its values and files; "files": name → content hash
<plugin>/<SECTION.Key>.<i>.webp        one still per captured value
<plugin>/<SECTION.Key>.<i>.full.webp   full resolution (settings with up to 6 values)
<plugin>/<SECTION.Key>.mp4             the sweep clip of a range setting (frame i = value i)
<plugin>/brand/brand.json              the fix's installer look: art per theme, logo, icon, colours, texts, links
<plugin>/brand/<file>                  its files (listed in manifest.json as "brand/<file>")
```

`brand/` is made by the site's `npm run brand -- <plugin>` from the fix's own art and stays when a capture is
replaced. FusionFixHub's installer checks every file against the manifest's hash before it uses it.

A recapture overwrites the folder (files the new capture doesn't have are deleted) and is committed; there are no
version folders, releases or tags.

The site serves the files at `https://fusionfix.io/assets/previews/<plugin>/<file>`, cached at Cloudflare's edge so
GitHub sees few requests. Pages and FusionFixHub ask for `<file>?v=<hash>` with the hash from `manifest.json`: that
address always means the same bytes and is cached for a year, so a recapture shows up as soon as the new manifest is
read (within about 10 minutes of the push).

## Updating a capture

In the site repository (FusionFixHub-Site, cloned next to this one):

```
npm run previews -- <capture folder>
```

It encodes the capture (WebP quality 90 at full resolution; lossless only where that is smaller), writes
`<plugin>/` here and prints the commands to publish it:

```
git -C ../FusionFixPreviews add -A <plugin>
git -C ../FusionFixPreviews commit -m "<plugin>: previews <capture>"
git -C ../FusionFixPreviews push
```

## Size

Lossy WebP keeps a plugin's folder small (GTA IV about 40 MB, Max Payne 3 about 38 MB). Each recapture adds its
changed files to the history; that is fine at this size. If the history ever gets large, squash it: commit the
current files on a new orphan branch and force-push it as `main`. The addresses don't change.
