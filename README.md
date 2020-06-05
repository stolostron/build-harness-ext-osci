## Extensions to `build-harness`

This repo is based on our build-harness-extensions repo, but has been modified to
work with the OpenShift CI pipeline.

This repo is structured just like `build-harness`, and is part of our
image-builder images. The build harness is at `/opt/build-harness/build-harness`.
The extensions are at `/opt/build-harness/build-harness-extensions`.

```BUILD_HARNESS_EXTENSIONS_PATH```

In order to use the build harness and extensions in OpenShift CI, add the
following to your `Makefile`:

```
    -include /opt/build-harness/Makefile.build-harness-openshift-ci
```
