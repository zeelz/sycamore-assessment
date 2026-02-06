## This repo contains solutions to Sycamore job assessment

### Interview questions and answers document
[Answers to questions](https://docs.google.com/document/d/1Ofj0xr11Ysh0Uv5qZ-wQsx4_tCe_8FPRH_OC7JZzJ_Y/edit?usp=sharing)


## CI/CD Pipeline Workflow and Fix

1. The first workflow run at failed at the `Build docker image` step run due to the base image being not found in Docker registry. This step ran successfully after the image was update to `node:20-alpine`

`ERROR: failed to build: failed to solve: node:14-highspeed: failed to resolve source metadata for docker.io/library/node:14-highspeed: docker.io/library/node:14-highspeed: not found`

2. Trivy showed four vulnerable packages. Three of them are fixed in their new versions, while the forth one is yet to be fixed.

Therefore the solution is to update the fixed packages to their newer version and tell Trivy to ignore the unfixed one.

Fix available for: cross-spawn@7.0.5 glob@10.5.0 tar@7.5.7
Unfixed: 

`npm install cross-spawn@7.0.5 glob@10.5.0 tar@7.5.7`