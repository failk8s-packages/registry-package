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
| `create_namespace` | Optional |Whether the namespace should be created. (Default: `True`) |
| `useContour` | Optional | Should it create an HTTPRoute instead of an Ingress (Default: `False`)|
| `useCertManager` | Optional | Use TLS for access with either a ClusterIssuer or a wildcard cert  (Default: `True`)|
| `useStorage` | Optional | Should a PVC be created? (Default: `True`) |
| `domain` | Required | Domain to use for the registry (Default: ` `) |
| `registry.storage.size` | Optional | Size of the PVC (Default: `10Gi`) |
| `registry.storage.storage_class` | Optional | Storage class to use for the PVC resource (Default: `default`) |
| `registry.ingress.ingress_class` | Optional | Ingress class to use for the Ingress resource (Default: `contour`) |
| `registry.certmanager.secret.name` | Optional | Name of the secret with Wildcard cert or to hold the cert if ClusterIssuer (Default: ``) |
| `registry.auth.type` | Optional | Is the registry authenticated? Values are `any` or `htpasswd` (Default: `any` so non authenticated) |
| `registry.auth.htpasswd.users` | Optional | List of users to give access to (at least one needs to be define if `htpasswd` is used. These will have `name` and `password` (Default: ``) |

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
5. Add it to the [failk8s-repo](https://github.com/failk8s-packages/failk8s-repo) and publish the new repo and test the package from there, or [test with local files](./target/test)

**NOTE**: `develop` versions will not be image locked and will have a version of 0.0.0+develop to comply with semver.