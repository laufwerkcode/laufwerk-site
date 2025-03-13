Run this site locally using:

```bash
$ docker run --rm -it \
--volume="$PWD:/srv/jekyll" \
--volume="$PWD/vendor/bundle:/usr/local/bundle" \
--env JEKYLL_ENV=development \
-p 4000:4000 jekyll/jekyll jekyll serve
```