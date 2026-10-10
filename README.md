# rice-wxl.github.io

Personal academic website of Xilin Wang, built with [al-folio](https://github.com/alshedivat/al-folio) and deployed to GitHub Pages by `.github/workflows/deploy.yml` on every push to `main`.

## Common updates

| To change… | Edit |
|---|---|
| Bio, photo caption | `_pages/about.md` |
| Publications | `_bibliography/papers.bib` (add `selected = {true}` to show on the home page) |
| News | add a file to `_news/` (copy an existing one, change `date` and text) |
| Projects | `_projects/*.md` (`importance` sets the order) |
| Blog posts | add `_posts/YYYY-MM-DD-title.md` |
| CV | edit `cv/Xilin_Wang_CV.tex`, then `tectonic -X compile cv/Xilin_Wang_CV.tex --outdir assets/pdf` |
| Social links | `_data/socials.yml` |
| Accent color | `assets/css/main.scss` |

## Local preview

```bash
export PATH="/opt/homebrew/opt/ruby/bin:$PATH"
bundle install
bundle exec jekyll serve --livereload   # http://127.0.0.1:4000
```
