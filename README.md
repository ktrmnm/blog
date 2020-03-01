# ktrmnm blog

## Requirements

- Node.js
- Hexo

## How to use

On `master` branch

(1) Create new post

Execute this
```
hexo new <post_title>
```
or just put a markdown text to `soure/_posts`.
I like to use post titles like as "yyyy-mm-dd-title".

(2) Edit the text

by markdown.

(3) Deploy

```
hexo generate -d
```
This command automatically pushes to `gh-pages` branch. See `_config.yml` for detailed configuration.

For any other usages, see the official document of [Hexo](https://hexo.io/) for details.
