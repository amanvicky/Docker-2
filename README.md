# docker-in-depth — Docker in Depth

Second-pass Docker study: ENTRYPOINT vs CMD, multi-stage builds, containerizing a real static site, and compose — notes and exercises, each committed next to the files that prove it.

## What's here

| Path | Topic |
|---|---|
| `DockerFiles/cmd/` | Minimal image: `CMD ["echo", "hello"]` |
| `DockerFiles/entry/` | Minimal image: `ENTRYPOINT ["echo"]` |
| `DockerFiles/entrycmdboth/` | Both instructions in one image — what wins when they combine |
| `EntrypointVsCmd.txt` | The lab log for those three: build each `printer:v1/v2/v3`, run with and without args, diff the behavior |
| `DockerFiles/TooplateWebsite/` | A real static website (Ubuntu + Apache2, site tarball pulled in with `ADD`) containerized end-to-end |
| `MultiStageDockerfile.txt` | Multi-stage Java build: fat Maven stage → slim Tomcat runtime carrying only the `.war` |
| `IntstallDockerAndCompose.txt` | Docker + Compose install notes |
| `Docker.pdf` | The course deck the exercises came from |

## The CMD/ENTRYPOINT experiment

Three one-line Dockerfiles, three different behaviors:

- `CMD ["echo", "hello"]` — `docker run printer:v1` prints *hello*; any argument **replaces** it entirely.
- `ENTRYPOINT ["echo"]` — `docker run printer:v2` prints nothing; `docker run printer:v2 hello` prints *hello*. Everything you pass **appends** to the entrypoint.
- Both together — `cmd` becomes the default argument to the entrypoint: `docker run printer:v3` prints *hello*, `docker run printer:v3 hi` prints *hi*.

## The multi-stage pattern

`MultiStageDockerfile.txt` builds a Java webapp in a fat `openjdk:8` + Maven stage, then copies only `vprofile-v2.war` into a slim `tomcat:8-jre11` runtime — build tools never ship in the final image.

## The real-website container

`TooplateWebsite/Dockerfile` installs Apache2 on Ubuntu, unpacks the site tarball straight into `/var/www/html/` with `ADD` (it auto-extracts), keeps Apache logs on a `VOLUME`, and runs Apache in the foreground:

```bash
docker build -t lilfasion DockerFiles/TooplateWebsite/
docker run -d -p 8080:80 lilfasion
```

Continues from [Docker](https://github.com/amanvicky/Docker) (Node app containerization) and feeds the Kubernetes work in [kubernetes](https://github.com/amanvicky/kubernetes).
