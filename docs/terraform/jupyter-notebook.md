---
title: Terraform – Jupyter Notebooks
description: Provision BuzzHPC Jupyter Notebook services using Terraform
tags:
  - Terraform
  - Jupyter
  - Notebook
  - IaC
---

Jupyter Notebooks are web-accessible notebook environments running on bare-metal GPU nodes. BuzzHPC manages scheduling, persistent storage, and HTTPS access automatically. Use the `buzzhpc_service` resource with a `jupyter-notebook-v4` profile.

---

## Resource: `buzzhpc_service`

```hcl
resource "buzzhpc_service" "notebook" {
  project   = "system-catalog"
  workspace = var.workspace
  name      = var.notebook_name

  spec = jsonencode({
    serviceProfile = {
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
      },
      {
        name      = "Pod Image"
        valueType = "text"
        value     = var.notebook_image
      }
    ]
  })
}
```

---

## Available SKUs

| SKU | Region | GPU Options |
|---|---|---|
| `jupyter-notebook-v4-ca-qc-2` | CA-QC-2 (Quebec) | H200 |
| `jupyter-notebook-v4` | CA-QC-1 (Quebec) | A40, H100 |

---

## Variables

| Variable | Type | Default | Description |
|---|---|---|---|
| `workspace` | string | `"test"` | Workspace within the system-catalog project |
| `notebook_name` | string | `"my-notebook"` | Unique name — also determines the public URL subdomain |
| `sku` | string | `"jupyter-notebook-v4-ca-qc-2"` | Use `jupyter-notebook-v4-ca-qc-2` for CA-QC-2 or `jupyter-notebook-v4` for CA-QC-1 |
| `node_type` | string | `"H200"` | `"H200"` for CA-QC-2; `"A40"` or `"H100"` for CA-QC-1 |
| `gpu_count` | number | `1` | Number of GPUs to allocate |
| `notebook_image` | string | `"jupyter/minimal-notebook:latest"` | Jupyter-compatible container image |

---

## Outputs

| Output | Description |
|---|---|
| `notebook_id` | Terraform resource ID (`<project>/<workspace>/<name>`) |
| `notebook_status` | Current deployment status |
| `notebook_url` | Public HTTPS URL once deployed |

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

resource "buzzhpc_service" "notebook" {
  project   = "system-catalog"
  workspace = "test"
  name      = "my-notebook"

  spec = jsonencode({
    serviceProfile = { name = "jupyter-notebook-v4-ca-qc-2", systemCatalog = true }
    variables = [
      { name = "GPU Count", valueType = "text", value = "1"  },
      { name = "Node Type", valueType = "text", value = "H200" },
      { name = "Pod Image", valueType = "text", value = "jupyter/minimal-notebook:latest" }
    ]
  })
}

output "notebook_id"     { value = buzzhpc_service.notebook.id }
output "notebook_status" { value = jsondecode(buzzhpc_service.notebook.status).status }
output "notebook_url"    { value = "https://my-notebook.notebook.buzzperformancecloud.com" }
```

!!! info
    Set `BUZZHPC_API_KEY` in your environment before running `terraform apply`.

!!! info
    The notebook starts in `not_deployed` status and is brought online by BuzzHPC automatically. Access it at `https://<name>.notebook.buzzperformancecloud.com` once the status reaches `deployed`.
