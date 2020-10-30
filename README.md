# k8s-cert-syncer
Sync certificates from Kubernetes

## Motivation
Kubernetes is great. [Cert-manager](https://cert-manager.io/) makes it
easy to manage/issue certificates in a Kubernetes cluster. In 1 place
you can configure certificates, manage DNS-provider API keys and ACME certificates.

Unfortunately, these certs are only _"easy to use"_ if your workload is
also going through Kubernetes. If you have other nodes, they generally need
to manage their own certs, and if those nodes are not on the Internet (such as
on a NAT'd LAN) then you can't skirt by with the simplicity of LetsEncrypt HTTP
validation. 

k8s-cert-syncer solves that problem with an easy-to-use script that will
fetch certificates from the Kubernetes API and save them locally. No need
to have your machine be a part of your cluster. 

## Requirements
### Admin script
- `jq` - v1.6+
- `kubectl`

It is assumed that your environment kubectl is configured for the cluster
where the certs are.

You must have the ability to create/update/delete
these resources in the target namespace:
- `Role`
- `RoleBinding`
- `ServiceAccount`

### Client script
- `jq` - v1.6+
- `curl`

## Quick Start
We will assume you have a _Certificate_ named `my-cert` in the `default` namespace.

To create the sync script:
```sh
./admin.sh mkscript default my-cert > client.sh
chmod +x ./client.sh
```

The `client.sh` file that was written **contains secrets**.
Protect access to it and do not share with anyone who you don't
want to have access to this certificate.

You can run the client script with:
```sh
./client.sh cert.pem key.pem
```

This will write 2 files, `cert.pem` and `key.pem`. You can provide
alternative paths so long as the executing user has permissions to
write to them!
