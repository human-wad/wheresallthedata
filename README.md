# wheresallthedata.fyi

The front page is `index.html`, hand-written and copied as is. The blog at `/blog/` is built by
GitHub Pages with Jekyll; nothing needs installing to publish.

## Writing a post

1. Copy `_drafts/how-to-write-a-post.md` to `_posts/YYYY-MM-DD-short-name.md`.
2. Set `title` and `authors` (keys from `_data/authors.yml`; list both for a joint post).
3. Write Markdown. Put images in `blog/media/`.
4. Commit and push. It shows up at `/blog/YYYY/short-name/` and in the feed at `/blog/feed.xml`.

## Linking a post from your own site

Add it to the top of `_data/blogroll.yml`:

```yaml
- title: The post's title
  url: https://your-site.example/the-post
  by: axiss
  date: 2026-10-24
```

## Adding an author

Add an entry to `_data/authors.yml` with `name`, `uri` and `email`.

## Previewing locally

```sh
gem install jekyll -v 3.10.0 && gem install jekyll-feed kramdown-parser-gfm webrick
jekyll serve --drafts
```
