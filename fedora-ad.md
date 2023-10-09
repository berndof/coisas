```bash
sudo dnf install realmd sssd oddjob oddjob-mkhomedir adcli samba-common-tools -y
sudo dnf install krb5-workstation -y

sudo realm discover DOMÍNIO

sudo realm join DOMÍNIO -v --user USUARIO

```

### configuração do kerberos

```bash
sudo nano /etc/krb5.conf
```
```
[libdefaults]
    default_realm = SCCLGS.INTRA
    default_tkt_enctypes = aes256-cts-hmac-sha1-96 aes128-cts-hmac-sha1-96 arcfour-hmac
    default_tgs_enctypes = aes256-cts-hmac-sha1-96 aes128-cts-hmac-sha1-96 arcfour-hmac
    permitted_enctypes = aes256-cts-hmac-sha1-96 aes128-cts-hmac-sha1-96 arcfour-hmac

[realms]
    SCCLGS.INTRA = {
        kdc = ad.scclgs.intra
        admin_server = ad.scclgs.intra
        default_domain = scclgs.intra
    }

[domain_realm]
    .scclgs.intra = SCCLGS.INTRA
    scclgs.intra = SCCLGS.INTRA
```
### configuração do sssd

```bash
sudo nano /etc/sssd/sssd.conf
```
```
[sssd]
domains = scclgs.intra
config_file_version = 2
services = nss, pam

[domain/scclgs.intra]
ad_domain = scclgs.intra
krb5_realm = SCCLGS.INTRA
realmd_tags = manages-system joined-with-adcli 
cache_credentials = True
id_provider = ad
krb5_store_password_if_offline = True
default_shell = /bin/bash
ldap_id_mapping = True
use_fully_qualified_names = True
fallback_homedir = /home/%u@%d
access_provider = ad
```
```bash
sudo systemctl start sssd
sudo systemctl enable sssd

su - username@scclgs.intra
```
