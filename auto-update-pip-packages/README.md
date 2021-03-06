# OpenFisca Operations

## Auto-update pip packages

### Install

Install the cron job. As root:

```sh
cp cron /etc/cron.d/openfisca-auto-update-pip-packages
systemctl restart cron
```

You may customize these values to adapt them to your server:
- the full path to the `update-openfisca-virtualenv.sh` script in the cron file
- the Bash variables in `update-openfisca-virtualenv.sh` header (`PIP_PACKAGES` and `VENV_ACTIVATE`)

### Configure user permissions

Allow some commands for `openfisca` user to be used with `sudo` without typing any password. As root:

```sh
visudo
```

Then add these lines to the file under the `User privilege specification` section:

```
openfisca ALL=(ALL) NOPASSWD: /bin/systemctl restart openfisca-web-api.service
openfisca ALL=(ALL) NOPASSWD: /bin/systemctl restart legislation-explorer.service
```

This will allow the `update-openfisca-virtualenv.sh` script, which runs as the `openfisca` user, to restart the services.

### Configure emails sending

Cron sends emails containing the output of the script. The recipient is `openfisca`.
You can configure the email target by editing `/etc/aliases`.

A common value is: `openfisca: webmaster@openfisca.fr`.

The emails sent contain this kind of content:

```
- New version of package OpenFisca-France was found on PyPI: current=4.1.7, new=12.0.4
- New version of package OpenFisca-Web-API was found on PyPI: current=2.0.0, new=3.0.0
- New version(s) of PyPI package(s) were found and installed. Restarting openfisca-web-api.service and legislation-explorer.service as a consequence.
- Triggering an HTTP request on https://api.openfisca.fr/ to pre-load OpenFisca-Web-API simulation results, so the first user will not have to wait. Response body:
{"apiVersion": 1, "message": "Welcome, this is OpenFisca Web API.", "method": "/"}
```

### Update packages manually

To update pip packages in `openfisca` virtualenv, run:

```sh
cd openfisca-ops/auto-update-pip-packages
./update-openfisca-virtualenv.sh
```
