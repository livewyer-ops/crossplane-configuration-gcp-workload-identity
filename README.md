# crossplane-configuration-gcp-workload-identity

A [Crossplane Configuration] for setting up GCP Workload Identity to enable Kubernetes workloads to authenticate with Google Cloud services using service accounts.

## Overview

This Configuration provides a Composite Resource Definition (XRD) and Composition for automating the setup of GCP Workload Identity, which allows Kubernetes pods to authenticate to Google Cloud services without storing service account keys.

The configuration automates:

- Creation of GCP Service Accounts
- IAM role bindings for GCP projects
- Kubernetes Service Account creation with appropriate annotations
- Workload Identity IAM policy bindings

## Features

- **Multi-Project Support**: Grant permissions across multiple GCP projects
- **Flexible IAM Roles**: Assign multiple IAM roles to the workload identity
- **Kubernetes Integration**: Automatically creates and configures Kubernetes Service Accounts
- **Customizable**: Control service account mounting and namespace placement

## Prerequisites

This Configuration requires:

- Crossplane >= v1.14.1
- [upbound/provider-family-gcp](https://marketplace.upbound.io/providers/upbound/provider-family-gcp) >= v2.2.0
- [crossplane-contrib/provider-kubernetes](https://marketplace.upbound.io/providers/crossplane-contrib/provider-kubernetes) >= v1.0.0
- [upbound/function-go-templating](https://marketplace.upbound.io/functions/upbound/function-go-templating) >= v0.11.0
- [upbound/function-auto-ready](https://marketplace.upbound.io/functions/upbound/function-auto-ready) >= v0.5.0

## Installation

Install this Configuration using the Crossplane CLI:

```shell
kubectl crossplane install configuration ghcr.io/livewyer-ops/crossplane-configuration-gcp-workload-identity:latest
```

Or create a Configuration resource:

```yaml
apiVersion: pkg.crossplane.io/v1
kind: Configuration
metadata:
  name: gcp-workload-identity
spec:
  package: ghcr.io/livewyer-ops/crossplane-configuration-gcp-workload-identity:latest
```

## Usage

Create a WorkloadIdentity resource:

```yaml
apiVersion: gcp.livewyer.io/v1alpha1
kind: WorkloadIdentity
metadata:
  name: my-app-workload-identity
  namespace: default
spec:
  forProvider:
    projectId: my-gcp-project
    serviceAccountName: my-app
    roles:
      - roles/storage.objectViewer
      - roles/cloudsql.client
    additionalProjects:
      - projectId: another-project
        roles:
          - roles/secretmanager.secretAccessor
```

### Parameters

| Parameter                      | Required | Description                                                                   |
| ------------------------------ | -------- | ----------------------------------------------------------------------------- |
| `projectId`                    | Yes      | GCP project ID where the service account will be created                      |
| `roles`                        | Yes      | List of IAM roles to grant in the primary project                             |
| `serviceAccountName`           | No       | Name for the Kubernetes Service Account                                       |
| `serviceAccountNamespace`      | No       | Namespace for the Kubernetes Service Account (defaults to resource namespace) |
| `additionalProjects`           | No       | List of additional projects with roles to grant access                        |
| `automountServiceAccountToken` | No       | Enable automatic token mounting (default: true)                               |

## Examples

See the [examples/](./examples/) directory for more usage examples:

- [workload-identity.yaml](./examples/workload-identity.yaml) - Basic workload identity with multi-project access

## Development

### Building the Configuration

Build the Configuration package using the Crossplane CLI:

```shell
crossplane xpkg build
```

With custom options:

```shell
crossplane xpkg build --package-root=. --package-file=gcp-workload-identity.xpkg --examples-root=./examples
```

### Publishing

Login to your registry:

```shell
crossplane xpkg login
```

Push the package:

```shell
crossplane xpkg push -f gcp-workload-identity.xpkg your-org/crossplane-configuration-gcp-workload-identity:v1.0.0
```

## License

MIT License - see [LICENSE](./LICENSE) for details

## Maintainer

Maintained by [Livewyer](https://livewyer.com) - <bowen@livewyer.com>

## Links

- [Crossplane Documentation](https://docs.crossplane.io)
- [GCP Workload Identity Documentation](https://cloud.google.com/kubernetes-engine/docs/how-to/workload-identity)
- [Creating Crossplane Configurations](https://docs.crossplane.io/latest/concepts/packages/#create-a-configuration)

[Crossplane Configuration]: https://docs.crossplane.io/latest/concepts/packages
