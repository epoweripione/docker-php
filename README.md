# docker-php
**Build latest PHP docker images with extensions**

* Use `install-php-extensions extension_name` to install Extensions.
> [docker-php-extension-installer](https://github.com/mlocati/docker-php-extension-installer)
* Default **installed** extensions: see the `Dockerfile` for more detail.
* `Composer`
* `PDFlib`

More info: https://hub.docker.com/_/php/

# grpc
Recently, I removed `grpc`, because it takes an extremely long time to build, exceeding the limit for `Github Actions`.

If you really want to use it, build it locally.

Maybe add back in the future if we can find a way to reduce the build time.
