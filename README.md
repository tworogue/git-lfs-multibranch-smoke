# Git LFS multibranch smoke test

A small fixture for checking the **Git LFS pull after checkout** behaviour in a Jenkins-compatible multibranch pipeline.

## Setup

1. Create a multibranch pipeline with this repository as a Git branch source.
2. Enable branch discovery and use `Jenkinsfile` as the script path.
3. Use an agent with Git and Git LFS installed and network access to GitHub.
4. Scan the repository. Both `main` and `feature/lfs-check` contain the same test.

## Expected results

- With the LFS behaviour disabled, checkout leaves LFS pointer files and the verification stage fails.
- With **Git LFS pull after checkout** enabled in Branch Sources, checkout downloads the objects and all four checks pass.

The pipeline cleans its workspace before `checkout scm` and sets `GIT_LFS_SKIP_SMUDGE=1`, so ordinary Git smudge cannot hide a missing LFS behaviour. The Jenkinsfile does not run `git lfs pull` itself. Each LFS object is only 33 bytes; filenames also exercise spaces and commas.

A failure in the first stage means Git LFS is missing from the selected agent. A failure during checkout may indicate network or repository access problems.

## Fixture source

The four data files and their tracking rules come from [MarkEWaite/jenkins-pipeline-utils, branch tests/largeFileSupport](https://github.com/MarkEWaite/jenkins-pipeline-utils/tree/2a48da248bd8148f078bc643162437f609f823da), commit `2a48da248bd8148f078bc643162437f609f823da`.

The original MIT license is preserved in [LICENSE](LICENSE).
