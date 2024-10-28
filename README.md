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

