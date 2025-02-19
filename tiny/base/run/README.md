# Tiny

Tiny is a base image for containers.  It is functionally equivalent to Google's Distroless, but built with Ubuntu packages rather than Debian.

## Installed Packages

* libc6
* libssl1.1
* openssl
* ca-certificates
* base-files
* netbase
* tzdata

## Additional components

* Users: root, nonroot, nobody
   * `/etc/passwd` file
* Groups: root, nonroot, nobody, staff, tty
  * `/etc/group` file
* Custom os-release (`tiny`), in `/etc/os-release` file
*  `/etc/nsswitch.conf` file
* `/etc/services` file
* an empty `/tmp` directory


## Usage

For a image without additional users & groups required by cloud native buildpacks use the following
`docker pull cloudfoundry/run:tiny`

For a image with the cnb user + group required by cloud native buildpacks use the following
`docker pull cloudfoundry/run:tiny-cnb`

Users should compile their application and set an entrypoint. As an example:
```Dockerfile
FROM golang AS build-env

ADD . /app

RUN cd /app && \
    go build -o test

FROM cloudfoundry/run:tiny
COPY --from=build-env /app/test /test

ENTRYPOINT ["/test"]
```

## Building

1. Clone this repository and `cd` into the directory
1. `docker build -t tiny .`

## Testing
You will need to have [bats](https://github.com/sstephenson/bats) installed (`brew install bats`)
1. run `./tests/test.bats && ./tests/testapp.bats`

## File comparison with Distroless
1. Build the image (see above steps)
1. Compare the two:
```bash
scripts/filediff gcr.io/distroless/base
```

