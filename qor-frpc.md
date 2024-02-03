# Container Launch

#### Execution / Configuration Errors

> **json: cannot unmarshal x into Go struct field y of type z**

You used wrong z on a y key. It's usually things like putting raw number(s) where a string should be instead or a string where a raw number(s) should be instead.

> **error unmarshaling JSON: while decoding JSON: json: cannot unmarshal string into Go value of type v1.ServerConfig**

One of the keys in the configuration is not wrapped properly in quotation marks ex. "", or it's a String instead of Int. Usually cause of this is following keys

```toml
bindAddr = "0.0.0.0" <-- wrap this in ""
bindPort = 7000 <-- leave this one be
auth.method = "token" <-- wrap this in ""
auth.token = "xxx" <-- wrap this in ""
```

> **open <directory>: no such file or directory**

If you're going to use TLS certificates on your frps/frpc instance, you need to manually mount a folder with the certificates in that directory. Common approach to this is to use Caddy to generate a Let's Encrypt certificate for you and mount it read-only to the container so it can use it.

# Entrypoint Script

#### Missing, unset etc.

> **Your DEPLOYMENT_TYPE environmental variable is invalid!**

This container bundles both frps and frpc into one and switches between each other based on the variable that you set before deploying the container. Valid types are either `server` or `client`for `DEPLOYMENT_TYPE`.
