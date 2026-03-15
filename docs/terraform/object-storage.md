---
title: Terraform – Object Storage
description: Provision S3-compatible Object Storage buckets on BuzzHPC using Terraform
tags:
  - Terraform
  - Object Storage
  - S3
  - VAST
  - IaC
---

Object Storage provides S3-compatible buckets backed by VAST Data storage. Access credentials and the S3 endpoint are available in the BuzzHPC console after the service is deployed. Use the `buzzhpc_service` resource with an `object-storage-vast` profile.

---

## Resource: `buzzhpc_service`

```hcl
resource "buzzhpc_service" "object_storage" {
  project   = "system-catalog"
  workspace = var.workspace
  name      = var.bucket_name

  spec = jsonencode({
    serviceProfile = {
      name          = var.sku
      systemCatalog = true
    }
    variables = [
      {
        # Maximum storage quota in GB
        name      = "quota_max_size_gb"
        valueType = "json"
        value     = tostring(var.quota_gb)
      }
    ]
  })
}
```

---

## Available SKUs

| SKU | Region |
|---|---|
| `object-storage-vast-ca-qc-2` | CA-QC-2 (Quebec) |
| `object-storage-vast` | CA-QC-1 (Quebec) |

---

## Variables

| Variable | Type | Default | Description |
|---|---|---|---|
| `workspace` | string | `"test"` | Workspace within the system-catalog project |
| `bucket_name` | string | `"my-object-storage"` | Unique name for the bucket |
| `sku` | string | `"object-storage-vast-ca-qc-2"` | Use `object-storage-vast-ca-qc-2` for CA-QC-2 or `object-storage-vast` for CA-QC-1 |
| `quota_gb` | number | `10` | Maximum storage quota in GB |

---

## Outputs

| Output | Description |
|---|---|
| `storage_id` | Terraform resource ID (`<project>/<workspace>/<name>`) |
| `storage_status` | Current deployment status |

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

resource "buzzhpc_service" "object_storage" {
  project   = "system-catalog"
  workspace = "test"
  name      = "my-object-storage"

  spec = jsonencode({
    serviceProfile = { name = "object-storage-vast-ca-qc-2", systemCatalog = true }
    variables = [
      { name = "quota_max_size_gb", valueType = "json", value = "10" }
    ]
  })
}

output "storage_id"     { value = buzzhpc_service.object_storage.id }
output "storage_status" { value = jsondecode(buzzhpc_service.object_storage.status).status }
```

!!! info
    Set `BUZZHPC_API_KEY` in your environment before running `terraform apply`.

!!! info
    After deployment, retrieve your S3 endpoint and access credentials from the BuzzHPC console.
