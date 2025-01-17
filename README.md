ansible-role-atuin
==================

Patrick Wagstrom 

Overview
--------

[Atuin][https://atuin.sh/] is a great tool for collecting your shell information across different machines and making it available to other machines. It does this securely and easily. This role installs the atuin binary on your Raspbian or MacOS system.

Configuration
-------------

This role requires configuration depending on whether or not you're installing just the client or also installing the server.

* `sync_address`: Full http/https URL including port of where the atuin sync server is installed.
* `sync_username`: The username to use on the sync server
* `sync_login`: Boolean depending on whether or not you want to login to the sync server. If this is false, you don't need to add in the other sync information.
* `fish_install`: Boolean on whether or not to install the extensions for fish shell.
* `fish_extra`: Usually a multiline piece of code that can be inserted into your fish configuration to better support tweaks for Atuin.
* `fish_disable_ctrl_r`: Boolean on whether or not to disable the default fish ctrl-r behavior in favor of the Atuin behavior.
* `fish_disable_up_arrow`: Boolean on whether or not to disable the default fish up arrow behavior in favor of the Atuin behavior.
* `hostname_fix`: Boolean on whether to install a fish script that sets the `ATUIN_HOST_NAME` appropriately. Otherwise sometimes it gets set to a short hostname.

The following configuration settings are needed if you plan on installing the server. If you're not running your own server, then you don't need to worry about these and it should just work with the sync information above.

* `server_install`: Boolean on whether or not to install the server too
* `server_dir`: Path where the server gets installed
* `server_tls`: Boolean on whether or not to use TLS
* `server_cert_path`: If using TLS the full path of concatendated certificate and full chain in pem format.
* `server_pkey_path`: If using TLS, the full path of the private key in pem format.
* `server_open_registration`: Boolean on whether or not to enable open registration
* `server_port`: Integer port to run the server on.
* `server_db_password`: Your password to connect to the database server
* `server_db_name`: The database name on the database server.
* `server_db_username`: The username that is used to connect to the database server.

### Example Configuration

For the client, this is what I have. Note, `atuin_sync_password` and `atuin_sync_key` are set in my `secrets.yml` file.

```yaml
  vars:
    atuin:
      sync_address: "https://my_atuin_host:8888"
      sync_username: "my_atuin_username"
      sync_login: true
      fish_install: true
      fish_extra: |-2
        bind \ct _atuin_search
        if bind -M insert > /dev/null 2>&1
            bind -M insert \ct _atuin_search
        end
      fish_disable_ctrl_r: true
      fish_disable_up_arrow: true
      hostname_fix: true
```

For the server, this is what I have. Note, in addition to `atuin_sync_password` and `atuin_sync_key` I also have `atuin_server_db_password` set in my `secrets.yml` file.

```yaml
  vars:
    atuin:
      server_dir: "/home/myusername/Docker/atuin"
      server_install: true
      server_tls: true
      server_cert_path: "/root/.config/fullchain.pem"
      server_pkey_path: "/root/.config/privkey.pem"
      server_open_registration: true
      server_port: 8888
      server_db_name: atuin
      server_db_username: atuin
```

Issues
------

This role has only really been tested on Raspbian systems and MacOS systems. There are probably lots of different issues out there. Like, on 64 bit Raspberry Pis running 32 bit versions of Raspbian it installs the 64 bit executable, which still works because it's completely statically linked.

This also downloads a binary for Raspbian from my personal webserver. Yup, you're just downloading a binary from the internet. It's up to you if you trust me.