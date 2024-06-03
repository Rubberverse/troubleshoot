# Troubleshooting for qor-caddy container image

You may run into some issues running our images so here's a small knowledgebase and how to fix them (if possible)

This might not always be up-to-date but well at least it's here.

## ❌ Errors

Everything below this line indicates a configuration or image-creation failure resulting in unusable binaries being present. They're not specific to Caddy and are more specific to how our container image handles things so don't go posting these on Caddy's GitHub issues!

No seriously, don't do that. You can however do that if you experience a issue while running Caddy itself though, as that's out of my reach but keep in mind that it might be modules causing a issue, not necessarily Caddy itself so make sure to report any module bugs to their respective authors, if any.

## `line 0: syntax error: unexpected word (expecting ")")` upon running Caddy

This error won't be visible if you run the container without modifying it's entrypoint script, however one of the symptoms is that container will die after entrypoint script tries to launch Caddy.

This usually indicates that cross-compilation process failed, or `qemu-server` had a skill issue during image building phase and it produced a broken binary for the architecture or broken image altogether. To verify if one or the other is the case, try following steps

1. Replace entrypoint with BusyBox shell and run the image interactively, replace podman with docker if you use docker and alpine with debian if using debian image `podman run -it --entrypoint /bin/sh docker.io/mrrubberducky/qor-caddy:latest-alpine`
2. Change directory to `/app/bin` with `cd /app/bin` then run the binary with `./caddy`
3. Note the error message you get and post a GitHub issue on [our main repository](https://github.com/Rubberverse/qor-caddy/issues)

In case you get `exec format error` upon running the first step and you pulled the correct image for your architecture, please create a GitHub issue too as that indicates build process broke and docker buildkit /w `qemu-server` didn't generate proper multi-architecture image.

## `exec format error`

You're running a wrong image for your architecture. Either use qemu-server to emulate it or switch to correct architecture. If you're on correct architecture and still get this error upon running a correct version tag against your architecture, please post a GitHub issue on [our main repository](https://github.com/Rubberverse/qor-caddy/issues)

## `bind 80: permission denied` or `bind 443: permission denied`

This container doesn't come or grant libcap capabilities to binaries as it never really worked in my experience so in case you want it to bind to those ports, you will need to grant a sysctl flag to your rootless user on your host (if using `"host"` networking) and on your container.

To do so, you can add following line to your `compose.yaml` under `qor-caddy` directive:

```yaml
    sysctls:
      - net.ipv4.ip_unprivileged_port_start=80
```

and on your host, modify `sysctl.conf` in `/etc/sysctl.conf` and add following line `net.ipv4.ip_unprivileged_port_start=80` then apply it with `sysctl -p /etc/sysctl.conf`, granted this makes any user be able to bind to ports below 1000 to 80 on your host machine so it may be less desired in this case.

## `Invalid value or blank environment for CADDY_ENVIRONMENT, Container will fallback to Production launch`

This is more of a warning message than an error but it's usually scrapped to configuration failure in my eyes. This image allows you to switch between production and testing environments.

They're different in ways that

- Production environment runs in foreground and doesn't have dynamic config reloading and generally has less arguments you can pass to it
- Testing environments runs in background and has dynamic config reloading, plus more specific testing arguments you can pass to it

It's done that way as I want to give people freedom to play around with things, however it's recommended by Caddy wiki to not have dynamic config reloading enabled in production, instead enable a admin endpoint which then will allow you to run commands such as `caddy test` and `caddy reload`.

If admin enpoint is off, you won't be able to issue those commands so it's recommended to enable admin endpoint for `localhost:2019`, then you can issue following command `podman exec -t <CONTAINER_NAME> /app/bin/caddy reload -c <CONFIG_PATH>` to reload the configuration file after changing it.

## `No file or path was found in CONFIG_PATH environment variable`

Our container images never ship with default configuration files so by default Caddy won't be able to know what to do. Users are expected to mount their own configuration files `caddy.json` or `Caddyfile` under `/app/configs` then point `CONFIG_PATH` environmental variable to that location.
