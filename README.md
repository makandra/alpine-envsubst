# alpine-envsubst

Docker image based on alpine to use `envsubst` (part of the `gettext` package) to substitute environmen variables in init containers.

# Usage

The entrypoint expects two parameters which are the source file and the target file where the rendered output is stored.

Example:

```
docker run -v $(pwd)/config:/config mee /config/config.template /config/config.rendered
```

## k8s example with initContainer

```
initContainers:
  - name: render-config
    image: ghcr.io/makandra/alpine-envsubst:v1.0.0
    args: ["/source/config.json", "/rendered/config.json"]
    env:
      - name: SOMEENV
        valueFrom:
          secretKeyRef:
            name: some_secret
            key: some_key
    volumeMounts:
    - name: template-config
      mountPath: /source/config.json
      subPath: config.json
    - mountPath: /rendered
      name: rendered-configuration
volumes:
- name: template-config
  configMap:
    name: template-config
    items:
    - key: config.json
      path: config.json
- name: rendered-configuration
  emptyDir:
    sizeLimit: 5Mi
```

The `rendered-configuration` volume can then be mounted in the containers requiring the configuration.
