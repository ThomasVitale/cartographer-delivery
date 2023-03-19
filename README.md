# Cartographer Delivery

![Test Workflow](https://github.com/kadras-io/cartographer-delivery/actions/workflows/test.yml/badge.svg)
![Release Workflow](https://github.com/kadras-io/cartographer-delivery/actions/workflows/release.yml/badge.svg)
[![The SLSA Level 3 badge](https://slsa.dev/images/gh-badge-level3.svg)](https://slsa.dev/spec/v0.1/levels)
[![The Apache 2.0 license badge](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
[![Follow us on Twitter](https://img.shields.io/static/v1?label=Twitter&message=Follow&color=1DA1F2)](https://twitter.com/kadrasIO)

A Carvel package providing [Cartographer](https://cartographer.sh) delivery chains to deploy workloads to a Kubernetes cluster based on GitOps or RegistryOps.

## 🚀&nbsp; Getting Started

### Prerequisites

* Kubernetes 1.24+
* Carvel [`kctrl`](https://carvel.dev/kapp-controller/docs/latest/install/#installing-kapp-controller-cli-kctrl) CLI.
* Carvel [kapp-controller](https://carvel.dev/kapp-controller) deployed in your Kubernetes cluster. You can install it with Carvel [`kapp`](https://carvel.dev/kapp/docs/latest/install) (recommended choice) or `kubectl`.

  ```shell
  kapp deploy -a kapp-controller -y \
    -f https://github.com/carvel-dev/kapp-controller/releases/latest/download/release.yml
  ```

### Dependencies

Cartographer Delivery requires the [Cartographer Blueprints](https://github.com/kadras-io/cartographer-blueprints) package. You can install it from the [Kadras package repository](https://github.com/kadras-io/kadras-packages).

### Installation

Add the Kadras [package repository](https://github.com/kadras-io/kadras-packages) to your Kubernetes cluster:

  ```shell
  kubectl create namespace kadras-packages
  kctrl package repository add -r kadras-packages \
    --url ghcr.io/kadras-io/kadras-packages \
    -n kadras-packages
  ```

<details><summary>Installation without package repository</summary>
The recommended way of installing the Cartographer Delivery package is via the Kadras <a href="https://github.com/kadras-io/kadras-packages">package repository</a>. If you prefer not using the repository, you can add the package definition directly using <a href="https://carvel.dev/kapp/docs/latest/install"><code>kapp</code></a> or <code>kubectl</code>.

  ```shell
  kubectl create namespace kadras-packages
  kapp deploy -a cartographer-delivery-package -n kadras-packages -y \
    -f https://github.com/kadras-io/cartographer-delivery/releases/latest/download/metadata.yml \
    -f https://github.com/kadras-io/cartographer-delivery/releases/latest/download/package.yml
  ```
</details>

Install the Cartographer Delivery package:

  ```shell
  kctrl package install -i cartographer-delivery \
    -p cartographer-delivery.packages.kadras.io \
    -v ${VERSION} \
    -n kadras-packages
  ```

> **Note**
> You can find the `${VERSION}` value by retrieving the list of package versions available in the Kadras package repository installed on your cluster.
> 
>   ```shell
>   kctrl package available list -p cartographer-delivery.packages.kadras.io -n kadras-packages
>   ```

Verify the installed packages and their status:

  ```shell
  kctrl package installed list -n kadras-packages
  ```

## 📙&nbsp; Documentation

Documentation, tutorials and examples for this package are available in the [docs](docs) folder.
For documentation specific to Cartographer, check out [cartographer.sh](https://cartographer.sh).

### Delivery: Basic

The `basic` delivery chain provides a simple Cartographer path consisting of the following stages:

* Monitor configuration source code repository with FluxCD;
* Deploy the workload to Kubernetes with Carvel kapp.

## 🎯&nbsp; Configuration

The Cartographer Delivery package can be customized via a `values.yml` file.

  ```yaml
  delivery_chain: basic
  ```

Reference the `values.yml` file from the `kctrl` command when installing or upgrading the package.

  ```shell
  kctrl package install -i cartographer-delivery \
    -p cartographer-delivery.packages.kadras.io \
    -v ${VERSION} \
    -n kadras-packages \
    --values-file values.yml
  ```

### Values

The Cartographer Delivery package has the following configurable properties.

<details><summary>Configurable properties</summary>

| Config | Default | Description |
|-------|-------------------|-------------|
| `delivery_chain` | `basic` | The type of delivery chain to use when deploying workloads. Options: `basic`. |
| `service_account` | `default` | The default `ServiceAccount` used by the delivery chain. |
| `git_access_secret` | `git-secret` | The Secret containing authentication credentials for the Git repository. |

</details>

## 🛡️&nbsp; Security

The security process for reporting vulnerabilities is described in [SECURITY.md](SECURITY.md).

## 🖊️&nbsp; License

This project is licensed under the **Apache License 2.0**. See [LICENSE](LICENSE) for more information.

## 🙏&nbsp; Acknowledgments

This package is inspired by:

* the [examples](https://github.com/vmware-tanzu/cartographer/tree/main/examples) in the Cartographer project;
* the original cartographer-catalog package used in [Tanzu Community Edition](https://github.com/vmware-tanzu/community-edition) before its retirement;
* the [set of delivery chains](https://github.com/vrabbi/tap-oss/tree/main/packages/ootb-supply-chains) included in an example of Tanzu Application Platform OSS stack.
* the out-of-the-box delivery chains provided by [Tanzu Application Platform](https://tanzu.vmware.com/application-platform).
