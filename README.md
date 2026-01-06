# blog.hadfield.dev

Personal blog built with [Hugo](https://gohugo.io/) using the [Ananke](https://github.com/theNewDynamic/gohugo-theme-ananke) theme.

## Prerequisites

- [Hugo](https://gohugo.io/installation/) (v0.146.0+, extended version recommended)
- [Go](https://go.dev/dl/) (v1.12+, required for Hugo Modules)

```bash
# macOS
brew install hugo go
```

## Development

### Run locally

```bash
hugo server
```

Site available at http://localhost:1313/

### Build for production

```bash
hugo
```

Output goes to `public/`.

## Project Structure

```
.
├── content/
│   ├── _index.md                    # Homepage
│   ├── about.md                     # About page
│   └── posts/
│       └── YYYY-MM-DD-slug/
│           ├── index.md             # Post content
│           └── *.png                # Post images (page bundle)
├── layouts/
│   └── shortcodes/
│       └── video.html               # Custom video shortcode
├── static/
│   └── images/
│       └── featured.png             # Default hero image
├── hugo.toml                        # Site configuration
├── go.mod                           # Hugo module definition
└── go.sum                           # Module checksums
```

## Creating Posts

Posts use [page bundles](https://gohugo.io/content-management/page-bundles/) - each post is a folder containing `index.md` and any associated images.

```bash
mkdir -p content/posts/YYYY-MM-DD-my-post-slug
```

### Front matter

```yaml
---
title: "Post Title"
date: YYYY-MM-DD
featured_image: image.png           # Optional: relative to post folder
---
```

If no `featured_image` is set, the theme looks for images with `cover` or `feature` in the filename.

## Configuration Reference

All configuration lives in `hugo.toml`.

### Site Settings

```toml
baseURL = "https://blog.hadfield.dev/"
languageCode = "en-us"
title = "blog.hadfield.dev"
```

### Theme (Hugo Module)

```toml
[module]
  [[module.imports]]
    path = "github.com/theNewDynamic/gohugo-theme-ananke"
```

Update theme:
```bash
hugo mod get -u
```

### General Parameters

```toml
[params]
  description = ""                    # Site description (SEO)
  site_logo = ""                      # Logo image path
  featured_image = "/images/featured.png"  # Default hero image
```

### Hero Image Settings

| Parameter | Description | Example |
|-----------|-------------|---------|
| `featured_image` | Hero background image | `/images/hero.jpg` |
| `background_color_class` | Background color when no image | `bg-blue`, `bg-gray` |
| `cover_dimming_class` | Overlay opacity on hero | `bg-black-60` (default), `bg-black-20`, `bg-white-40` |
| `featured_image_class` | Image fitting/alignment | `cover bg-center`, `contain bg-top` |

**Dimming examples:**
- `bg-black-10` to `bg-black-90` — light to dark overlay
- `bg-white-10` to `bg-white-90` — white overlay
- Empty string — no overlay

### Typography & Colors

```toml
[params]
  body_classes = "avenir bg-near-white"    # Body font + background
  post_content_classes = "avenir"          # Post content font
  text_color = "dark-gray"                 # Main text color
```

**Available fonts** (Tachyons classes):
- `avenir` (default)
- `athelas`, `georgia`, `times`, `bodoni`
- `calisto`, `garamond`, `baskerville`
- `helvetica`, `sans-serif`

**Background colors:** See [Tachyons skins](https://tachyons.io/docs/themes/skins/)

### Navigation Menu

```toml
[menu]
  [[menu.main]]
    name = "About"
    url = "/about/"
    weight = 10
  [[menu.main]]
    name = "Posts"
    url = "/posts/"
    weight = 20
```

### Social Links

```toml
[params.social]
  github = "username"
  linkedin = "username"
  twitter = "username"
  instagram = "username"
  youtube = "username"
  facebook = "username"
```

### Custom CSS

1. Create `assets/ananke/css/custom.css`
2. Register in config:

```toml
[params]
  custom_css = ["custom.css"]
```

**Note:** Requires Hugo Extended version.

## Page-Level Overrides

Any page can override site defaults in its front matter:

```yaml
---
title: "Page Title"
featured_image: "/images/custom-hero.jpg"
featured_image_class: "contain bg-center"
cover_dimming_class: "bg-black-20"
text_color: "navy"
omit_header_text: true              # Hide title on hero
toc: true                           # Show table of contents
---
```

## Shortcodes

### Video

```markdown
{{</* video src="https://example.com/video.mp4" */>}}
```

## Recommended Hero Image Dimensions

- **Width:** 1920px minimum
- **Aspect ratio:** 16:9 or wider
- **Height:** 600-800px typical

Images scale responsively using CSS `background-size: cover`.

## Theme Documentation

- [Ananke Wiki](https://github.com/theNewDynamic/gohugo-theme-ananke/wiki)
- [Tachyons CSS](https://tachyons.io/)
- [Hugo Documentation](https://gohugo.io/documentation/)

