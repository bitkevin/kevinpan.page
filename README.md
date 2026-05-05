kevinpan.page
======

Personal notes for maintaining the blog:

```bash
# Clone the blog repo
git clone https://github.com/bitkevin/kevinpan.page.git
cd kevinpan.page

# Install dependencies
npm install

# Run the local preview server
npx hexo server
```

Create a new post:

```bash
npx hexo new "my new article"
```

Deploy the generated site to `bitkevin/bitkevin.github.io`:

```bash
npx hexo clean
npx hexo deploy
```

----

Notes:

- Write posts directly in this repo.
- For images, upload them somewhere that provides a stable public URL, then insert that URL into the post.
- Deployment uses the Git target configured in the root `_config.yml`.
