# Troubleshooting 1001

Your one-stop guide to all sorts of errors!

### ❌ Exec format error

You're running a wrong image for your architecture. Either use qemu-server to emulate it or switch to correct architecture.

### ❌ bind 80: permission denied

You're missing a sysctl flag that allows the container to bind to privileged ports. This can be assigned to your container user from `docker-compose` or `podman run`. Might also need to repeat the same for your actual user if running rootlessly ex. your host user will need sysctl permitting him to bind below privileged ports (1000 by default)

### ❌ Invalid value or blank environment for CADDY_ENVIRONMENT, Container will fallback to Production launch 

This image switches between two configurations, one tailored for non-production, testing use and another one intended for production. Non-production value of CADDY_ENVIRONMENT is `test` where production one is `prod`.

The difference between them both is that one enables config watching so Caddy will dynamically reload config on any change where the other one has that turned off as recommended by Caddy wiki itself. If you need a way to dynamically reload your configuration, turn on admin endpoint with `admin localhost:2019` in Caddyfile and issue command `podman exec -t qor-caddy /app/caddy reload -c /app/configs/Caddyfile`.

### ❌ No file or path was found in CONFIG_PATH environment variable

You need to specify a path where a Caddyfile, caddy.json or different type of Caddy configuration residues in. By default it's recommended to use `/app/configs/Caddyfile` and mount your own Caddyfile in that location, if you don't then it will just fail to launch since v0.17.0 no longer bundles a Caddyfile alongside the image.

### ⚠️ Potentially invalid ADAPTER_TYPE value

If you're using a config adapter that's not one of the following: `caddyfile` or `json` in your custom-built image, then you can discard this warning.

However if you're using our Caddy image, it means that your `ADAPTER_TYPE` environmental variable is set **wrong**. Please make sure it's one of the supported values which is `caddyfile` or `json`! Technically we also allow yaml as valid option but we have no idea if it actually works, probably not since it needs a extra module so don't use that one. This isn't enforced due to allowing users self-building our images to have freedom of picking anything they want.

If this warning message bothers you in your self-build images, just remove the `else` block at line 37 of `docker-entrypoint.sh`
