# registry-package

This package provides registry functionality using [docker registry](https://github.com/distribution/distribution).

## Components

* registry

## Configuration

The following configuration values can be set to customize the registry installation.

### Global

| Value | Required/Optional | Description |
|-------|-------------------|-------------|
| `namespace` | Optional | The namespace in which to deploy registry. (Default: `registry`) |
| `ingress.ingress_class` | Optional | Ingress class to use for the Ingress resource (Default: `contour`) |
| `domain` | Required | Domain to use for the registry (Default: ` `) |
| `registry.admin.username` | Optional | Username to log into the registry (Default: `admin`) |
| `registry.admin.password` | Optional | Password to log into the registry (Default: `admin123!`) |
| `registry.secrets.htpasswd` | Optional | Htpasswd entry for username/password selected above, if username or password changes this needs to be regenerated (Default: result of `htpasswd -nb admin admin123!)`) |
| `registry.secrets.hashared` | Optional | Password to connect registries in HA (Default: `replicated`) |

## Usage Example

Install this package and then you should be able to log in with:

```
docker login registry.<DOMAIN> -u <userame> -p <password>
```

The registry will create an ingress using the wildcard certificate available on the cluster. The wildcard secret needs to be present in the registry namespace. SecretCopier operator and Certs package will help you with that.

## Develop checklist


1. Update your [config.json](./config.json) with the package info
2. Add [overlays](./src/bundle/config/overlays/) and [values](./src/bundle/config/values.yaml)
3. Test your bundle: `ytt --data-values-file src/example-values/minikube.yaml  -f target/bundle/config` providing a sample values file from [example-values](./src/examples-values/)
4. Build your bundle `./hack/build.sh`
5. Publish your bundle: `./hack/push.sh`
6. Add it to the [failk8s-repo](https://github.com/failk8s-packages/failk8s-repo) and publish the new repo and test the package from there, or [test with local files](./target/test)