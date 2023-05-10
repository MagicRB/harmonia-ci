# harmonia

## Features

- http-ranges support for nar file streaming
- streaming build logs
- .ls file streaming
  - Note: doesn't contain `narOffset` in json response but isn't needed for
    `nix-index`
    
## Features added by this fork

- Speedup serving of smaller nar files by generating nar files using nix as a library instead of starting
  a `nix-store` process for each nar file
- Add `/serve/<narhash>/` endpoint to allow serving the content of package. 
  Also discovers index.html to allow serving websites directly from the nix store.

## Build

### Whole application

```bash
nix build -L
```

### Get a development environment:

``` bash
nix develop
```

## Configuration

Configuration is done via a `toml` file. The location of the configuration file
should be passed as env var `CONFIG_FILE`. If no config file is passed the
following default values will be used:

```toml
# default ip:hostname to bind to
bind = "127.0.0.1:8080"
# Sets number of workers to start in the webserver
workers = 4
# Sets the per-worker maximum number of concurrent connections.
max_connection_rate = 256
# binary cache priority that is advertised in /nix-cache-info
priority = 30
```

Per default we wont sign any narinfo because we don't have a secret key, to
enable this feature enable it by providing a path to a private key generated by
`nix-store --generate-binary-cache-key cache.example.com-1 /etc/nix/cache.sk /etc/nix/cache.pk`

```toml
# nix binary cache signing key
sign_key_path = "/run/secrets/key"
```

harmonia will fallback to the `SIGN_KEY_PATH` environment if `sign_key_path` is not set in configuration.

Logging can be configured with
[env_logger](https://docs.rs/env_logger/latest/env_logger/). The default value
is `info,actix_web=debug`. To only log errors use the following
`RUST_LOG=error` and to only disable access logging, use
`RUST_LOG=info,actix_web::middleware=error`

## Run tests

```bash
nix flake check -L
```


## Inspiration

- [eris](https://github.com/thoughtpolice/eris)
