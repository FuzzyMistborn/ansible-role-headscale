# Headscale Installer and Configuration

An ansible role to install and configure [headscale](https://github.com/juanfont/headscale).  Inspiration and much of the configuration options come from [@kazauwa](https://github.com/kazauwa/anisble-role-headscale/).  I prefer my binary install method better though so I changed that part.

Install with `ansible-galaxy install fuzzymistborn.headscale`

## Features

- Installation and configuration of `headscale` binary.
- Copying/updating `headscale` config file.
- Updating binaries if there is an update and version is not pinned.

## Configuration

This role has a number of variables that can be configured.

Additionally, you can pin a specific version with `headscale_pinned_ver`.  By default the role fetches and installs the latest available version, and will run the update command if the binary is already present every time the role is run.  You can disable this by pinning to a specific version.  Here's an example if you wanted to set the version.

```yaml
headscale_download_latest_ver: false
headscale_pinned_ver: 0.16.1
```
By setting a pinned version, the updater commands will not run and a version will only be pulled if the installed version does not match the pinned version.

You can set the headscale config in yaml format.  See the [default config](headscale_config) as a starting point.

The other variables, like `headscale_gh_url`, `headscale_install_directory`, etc I do not recommend changing unless you want to customize the install.

See the release pages for [headscale](https://github.com/juanfront/headscale/releases) to find the correct distribution for your install.

### Example for Autorestic Config File

Autorestic configuration is copied to the root home directory by default. It can be changed to another user's home directory with the `autorestic_config_user` variable.

Below is an example that shows all the available options.

```yaml
autorestic_config_yaml:
  locations:
    docker:
      from: '/opt/docker'
      to:
        - local
        - b2_docker
  backends:
    local:
      type: local
      path: /backup
      key: 123
    b2_docker:
      type: s3
      path: 'b2_backend_url'
      key: b2_password
      env:
        AWS_ACCESS_KEY_ID: 1234
        AWS_SECRET_ACCESS_KEY: 1234abc
```

## Github API

This role utilizes the GitHub API to determine the latest release available.  By default, the role utilizes unauthenticated requests, which are [limited by GitHub](https://docs.github.com/en/rest/overview/resources-in-the-rest-api#rate-limiting) to 60 requests per hour.  Requests are associated with the originating IP address.  For most usecases, this is not an issue.  However, you may find yourself rate limited.  If you authenticate, you can make 5,000 requests per hour.

To authenticate, you must obtain a [Personal Access Token](https://github.com/settings/tokens/new).  The token does not need any scopes selected.  Then add the following variables:

```
github_api_user: fuzzymistborn
github_api_pass: YOUR_TOKEN
github_api_auth: yes
```

That's it!

### If you appreciate my work, please consider buying me a beer (or coffee, or whatever)
[![ko-fi](https://ko-fi.com/img/githubbutton_sm.svg)](https://ko-fi.com/E1E5796VZ)
