Course website for University of Washington CSE's 390 A1: Android Programming.

This is the source for the site. For the real site, [go
here](https://courses.cs.washington.edu/courses/cse390a1/16au/).

The site is built using [Hugo](https://gohugo.io/).

New content is added via `hugo new post/new-post.md`. Edit that file happily.

The files for the public site live in the `public/` directory. They are built
from the other files using the command `hugo`.

To push to the UW servers I then run:

```
rsync -av ./public/ user@server:/cse/web/courses/cse390a1/16au
```
Note that the trailing slash in `./public/` is required to copy the contents
rather than the directory itself.
