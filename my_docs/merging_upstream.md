Merging upstream changes
---

Upstream changes should be merged on a regular basis, to show that this is compatible with the current official buildpack and to support proper deployments using the latest Heroku infrastructure.

Here are the steps:

- Clean up any of my own development cruft first.
- Generate a list of files changed since the last upstream merge:
  - `git diff --name-only hash_of_last_merge`
  - It's helpful to know which files may have conflicts.
- Make new branch `merge_upstream_mmddyy`.
- Run `git pull https://github.com/heroku/heroku-buildpack-python.git main`
- There will always be conflict with README, and I will always keep mine:
  - `git checkout --ours README.md`
  - `git add README.md`
- Resolve any remaining conflicts.
- Commit changes.
  - `git commit -am "Merged upstream changes."`
- Deploy any test projects, using this branch.
  - `heroku buildpacks:set https://github.com/ehmatthes/heroku-python-buildpack.git#merge_upstream_mmddyy`
- Merge to main and push.
  - `git checkout main`
  - `git merge merge_upstream_mmddyy`
  - `git push origin main`