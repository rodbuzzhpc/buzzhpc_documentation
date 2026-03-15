---
title: Terraform – Managed Kubernetes
description: Provision BuzzHPC Managed Kubernetes clusters using Terraform
tags:
  - Terraform
  - Kubernetes
  - MKS
  - IaC
---

Managed Kubernetes clusters give you a fully managed control plane backed by BuzzHPC GPU nodes. Use the `buzzhpc_compute_instance` resource with an `mks-k8s` profile.

---

## Resource: `buzzhpc_compute_instance`

```hcl
resource "buzzhpc_compute_instance" "cluster" {
  project   = "system-catalog"
  workspace = var.workspace
  name      = var.cluster_name

  spec = jsonencode({
    computeProfile = {
      name          = var.sku
      systemCatalog = true
    }
    variables = [
      {
        name      = "GPU Count"
        valueType = "text"
        value     = tostring(var.gpu_count)
      },
      {
        name      = "Node Type"
        valueType = "text"
        value     = var.node_type
      }
    ]
  })
}
```

---

## Available SKUs

| SKU | Region | GPU Options |
|---|---|---|
| `mks-k8s-ca-qc-2` | CA-QC-2 (Quebec) | H200 |
| `mks-k8s` | CA-QC-1 (Quebec) | A40, H100 |

---

## Variables

| Variable | Type | Default | Description |
|---|---|---|---|
| `workspace` | string | `"test"` | Workspace within the system-catalog project |
| `cluster_name` | string | `"my-k8s-cluster"` | Unique name for the cluster |
| `sku` | string | `"mks-k8s-ca-qc-2"` | Use `mks-k8s-ca-qc-2` for CA-QC-2 or `mks-k8s` for CA-QC-1 |
| `node_type` | string | `"H200"` | `"H200"` for CA-QC-2; `"A40"` or `"H100"` for CA-QC-1 |
| `gpu_count` | number | `1` | Number of GPU nodes |

---

## Outputs

| Output | Description |
|---|---|
| `cluster_id` | Terraform resource ID (`<project>/<workspace>/<name>`) |
| `cluster_status` | Current deployment status |

---

## Full Example

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

resource "buzzhpc_compute_instance" "cluster" {
  project   = "system-catalog"
  workspace = "test"
  name      = "my-k8s-cluster"

  spec = jsonencode({
    computeProfile = { name = "mks-k8s-ca-qc-2", systemCatalog = true }
    variables = [
      { name = "GPU Count", valueType = "text", value = "1" },
      { name = "Node Type", valueType = "text", value = "H200" }
    ]
  })
}

output "cluster_id"     { value = buzzhpc_compute_instance.cluster.id }
output "cluster_status" { value = jsondecode(buzzhpc_compute_instance.cluster.status).status }
```

!!! info
    Set `BUZZHPC_API_KEY` in your environment before running `terraform apply`.
