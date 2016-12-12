NowFap -  NOn Web Federated Authentication Portal
======

This project deploys COmanage using Ansible as a portal to handle
various non-web SSO scenarios. The portal needs an IdP for external
authentication. The IdP must adhere to the SAML 2.0 Web-SSO profile. The
EPPN is used to identify remote users and thus must be included in the
SAML response.

# Ansible playbooks and roles to install COmanage virtual machine.

This codebase contains all the roles/playbooks and templates to
configure a COmanage VMs.  The target Linux distribution for COmanage
server is Ubuntu Server 16.04

Machines are provisioned as follows:

```
ansible-playbook -i inventories/inventory.comanage comanage.yml
```

If you want to create a different inventory, you can do store them into:
`inventory/` and use your own settings.

Do not forget to initialize `sp_protocol`, `sp_hostname` and`sp_path` in
your host file. See [caveats](https://github.com/venekamp/nowfap-portal#do-not-forget-to-specify-parameters-for-mod_mellon)


# Roles
The playbook executes the following roles:

| Role       | Description |
| ---------- | ----------- |
common       | A number of command task, like package installation. |
auth_mellon  | Install and configure an Apache module for external SAML Authentication. |
php          | Php 5.6 is necessary for the underlying framework. |
mysql        | The framework requires a database. |
comanage     | COmanage provides a portal in which the use case are implemented. |
surfconext   | Necessary metadata is fetched and verified in order to make the SAML flow work. |
apache       | The portal is build with Apache. |
ldap         | LDAP is used to store attributes (ASP, OTP, SSH Pub key, etc) collected by COmanage. |
phpldapadmin | Web interface to an LDAP instance. |

# Caveats
There are number of caveats that you should be aware of.

## Install certificates yourself
The playbook currently does not setup certificates for you. Instead, it is
expected that certificates are already installed on the target machine.
To support this, two variables are defined:
- certificate
- certificate_key

The first one, `certificate` points to the installed certificate, while
`certificate_key` points to the corresponding key. Use your host file to
change the values, otherwise you will fallback to default values.

| Variable | Default value |
| -------- | ------------ |
| certificate     | /etc/ssl/cert/portal.example.org.pem    |
| certificate_key | /etc/ssl/private/portal.example.org.key |

## Do not forget to specify parameters for mod_mellon
In order for mod_mellon to do the right thing, it needs to know a few
things first. You need to do this for each host, as these values are
unique to each one. Well, at least the sp_hostname is. So, please define
the following in your host file:

| Variable | Description | Default value |
| -------- | ----------- | ------------- |
| sp_protocol | Protocol to be used | https:// |
| sp_hostname | Name of the host where mod_mellon is being used | portal.example.org |
| sp_path     | Path that follows the host name to create the full URI | 'registry/auth/sp' |
