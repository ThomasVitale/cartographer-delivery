# Cartographer Delivery

This project provides a [Carvel package](https://carvel.dev/kapp-controller/docs/latest/packaging) with [Cartographer](https://cartographer.sh) delivery chains to deploy workloads to a Kubernetes cluster based on GitOps or RegistryOps.

## Components

* cartographer-delivery

## Description

### Delivery: Basic

The `basic` delivery chain provides a simple Cartographer path consisting of the following stages:

* Monitor configuration source code repository with FluxCD;
* Deploy the workload to Kubernetes with Carvel kapp.

## Prerequisites

* Install the [`kctrl`](https://carvel.dev/kapp-controller/docs/latest/install/#installing-kapp-controller-cli-kctrl) CLI to manage Carvel packages in a convenient way.
* Ensure [kapp-controller](https://carvel.dev/kapp-controller) is deployed in your Kubernetes cluster. You can do that with Carvel
[`kapp`](https://carvel.dev/kapp/docs/latest/install) (recommended choice) or `kubectl`.

```shell
kapp deploy -a kapp-controller -y \
  -f https://github.com/vmware-tanzu/carvel-kapp-controller/releases/latest/download/release.yml
```

## Dependencies

Cartographer Delivery requires the Cartographer Blueprints package to be already installed in the cluster. You can install it from the [Kadras package repository](https://github.com/arktonix/kadras-packages).

## Installation

You can install the Cartographer Delivery package directly or rely on the [Kadras package repository](https://github.com/arktonix/kadras-packages)
(recommended choice).

Follow the [instructions](https://github.com/arktonix/kadras-packages) to add the Kadras package repository to your Kubernetes cluster.

If you don't want to use the Kadras package repository, you can create the necessary `PackageMetadata` and
`Package` resources for the Cartographer Delivery package directly.

```shell
kubectl create namespace carvel-packages
kapp deploy -a cartographer-delivery-package -n carvel-packages -y \
    -f https://github.com/arktonix/cartographer-delivery/releases/latest/download/metadata.yml \
    -f https://github.com/arktonix/cartographer-delivery/releases/latest/download/package.yml
```

Either way, you can then install the Cartographer Delivery package using [`kctrl`](https://carvel.dev/kapp-controller/docs/latest/install/#installing-kapp-controller-cli-kctrl).

```shell
kctrl package install -i cartographer-delivery \
    -p cartographer-delivery.packages.kadras.io \
    -v 0.1.0 \
    -n carvel-packages
```

You can retrieve the list of available versions with the following command.

```shell
kctrl package available list -p cartographer-delivery.packages.kadras.io
```

You can check the list of installed packages and their status as follows.

```shell
kctrl package installed list -n carvel-packages
```

## Configuration

The Cartographer Delivery package has the following configurable properties.

| Config | Default | Description |
|-------|-------------------|-------------|
| `delivery_chain` | `basic` | The type of delivery chain to use when deploying workloads. Options: `basic`, `acceptance`, `smoke`. |
| `service_account` | `default` | The ServiceAccount used by the delivery chain. |
| `git_implementation` | `go-git` | The Git implementation used by Flux. |

You can define your configuration in a `values.yml` file.

```yaml
delivery_chain: basic

service_account: default
git_implementation: go-git
```

Then, reference it from the `kctrl` command when installing or upgrading the package.

```shell
kctrl package install -i cartographer-delivery \
    -p cartographer-delivery.packages.kadras.io \
    -v 0.1.0 \
    -n carvel-packages \
    --values-file values.yml
```

## Documentation

For documentation specific to Cartographer, check out [cartographer.sh](https://cartographer.sh).

## References

This package is inspired by:

* the [examples](https://github.com/vmware-tanzu/cartographer/tree/main/examples) in the Cartographer project;
* the [Cartographer Catalog](https://github.com/vmware-tanzu/cartographer-catalog) package used in Tanzu Community Edition;
* the [set of supply chains](https://github.com/vrabbi/tap-oss/tree/main/packages/ootb-supply-chains) included in an example of Tanzu Application Platform OSS stack.

## Supply Chain Security

This project is compliant with level 2 of the [SLSA Framework](https://slsa.dev).

<img src="https://slsa.dev/images/SLSA-Badge-full-level2.svg" alt="The SLSA Level 2 badge" width=200>
