# Buildable Odoo application stack for Kubernetes on Wodby

Deploy Odoo with your own modules on Kubernetes with Wodby: your Git repository
holds your custom and community modules, and Wodby builds Odoo with them.

This repository defines the Wodby stack manifests and default service
composition for buildable Odoo.

<!-- wodby:generated:start -->

## Stack contract

- [Browse Wodby application stacks](https://wodby.com/stacks)
- [Wodby stack documentation](https://wodby.com/docs/2.0/stacks/)
- [Stack manifest reference](https://wodby.com/docs/2.0/stacks/template/)

## Service definitions

- [Buildable Odoo service](https://github.com/wodby/service-odoo-buildable)
- [Nginx (Odoo) service](https://github.com/wodby/service-odoo-nginx)
- [PostgreSQL service](https://github.com/wodby/service-postgres)
- [NFS provisioner service](https://github.com/wodby/service-nfs-provisioner)
- [OpenSMTPD service](https://github.com/wodby/service-opensmtpd)

## What's included

| Component / service | Default configuration |
| --- | --- |
| Odoo<br>`odoo` | required; enabled by default; volumes: `filestore` 20 GB; links: `db` → `postgres`, `filestore` → `filestore-nfs`, `sendmail` → `opensmtpd` |
| Nginx<br>`nginx` | required; enabled by default; main; links: `backend` → `odoo` |
| PostgreSQL<br>`postgres` | required; enabled by default; volumes: `data` 20 GB |
| Odoo data NFS storage<br>`filestore-nfs` | required; enabled by default; volumes: `data` 25 GB |
| OpenSMTPD<br>`opensmtpd` | optional; disabled by default |

Enabled optional services are selected by default but can be excluded when an
app is created. Disabled optional services are available but not selected by
default. Required services cannot be excluded.

## Validate the stack manifest

```bash
wodby stack validate-manifest stack.yml --org <org-id>
```

<!-- wodby:generated:end -->

## Deploy this stack

Connect a Git repository started from the
[Odoo boilerplate](https://github.com/wodby/odoo-boilerplate):

- `addons/` holds your Odoo modules. Every directory that holds modules is
  added to Odoo's addons path.
- `requirements.txt` lists the Python packages your modules need.

Choose the Odoo version (18, 19 or 20) when you create the app. The build
starts from the prebuilt [`wodby/odoo`](https://github.com/wodby/odoo) image for
that version and adds your modules. To run Odoo without your own modules, use
the [Odoo stack](https://github.com/wodby/stack-odoo).

Enter the administrator's password, and optionally a login, email, language and
the apps to install, in the Odoo service's settings when you create the app.
Your own modules can be among the apps to install. The first deployment installs
Odoo with that account; Odoo's database manager is turned off. Later deployments
update the installed modules whose files changed. New modules appear in Odoo's
Apps menu, where you install them.

Uploaded files and sessions are kept on the shared `filestore` volume, so Odoo
can run more than one replica. Enable OpenSMTPD, or set up an outgoing mail
server in Odoo, to send email.

## Maintain a custom version

1. Fork this repository.
2. Edit the stack manifest.
3. Import the repository as a [Git-backed stack](https://wodby.com/docs/2.0/stacks/create/#create-a-git-backed-stack).

When replacing or renaming a stack service, update every related link target
and derivative reference. Stack-local names and referenced service names are
distinct identifiers.
