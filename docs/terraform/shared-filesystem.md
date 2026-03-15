---
title: Terraform – Shared Filesystem
description: Provision persistent NFS-compatible Shared Filesystems on BuzzHPC using Terraform
tags:
  - Terraform
  - Shared Filesystem
  - NFS
  - Storage
  - IaC
---

Shared Filesystems provide a persistent NFS-compatible volume that can be mounted across multiple pods and workloads simultaneously. Useful for sharing datasets, model weights, or outputs between compute instances and notebooks. Use the `buzzhpc_service` resource with the `shared-filesystem` profile.

---

## Resource: `buzzhpc_service`

```hcl
resource "buzzhpc_service" "shared_fs" {
  project   = "system-catalog"
  workspace = var.workspace
  name      = var.filesystem_name

  spec = jsonencode({
    serviceProfile = {
      name          = "shared-filesystem"
      systemCatalog = true
    }
    variables = [
      {
        # Volume size in GB
        name      = "block_storage_volume_size_gb"
        valueType = "json"
        value     = tostring(var.size_gb)
      }
    ]
  })
}
```

---

## Available SKUs

| SKU | Region |
|---|---|
| `shared-filesystem` | CA-QC-2 (Quebec) |

---

## Variables

| Variable | Type | Default | Description |
|---|---|---|---|
| `workspace` | string | `"test"` | Workspace within the system-catalog project |
| `filesystem_name` | string | `"my-shared-fs"` | Unique name for the shared filesystem |
| `size_gb` | number | `50` | Volume size in GB |

---

## Outputs

| Output | Description |
|---|---|
| `filesystem_id` | Terraform resource ID (`<project>/<workspace>/<name>`) |
| `filesystem_status` | Current deployment status |

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

resource "buzzhpc_service" "shared_fs" {
  project   = "system-catalog"
  workspace = "test"
  name      = "my-shared-fs"

  spec = jsonencode({
    serviceProfile = { name = "shared-filesystem", systemCatalog = true }
    variables = [
      { name = "block_storage_volume_size_gb", valueType = "json", value = "50" }
    ]
  })
}

output "filesystem_id"     { value = buzzhpc_service.shared_fs.id }
output "filesystem_status" { value = jsondecode(buzzhpc_service.shared_fs.status).status }
```

!!! info
    Set `BUZZHPC_API_KEY` in your environment before running `terraform apply`.

!!! info
    Once deployed, mount the filesystem into your compute instances or notebooks using the NFS mount path shown in the BuzzHPC console.
