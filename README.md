vault need multiple keys to unseal it 

multiple cloud provider has there own secret manager azure vault, aws secret manager etc.

vault have plugins and work in multiple clouds and even in the on-prem datacenters

---

vault can be used as dev mode in which it automatically run in unsealed mode with 1 unseal key and token already generated
```bash
vault server -dev
```
---
vault installation : done by using official documents 

after that go to /etc/vault.d/vault.hcl and configure the vault accordingly if u dont have the certificates then comment the https section and uncomment the http section
```bash
vi /etc/vault.d/vault.hcl
```
vault.hcl

```hcl
ui = true #this is for enabling the ui

storage "file" { #storage location
  path = "/opt/vault/data"
}

# HTTP listener you can change the the ip address so that it can be accessed via that ip only for example your local ip
listener "tcp" {
  address = "0.0.0.0:8234"
  tls_disable = 1
}
```

---

access the ui from that ip and port given in http address

public-ip-of-the-server:8234

to run vault cli use this command
```bash
export VAULT_ADDR="http://127.0.0.1:8234"
```

there are 2 methods to generate keys either on the ui (simple mode) or on the cli

command for the cli

```bash
vault operater init
```
by default it generate 5 keys and 3 threshold means 3 keys are necessary to unseal it you can also specify how many keys and threshold do you want
```bash
vault operator init -key-shares=3 -key-threshold=2
```
one token is also generated with the keys that is required as password to access vault

we have multiple authentication method to access in the vault by hich u can login

one is the basic one using the root token we have generated

mount path 

every auth is mounted to a particular path 

token is mounted to auth/token

userpass is mounted to auth/userpass

while enabling we can also specify custom path for example
```bash
vault auth enable -path=custom-path userpass

vault auth commands

vault login

#or

vault login token=your_token

vault auth enable #enabling other authentication method

vault auth list #how many type of authentication is enabled

vault auth disable

vault token create

vault token revoke token
```
creating other users
```bash
vault auth enable userpass

vault write auth/userpass/users/sahil password=sahil7879

vault delete auth/userpass/users/sahil25

vault delete auth/userpass/users/sahil23
```
Success! Data deleted (if it existed) at: auth/userpass/users/sahil23

even if the user dont exist it still shows success

there is no way to get all the users list in the CLI but can be viewed in UI
```bash
vaut auth enable github

vault write auth/github/config organization=vaultdem

vault login -method=github token=""

vault write auth/github/map/teams/dev value=dev-policy

```
secrets
```bash
vault secrets enable kv

vault secrets enable -path=sahil kv

vault kv put sahil/webui user=sahil pass=xyz

vault kv get sahil/webui

vault kv list sahil/

vault kv get sahil/webui

==== Data ====
Key     Value
---     -----
pass    xyz
user    sahil

vault secrets tune -default-lease-ttl=72h sahil/

vault kv delete sahil/webui

vault secrets disable sahil

vault secrets enable -version=2 -path=sahil2 kv

vault write kv/config max_versions=4
vault read kv/config

vault kv put sahil2/webui name="sahil" pass="1111"
vault kv put sahil2/webui name="sahil" pass="2222"
vault kv put sahil2/webui name="sahil" pass="3333"

vault kv get -version=1 sahil2/webui
vault kv get -version=2 sahil2/webui

vault kv delete -versions=1 sahil2/webui
vault kv undelete -versions=1 sahil2/webui

vault kv destroy -versions=2 sahil2/webui


vault kv metadata get sahil2/webui
vault kv metadata put -mount=sahil2/webui -max-versions 3 -delete-version-after="1h1m1s" sahil2

vault kv metadata delete sahil2/webui

vault secrets disable sahil2

```
