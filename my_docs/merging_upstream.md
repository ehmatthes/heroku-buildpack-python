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
  - Note 10/21/20: I did not have this conflict this time. Maybe it's set to always keep mine?
- Resolve any remaining conflicts.
- Commit changes and push this branch.
  - `git commit -am "Merged upstream changes."`
  - `git push origin merge_upstream_mmddyy`
- Deploy any test projects, using this branch.
  - `heroku buildpacks:set https://github.com/ehmatthes/heroku-buildpack-python.git#merge_upstream_mmddyy`
  - Note: If using a test project that's already been pushed, changing the buildpack to this one will allow a new push without any changes to the project itself.
- Merge to main and push.
  - `git checkout main`
  - `git merge merge_upstream_mmddyy`
  - `git push origin main`
- Delete temp branch
  - `git branch -D merge_upstream_mmddyy`
  - `git push origin --delete merge_upstream_mmddyy`