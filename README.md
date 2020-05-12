# auditorium

This is just a collection of simple scripts and configuration for setting up a "virtual auditorium".

Streams can be published through RTMP, handled by [nginx](https://github.com/nginx/nginx) with [rtmp module](https://github.com/sergey-dryabzhinsky/nginx-rtmp-module/). Published streams are transcoded, on-the-fly, into multiple bitrate/resolution hls variants with [ffmpeg](https://github.com/FFmpeg/FFmpeg).
People can watch in *almost* any browser. Powered by [video.js](https://github.com/videojs/video.js).

## Getting Started

- Make sure you have buildah and podman installed

- Build images:
  ```shell
  set -a
  source env-samples/default.env # copy and modify, if necessary
  set +a

  echo -n nginx nginx-rtmp-module ffmpegd confd | xargs -rn1 -P4 bash -c 'scripts/build/${0}'
  ```

- Create `volumes/db/data/auditorium.yml` with the following contents:

    ```yaml
    auditorium:
    domain: {{ $domain }}
    servers:
        - rtmp-n01
    streams:
        {{ $stream.id }}:
        key: {{ $stream.key }}
    ```
  - replace the variables in curly braces:
    - domain: where it will be hosted (e.g.: auditorium.grana.guru)
    - stream.id: public identifier (e.g.: `< /dev/urandom tr -dc 'a-zA-Z0-9' | fold -w12 | head -n1`)
    - stream.key: secret key, used by caster (e.g.: `< /dev/urandom tr -dc 'a-zA-Z0-9' | fold -w32 | head -n1`)

- Execute:

    ```shell
    set -a
    source env-samples/default.env # copy and modify, if necessary
    set +a

    scripts/deploy/standalone-rootless/start
    ```

## Features

- [x] multiple simultaneous streams
- [x] RTMP publish
- [x] HLS play
  - [x] resolution/bitrate variants
- [ ] replace confd
- [ ] letsencrypt automation
- [ ] stream catalog
- [ ] authentication/authorization (keycloak)
- [ ] manager module
  - [ ] manage streams
  - [ ] scheduled streams
