A tiny project with one N+1 query (the lazy load of `Book.author` in the
loop of `library.py`), on which `.github/workflows/test.yml` runs the action.
`queryguard-baseline.json` accepts the finding, for the run that checks the
`baseline` input. It is not part of the action.
