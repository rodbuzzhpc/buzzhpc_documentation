---
title: Terraform – DevPods
description: Provision BuzzHPC Developer Pods using Terraform
tags:
  - Terraform
  - DevPods
  - Developer Pods
  - IaC
---

Developer Pods (DevPods) are on-demand containerised development environments running on bare-metal GPU nodes. Use the `buzzhpc_compute_instance` resource with a `managed-developer-pods-v2` profile.

---

## Resource: `buzzhpc_compute_instance`

```hcl
resource "buzzhpc_compute_instance" "devpod" {
  project   = "system-catalog"
  workspace = var.workspace
  name      = var.pod_name

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
| `managed-developer-pods-v2-ca-qc-2` | CA-QC-2 (Quebec) | H200 |
| `managed-developer-pods-v2` | CA-QC-1 (Quebec) | A40, H100 |

---

## Variables

| Variable | Type | Default | Description |
|---|---|---|---|
| `workspace` | string | `"test"` | Workspace within the system-catalog project |
| `pod_name` | string | `"my-devpod"` | Unique name for the DevPod |
| `sku` | string | `"managed-developer-pods-v2-ca-qc-2"` | Use `managed-developer-pods-v2-ca-qc-2` for CA-QC-2 or `managed-developer-pods-v2` for CA-QC-1 |
| `node_type` | string | `"H200"` | `"H200"` for CA-QC-2; `"A40"` or `"H100"` for CA-QC-1 |
| `gpu_count` | number | `1` | Number of GPUs to allocate |

---

## Outputs

| Output | Description |
|---|---|
| `pod_id` | Terraform resource ID (`<project>/<workspace>/<name>`) |
| `pod_status` | Current deployment status |

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

resource "buzzhpc_compute_instance" "devpod" {
  project   = "system-catalog"
  workspace = "test"
  name      = "my-devpod"

  spec = jsonencode({
    computeProfile = { name = "managed-developer-pods-v2-ca-qc-2", systemCatalog = true }
    variables = [
      { name = "GPU Count", valueType = "text", value = "1" },
      { name = "Node Type", valueType = "text", value = "H200" }
    ]
  })
}

output "pod_id"     { value = buzzhpc_compute_instance.devpod.id }
output "pod_status" { value = jsondecode(buzzhpc_compute_instance.devpod.status).status }
```

!!! info
    Set `BUZZHPC_API_KEY` in your environment before running `terraform apply`.
