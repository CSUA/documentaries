# documentaries

documenting history of CSUA

The repo is a bunch of Github-flavored-markdown files. Meant to be a more permanent/accessible history record than Google drive.

## How to test locally:

- Install gh-pages gem via Gemfile

```shell
bundle install
```

- Run jekyll
  - If running remotely (e.g., on a dev server) make sure port is open.
  - `--host 0.0.0.0` is needed so that jekyll will accept any hostname in request

```shell
bundle exec jekyll serve --port 8000 --host 0.0.0.0 --watch
```
