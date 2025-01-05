# dancarrollorg_hugo

My personal website, generated using [Hugo](https://gohugo.io/).

## Theme

This repo is using the Hugo Bearcub theme, which is added as a Git submodule:

```
git submodule add https://github.com/clente/hugo-bearcub themes/hugo-bearcub
```

## Developer workflow

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

## Potential posts

* Migration from Hyde to Hugo
* Generating a Cloudflare `_redirects` file
