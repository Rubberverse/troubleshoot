## 🧤 Environmental Variables - CADDY_ENVIRONMENT

- **Your CADDY_ENVIRONMENT environmental variable is invalid!**

In order to run the image, you need to choose between two environments. Each one of them does something a bit different than the other one, mainly:

`TEST` will run Caddy in background and listen actively to any config changes, **NOT RECOMMENDED FOR USE IN PRODUCTION** by Caddy developers and us!

`PROD` will run Caddy but won't listen to any config changes

To choose your environment, pass CADDY_ENVIRONMENT with the environment type you want to use ex. `CADDY_ENVIRONMENT=PROD`. 

> [!NOTE]
> Instead of restarting your container, make use of Caddy's admin endpoint. Simply point it to localhost:2019 with `admin localhost:2019` and then run `podman exec -t container_name caddy reload` to reload the config

## 🏗️ Environmental Variables - ADAPTER_TYPE

- **Potentially invalid ADAPTER_TYPE value**

If you're using a config adapter that's not one of the following: `caddyfile`, `json`, `yaml` in your custom-built image, then you can discard this error.

However if you're using our Caddy image, it means that your `ADAPTER_TYPE` environmental variable is set **wrong**. Please make sure it's one of the supported values which is `caddyfile` or `json`! 

> [!CAUTION]
> Passing yaml will be seen as valid option but this might lack the modules needed to actually make use of it so your container might die afterwards

## ✈️ Environmental Variables - CONFIG_PATH

- **Your CONFIG_PATH is empty! It is required to launch the container successfully!**

You **must** specify a path where Caddyfile/caddy.json/caddy.yaml will residue inside the container. This can be _anything_ as long as the file exists in this path, by default it is recommended to utilize `/app/configs/Caddyfile` or `/app/configs/caddy.json`

> [!WARNING]
> Remember to mount a volume pointing to your local Caddyfile/caddy.json, otherwise you will get file not found error! By default it is recommended to mount your own config to `/app/configs/Caddyfile`
