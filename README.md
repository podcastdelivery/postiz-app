# postiz-app (podcastthreads build)

Builds [Postiz](https://github.com/gitroomhq/postiz-app) with one Podcast Delivery patch and publishes the image to `ghcr.io/podcastdelivery/postiz-app`. The NAS that runs Postiz pulls this image instead of compiling Postiz itself.

## The patch

[`.github/patches/podcastthreads.patch`](.github/patches/podcastthreads.patch) adds the Threads topic tag `podcastthreads` to every top-level Threads post. Meta allows one topic tag per post and rejects one on carousel items, so those are left alone. Postiz has no way to set a per-post value yet ([gitroomhq/postiz-app#1497](https://github.com/gitroomhq/postiz-app/issues/1497)). Once it does, this repository can be retired in favour of the official image.

## Building

The [workflow](.github/workflows/build-podcastthreads.yml) checks out an upstream release tag, applies the patch, builds `Dockerfile.dev` for `linux/amd64` and pushes two tags:

- `ghcr.io/podcastdelivery/postiz-app:<release>-pt`, for example `v2.25.0-pt`
- `ghcr.io/podcastdelivery/postiz-app:latest-pt`

Run it from the Actions tab and leave the tag blank for the latest release. It also runs every Monday at 10:00 UTC and builds the latest release if that tag doesn't exist yet. If the patch stops applying, the run fails before anything is pushed.

## Updating the NAS

Set `POSTIZ_IMAGE_TAG` in `/volume2/docker/postiz-docker-compose/.env` to the new `-pt` tag, then:

```bash
cd /volume2/docker/postiz-docker-compose
docker compose pull postiz && docker compose up -d postiz
```

To roll back, set the previous tag and run the same two commands.
