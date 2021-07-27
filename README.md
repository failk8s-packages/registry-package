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
2. Update your [vendir.yml](./src/bundle/vendir.yml) with your upstreams
3. `vendir sync` in `src/bundle` to fetch your new upstream files
4. Add [overlays](./src/bundle/config/overlays/) and [values](./src/bundle/config/values.yaml)
5. Update your [bundle.yml](./src/bundle/.imgpkg/bundle.yml) file
6. Test your bundle: `ytt -f config`
7. Lock images used: `kbld -f . --imgpkg-lock-output .imgpkg/images.yml`
8. Publish your bundle: `imgpkg push --bundle quay.io/failk8s/registry-package:develop --file .`. These steps can be done via [hack/build-package.sh](./hack/build-package.sh)
9. Package up your k8s manifests and test in k8s [hack/package-manifests.sh](./hack/package-manifests.sh). The files will be in `target` folder.
