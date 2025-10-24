
# ome-ngff-samples

This is a page to list various OME-NGFF sample images from IDR. Deployed at https://idr.github.io/ome-ngff-samples/

## Development

### Prerequisites

- [pixi](https://pixi.sh/) - Cross-platform package management tool

### Local Development

1. **Install dependencies:**
   ```bash
   pixi install
   ```

2. **Start the development server:**
   ```bash
   pixi run serve
   ```

   The site will be available at http://127.0.0.1:4000/ome-ngff-samples/

3. **Build the static site:**
   ```bash
   pixi run build
   ```

   The built site will be in the `_site/` directory.

### How It Works

- **pixi** manages the Ruby environment and dependencies
- **Jekyll 4.0** builds the static site
- **Minima 2.5.2** theme with custom head support for jQuery/DataTables
- Both local and CI use the same `pixi.toml` and `Gemfile.lock` for consistency

### Deployment

The site is automatically deployed to GitHub Pages when changes are pushed to the main branch using GitHub's built-in Jekyll build process.

## Troubleshooting

### Permission denied errors with pixi on macOS

If you encounter permission denied errors with your temp directory when running `pixi` commands:

```bash
mkdir -p ~/tmp
export TMPDIR=~/tmp
```

To make this permanent, add to your shell profile:
```bash
echo 'export TMPDIR=~/tmp' >> ~/.zshrc  # or ~/.bashrc
```

This is particularly common on university/enterprise-managed macOS machines where the default `$TMPDIR` may have restricted permissions.
