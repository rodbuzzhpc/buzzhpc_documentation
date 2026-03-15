---
title: Terraform Provider – Getting Started
description: Install and configure the BuzzHPC Terraform provider to manage cloud resources as code
tags:
  - Terraform
  - Infrastructure as Code
  - IaC
---

The BuzzHPC Terraform provider lets you create, update, and destroy BuzzHPC resources using HashiCorp Terraform. All resource types available in the console — GPU VMs, Managed Kubernetes clusters, DevPods, Jupyter Notebooks, LLM Inference endpoints, Object Storage buckets, and Shared Filesystems — can be managed as code.

---

## Prerequisites

- [Terraform](https://developer.hashicorp.com/terraform/install) ≥ 1.0 installed
- A BuzzHPC API key (see [Administration](../administration/overview.md))

---

## Authentication

The provider reads your API key from the `BUZZHPC_API_KEY` environment variable.

```bash
export BUZZHPC_API_KEY="your-api-key-here"
```

!!! info
    Never hardcode your API key in `.tf` files. Use the environment variable or a secrets manager such as HashiCorp Vault.

---

## Provider Configuration

Add the provider block to your Terraform configuration:

```hcl
terraform {
  required_providers {
    buzzhpc = {
      source  = "buzzhpc/buzzhpc"
      version = "~> 0.1"
    }
  }
}

provider "buzzhpc" {}
```

Then initialise the working directory:

```bash
terraform init
```

---

## Projects and Workspaces

Every BuzzHPC resource belongs to a **project** and a **workspace**. When using the system catalog service profiles (the standard SKUs listed in the console) you must set:

```hcl
project   = "system-catalog"
workspace = "<your-workspace-name>"
```

Your workspace name matches the workspace you have already created in the BuzzHPC console.

---

## Resource Types

| Terraform Resource | What it creates |
|---|---|
| `buzzhpc_compute_instance` | GPU VMs, Managed Kubernetes clusters, DevPods |
| `buzzhpc_service` | Jupyter Notebooks, LLM Inference endpoints, Object Storage, Shared Filesystems |

---

## Quick Example

```hcl
terraform {
  required_providers {
    buzzhpc = { source = "buzzhpc/buzzhpc" }
  }
}

provider "buzzhpc" {}

resource "buzzhpc_service" "notebook" {
  project   = "system-catalog"
  workspace = "test"
  name      = "my-notebook"

  spec = jsonencode({
    serviceProfile = { name = "jupyter-notebook-v4-ca-qc-2", systemCatalog = true }
    variables = [
      { name = "GPU Count", valueType = "text", value = "1" },
      { name = "Node Type", valueType = "text", value = "H200" },
      { name = "Pod Image", valueType = "text", value = "jupyter/minimal-notebook:latest" }
    ]
  })
}

output "notebook_status" {
  value = jsondecode(buzzhpc_service.notebook.status).status
}
```

```bash
terraform apply
```

---

## Next Steps

- [GPU Virtual Machines](gpu-vm.md)
- [Managed Kubernetes](kubernetes.md)
- [DevPods](devpods.md)
- [Jupyter Notebooks](jupyter-notebook.md)
- [LLM Inference](llm-inference.md)
- [Object Storage](object-storage.md)
- [Shared Filesystem](shared-filesystem.md)
