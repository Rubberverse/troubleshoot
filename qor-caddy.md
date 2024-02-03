# Container Launch

#### Permission issues and errors

> Exec format error

You're running a wrong image for your architecture. Either use qemu-server to emulate it or switch to correct architecture. It might also be an issue with the container, if so create an issue

> **bind 80: permission denied**

Alpine containers will refuse to bind to ports 80 and 443 no matter what you're going to do so your best bet is to make it use ports higher than 1000. Debian image doesn't seem to have such limitation.

> [!NOTE]
> For plugin usage guide, it's recommended to check out each plugins repository for configuration guidance. They provide examples there on how to configure them! Also keep in mind that not everything included in this image will be compatible when used together, some things may clash so make sure to test stuff before pushing it live. You can find list of plugins (aka modules) on our main repo, just click on the name and it will show you their repository - https://github.com/Rubberverse/qor-caddy

# Entrypoint Script

#### Missing, unset etc.

> **Your CADDY_ENVIRONMENT environmental variable is invalid!**

This image switches between two configurations, one tailored for non-production, testing use and another one inteded for production. Non-production value of CADDY_ENVIRONMENT is `test` where production one is `prod`.

The difference between them both is that one enables config watching so Caddy will dynamically reload config on any change where the other one has that turned off as recommended by Caddy wiki itself. If you need a way to dynamically reload your configuration, turn on admin endpoint with `admin localhost:2019` in Caddyfile and issue command `podman exec -t qor-caddy caddy reload /app/configs/Caddyfile`.

> **Your CONFIG_PATH is empty! It is required to launch the container successfully!**

You need to specify a path where a Caddyfile, caddy.json or different type of Caddy configuration residues in. By default it's recommended to use `/app/configs/Caddyfile` and mount your own Caddyfile in that location, if you don't then it will just fail to launch since v0.17.0 no longer bundles a Caddyfile alongside the image.

> **Potentially invalid ADAPTER_TYPE value**

If you're using a config adapter that's not one of the following: `caddyfile`, `json`, `yaml` in your custom-built image, then you can discard this error.

However if you're using our Caddy image, it means that your `ADAPTER_TYPE` environmental variable is set **wrong**. Please make sure it's one of the supported values which is `caddyfile` or `json`! Technically we also allow yaml as valid option but we have no idea if it actually works, probably not since it needs a extra module so don't use that one.
