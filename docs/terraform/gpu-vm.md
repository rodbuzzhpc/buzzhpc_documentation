---
title: Terraform – GPU Virtual Machines
description: Provision BuzzHPC GPU Virtual Machines using Terraform
tags:
  - Terraform
  - GPU VM
  - Virtual Machine
  - IaC
---

GPU Virtual Machines give you full root access to a dedicated GPU node. Use the `buzzhpc_compute_instance` resource with the `no-gpu-vm` profile to provision a VM.

---

## Resource: `buzzhpc_compute_instance`

```hcl
resource "buzzhpc_compute_instance" "vm" {
  project   = "system-catalog"
  workspace = var.workspace
  name      = var.vm_name

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
| `no-gpu-vm` | CA-QC-2 (Quebec) | H200 |

---

## Variables

| Variable | Type | Default | Description |
|---|---|---|---|
| `workspace` | string | `"test"` | Workspace within the system-catalog project |
| `vm_name` | string | `"my-gpu-vm"` | Unique name for the VM |
| `sku` | string | `"no-gpu-vm"` | Service profile SKU |
| `node_type` | string | `"H200"` | GPU node type |
| `gpu_count` | number | `1` | Number of GPUs to allocate |

---

## Outputs

| Output | Description |
|---|---|
| `vm_id` | Terraform resource ID (`<project>/<workspace>/<name>`) |
| `vm_status` | Current deployment status |

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

resource "buzzhpc_compute_instance" "vm" {
  project   = "system-catalog"
  workspace = "test"
  name      = "my-gpu-vm"

  spec = jsonencode({
    computeProfile = { name = "no-gpu-vm", systemCatalog = true }
    variables = [
      { name = "GPU Count", valueType = "text", value = "1" },
      { name = "Node Type", valueType = "text", value = "H200" }
    ]
  })
}

output "vm_id"     { value = buzzhpc_compute_instance.vm.id }
output "vm_status" { value = jsondecode(buzzhpc_compute_instance.vm.status).status }
```

!!! info
    Set `BUZZHPC_API_KEY` in your environment before running `terraform apply`.
