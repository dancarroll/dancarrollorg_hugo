# dancarrollorg_hugo

TODO:
* Nest tag URLs under blog/
* Get rid of categories generated pages
* Verify that the blog post link in using-windows-to-access-linux-host-name is correct
* Update all internal links to use Hugo reference format
* Validate all external links as well
* Proofread all posts, add updates for anything obviously incorrect/wrong/misleading


This repo is using the Hugo Bearcub theme, which is added as a Git submodule:

```
git submodule add https://github.com/clente/hugo-bearcub themes/hugo-bearcub
```

Create new page:

```
hugo new content content/posts/my-first-post.md
```

Generate site for local testing:

```
hugo server
```
