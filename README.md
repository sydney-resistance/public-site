# sydres.org

Static Jekyll site for the Sydney Resistance, deployed to GitHub Pages.

The authenticated agent portal at `agent.sydres.org` is a separate service
and is not part of this repo.

## Local development

```
bundle install
JEKYLL_NO_BUNDLER_REQUIRE=true jekyll serve --livereload
```

Open <http://localhost:4000>. (The `JEKYLL_NO_BUNDLER_REQUIRE` env var is a
workaround for a Ruby/Bundler/rubygems incompat on some distros; you can
drop it if `jekyll serve` works for you directly.)

## Deploy

Push to `main`. GitHub Actions builds with
`.github/workflows/jekyll-gh-pages.yml` and publishes to GitHub Pages. The
custom domain is configured via the `CNAME` file.

## Contributing

Pull requests welcome - especially for refreshing the dated photos and copy.
The site is deliberately small; one HTML file ([index.html](index.html)),
one layout ([_layouts/default.html](_layouts/default.html)), and CSS/JS
inherited verbatim from the original Resistance Berlin design.

### Refreshing photos and other assets

The visual assets are visibly aging (e.g. the iPhone-3-era scanner mockup,
the GIF showing the old comms apps). New photos and illustrations are very
welcome. When adding or replacing one:

- **Location:** put new files in [img/](img/). Slideshow backgrounds live
  in [img/600/](img/600/) and are referenced from `css/slideshow.css`.
- **Naming:** lowercase, hyphenated, descriptive - `discord-mockup.png`,
  not `IMG_4023.png`.
- **Formats:** SVG for logos and icons. PNG for screenshots and graphics
  with transparency. JPG for photos. Animated GIF only when motion is
  necessary - prefer a still image otherwise.
- **Dimensions:** photos should be at most ~2000px on the long edge.
  Anything bigger will be downscaled by the browser on a phone anyway.
- **File size:** aim for under 500 KB per image. Run new photos through
  [Squoosh](https://squoosh.app/) or `imagemagick` before committing.
- **Referencing in HTML:** use
  `{{ "/img/your-file.ext" | relative_url }}` so the URL works under
  both the GitHub Pages preview URL and the custom domain.
- **Alt text:** every `<img>` should have a meaningful `alt` attribute
  describing what's pictured (not just "image").
- **Don't replace** if you're only proposing a change - add the new asset
  alongside the old one and reference it from `index.html`; the old file
  can be removed in a follow-up commit once the new look is agreed.

### Refreshing copy

Body text lives directly in [index.html](index.html). The current copy is
ported from the original site. Edits to make it less dated or more
welcoming are encouraged.

### Pull request workflow

1. Fork the repo and open a PR against `main`.
2. A maintainer will review the diff. When the change looks safe to run in
   CI, they'll add the **`safe-to-test`** label, which triggers the
   [test workflow](.github/workflows/test.yml).
3. If you push new commits afterwards, the label is automatically removed
   and the maintainer must re-review and re-add it before tests run again.
4. Once tests pass and review is complete, the PR is merged. Deploy is
   automatic on push to `main`.

This label gate exists so unreviewed contributor code never runs in CI
without a human's sign-off.

## Tests

The test workflow runs [HTMLProofer](https://github.com/gjtorikian/html-proofer)
against the built site, checking for broken internal links, missing
referenced images, and HTML validity. To run it locally before opening a
PR:

```
bundle exec jekyll build
bundle exec htmlproofer ./_site --disable-external=true --allow-missing-href=true --ignore-missing-alt=true
```

External link checking is disabled in CI because external sites
rate-limit, change, and go down - false positives there would block real
content changes. If you want to spot-check outbound links, drop the
`--disable-external` flag locally.
