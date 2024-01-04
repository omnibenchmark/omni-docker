# omni-docker

A place to organize Dockerfiles for base images for OMNIBENCHMARKs

Note that ideas/inspiration were obtained from:
- https://gitlab.com/artemklevtsov/r-alpine/-/blob/master/Dockerfile?ref_type=heads
- https://youtu.be/kx-SeGbkNPU
- https://github.com/slimtoolkit/slim

# Useful commands

```
docker image ls
docker build -t markrobinsonuzh/omni-docker:alpine-20231219--R-4.3.2 --no-cache --progress=plain . 2>&1 | tee build.log
docker image rm 05a7f4388028

docker build -t markrobinsonuzh/omni-docker:slim-py-3.11--pandas --no-cache --progress=plain . 2>&1 | tee build.log
docker push markrobinsonuzh/omni-docker:slim-py-3.11--pandas

docker-slim --report xray.json xray --target markrobinsonuzh/omni-docker:alpine-20231219--R-4.3.2
docker-slim --report profile.json profile --target markrobinsonuzh/omni-docker:alpine-20231219--R-4.3.2 --http-probe=false --continue-after=1
docker-slim --report build.json build --target markrobinsonuzh/omni-docker:alpine-20231219--R-4.3.2 --tage markrobinsonuzh/omni-docker:alpine-20231219--R-4.3.2--slim --copy-meta-artifacts . --http-probe=false --continue-after=1
```
