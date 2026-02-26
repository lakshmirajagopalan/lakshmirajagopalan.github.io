## Context
Parse $ARGUMENTS to get following values:
- [current file name]: Topic name from $ARGUMENTS
- [word length]: how many words in the posts from $ARGUMENTS
- [difficulty level]: technical difficulty level from $ARGUMENTS

## Task
Create a file for the blog post 
- with filename of format `yyyy-mm-ff-{slug}.md` in folder `_posts`
- suggest title based on contents
- add metadata to the top of the file of format

---
layout: post
title: State Monads
categories: [Blogging, Scala]
tags: [scala, functional programming, monad, state monad]
seo:
  date_modified: 2020-03-25 06:51:27 -0300
---

- Identify tags and categories and create them if not available already under tabs/ and categories/

## Review the post

- **Invoke the blog-content-review subagent** to review and provide feedback on the blog
