# dancarrollorg_hugo

My personal website, generated using [Hugo](https://gohugo.io/).

TODO:
* Nest tag URLs under blog/
* Get rid of categories generated pages
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

## Tips

### Link to internal post

To link to another blog post, you can use a reference to the post filename.
For example, to link to a post defined in `2020-01-test-post.md`, you can use
the following:

```
[link text]({{% ref "2020-01-test-post" %}})
```
