# Cartographer Delivery

<a href="https://slsa.dev/spec/v0.1/levels"><img src="https://slsa.dev/images/gh-badge-level3.svg" alt="The SLSA Level 3 badge"></a>

This project provides a [Carvel package](https://carvel.dev/kapp-controller/docs/latest/packaging) with [Cartographer](https://cartographer.sh) delivery chains to deploy workloads to a Kubernetes cluster based on GitOps or RegistryOps.

## Description

### Delivery: Basic

The `basic` delivery chain provides a simple Cartographer path consisting of the following stages:

* Monitor configuration source code repository with FluxCD;
* Deploy the workload to Kubernetes with Carvel kapp.

## Prerequisites

* Kubernetes 1.24+
* Carvel [`kctrl`](https://carvel.dev/kapp-controller/docs/latest/install/#installing-kapp-controller-cli-kctrl) CLI.
* Carvel [kapp-controller](https://carvel.dev/kapp-controller) deployed in your Kubernetes cluster. You can install it with Carvel [`kapp`](https://carvel.dev/kapp/docs/latest/install) (recommended choice) or `kubectl`.

  ```shell
  kapp deploy -a kapp-controller -y \
    -f https://github.com/vmware-tanzu/carvel-kapp-controller/releases/latest/download/release.yml
  ```

## Dependencies

Cartographer Delivery requires the Cartographer Blueprints package to be already installed in the cluster. You can install it from the [Kadras package repository](https://github.com/kadras-io/kadras-packages).

## Installation

First, add the [Kadras package repository](https://github.com/kadras-io/kadras-packages) to your Kubernetes cluster.

  ```shell
  kubectl create namespace kadras-packages
  kctrl package repository add -r kadras-repo \
    --url ghcr.io/kadras-io/kadras-packages \
    -n kadras-packages
  ```

Then, install the Cartographer Delivery package.

  ```shell
  kctrl package install -i cartographer-delivery \
    -p cartographer-delivery.packages.kadras.io \
    -v 0.2.1 \
    -n kadras-packages
  ```

### Verification

You can verify the list of installed Carvel packages and their status.

  ```shell
  kctrl package installed list -n kadras-packages
  ```

### Version

You can get the list of Cartographer Delivery versions available in the Kadras package repository.

  ```shell
  kctrl package available list -p cartographer-delivery.packages.kadras.io -n kadras-packages
  ```

## Configuration

The Cartographer Delivery package has the following configurable properties.

| Config | Default | Description |
|-------|-------------------|-------------|
| `delivery_chain` | `basic` | The type of delivery chain to use when deploying workloads. Options: `basic`. |
| `service_account` | `default` | The ServiceAccount used by the delivery chain. |
| `git_implementation` | `go-git` | The Git implementation used by Flux. |
| `gitops.access_secret` | `git-secret` | The Secret containing credentials to access the specified Git repository. |

You can define your configuration in a `values.yml` file.

```yaml
delivery_chain: basic

service_account: default
git_implementation: go-git

gitops:
  access_secret: git-secret
```

Then, reference it from the `kctrl` command when installing or upgrading the package.

  ```shell
  kctrl package install -i cartographer-delivery \
    -p cartographer-delivery.packages.kadras.io \
    -v 0.2.1 \
    -n kadras-packages \
    --values-file values.yml
  ```

## Upgrading

You can upgrade an existing package to a newer version using `kctrl`.

  ```shell
  kctrl package installed update -i cartographer-delivery \
    -v <new-version> \
    -n kadras-packages
  ```

You can also update an existing package with a newer `values.yml` file.

  ```shell
  kctrl package installed update -i cartographer-delivery \
    -n kadras-packages \
    --values-file values.yml
  ```

## Other

The recommended way of installing the Cartographer Delivery package is via the [Kadras package repository](https://github.com/kadras-io/kadras-packages). If you prefer not using the repository, you can install the package by creating the necessary Carvel `PackageMetadata` and `Package` resources directly using [`kapp`](https://carvel.dev/kapp/docs/latest/install) or `kubectl`.

  ```shell
  kubectl create namespace kadras-packages
  kapp deploy -a cartographer-delivery-package -n kadras-packages -y \
    -f https://github.com/kadras-io/cartographer-delivery/releases/latest/download/metadata.yml \
    -f https://github.com/kadras-io/cartographer-delivery/releases/latest/download/package.yml
  ```

## Support and Documentation

For support and documentation specific to Cartographer, check out [cartographer.sh](https://cartographer.sh).

## References

This package is inspired by:

* the [examples](https://github.com/vmware-tanzu/cartographer/tree/main/examples) in the Cartographer project;
* the [Cartographer Catalog](https://github.com/vmware-tanzu/cartographer-catalog) package used in Tanzu Community Edition;
* the [set of supply chains](https://github.com/vrabbi/tap-oss/tree/main/packages/ootb-supply-chains) included in an example of Tanzu Application Platform OSS stack.

## Supply Chain Security

This project is compliant with level 3 of the [SLSA Framework](https://slsa.dev).

<img src="https://slsa.dev/images/SLSA-Badge-full-level3.svg" alt="The SLSA Level 3 badge" width=200>
