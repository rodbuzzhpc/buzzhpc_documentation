---
title: Terraform – LLM Inference
description: Deploy vLLM inference endpoints on BuzzHPC GPU infrastructure using Terraform
tags:
  - Terraform
  - LLM
  - Inference
  - vLLM
  - IaC
---

vLLM Inference services run OpenAI-compatible inference endpoints on bare-metal GPU nodes. Any public or gated HuggingFace model can be served, including Llama, Mistral, Qwen, and others. Use the `buzzhpc_service` resource with an `inference-vllm-v1` profile.

---

## Resource: `buzzhpc_service`

```hcl
resource "buzzhpc_service" "llm_inference" {
  project   = "system-catalog"
  workspace = var.workspace
  name      = var.service_name

  spec = jsonencode({
    serviceProfile = {
      name          = var.sku
      systemCatalog = true
    }
    variables = [
      {
        # HuggingFace model ID to serve
        name      = "KeyX"
        valueType = "text"
        value     = var.model_id
      },
      {
        # HuggingFace API token — required for gated/private models
        name      = "KeyAlpha"
        valueType = "text"
        value     = var.huggingface_token
      },
      {
        # Extra vLLM CLI arguments (e.g. "--max-model-len 8192 --dtype bfloat16")
        name      = "KeyY"
        valueType = "text"
        value     = var.vllm_extra_args
      },
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
| `inference-vllm-v1` | CA-QC-2 (Quebec) | H200 |
| `inference-vllm-v1-h100` | CA-QC-1 (Quebec) | A40, H100 |

---

## Variables

| Variable | Type | Default | Description |
|---|---|---|---|
| `workspace` | string | `"test"` | Workspace within the system-catalog project |
| `service_name` | string | `"my-llm-inference"` | Unique name for the inference service |
| `sku` | string | `"inference-vllm-v1"` | Use `inference-vllm-v1` for CA-QC-2 or `inference-vllm-v1-h100` for CA-QC-1 |
| `node_type` | string | `"H200"` | `"H200"` for CA-QC-2; `"A40"` or `"H100"` for CA-QC-1 |
| `model_id` | string | `"facebook/opt-125m"` | HuggingFace model ID (e.g. `meta-llama/Llama-3.1-8B-Instruct`) |
| `gpu_count` | number | `1` | Number of GPUs — use >1 for tensor parallelism with large models |
| `huggingface_token` | string | `""` | HuggingFace API token for gated/private models. Leave empty for public models. |
| `vllm_extra_args` | string | `""` | Extra vLLM CLI arguments (e.g. `--max-model-len 8192 --dtype bfloat16`) |

---

## Outputs

| Output | Description |
|---|---|
| `inference_id` | Terraform resource ID (`<project>/<workspace>/<name>`) |
| `inference_status` | Current deployment status |

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

resource "buzzhpc_service" "llm_inference" {
  project   = "system-catalog"
  workspace = "test"
  name      = "my-llm-inference"

  spec = jsonencode({
    serviceProfile = { name = "inference-vllm-v1", systemCatalog = true }
    variables = [
      { name = "KeyX",       valueType = "text", value = "meta-llama/Llama-3.1-8B-Instruct" },
      { name = "KeyAlpha",   valueType = "text", value = "" },
      { name = "KeyY",       valueType = "text", value = "" },
      { name = "GPU Count",  valueType = "text", value = "1" },
      { name = "Node Type",  valueType = "text", value = "H200" }
    ]
  })
}

output "inference_id"     { value = buzzhpc_service.llm_inference.id }
output "inference_status" { value = jsondecode(buzzhpc_service.llm_inference.status).status }
```

!!! info
    Set `BUZZHPC_API_KEY` in your environment before running `terraform apply`.

!!! info
    For private or gated HuggingFace models, provide your HuggingFace token in `KeyAlpha`. For public models (e.g. `facebook/opt-125m`), leave it empty.
