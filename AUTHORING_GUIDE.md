# Pseudotools Workflow Authoring Guide

*Schema version 0.2*

This document describes how each workflow JSON file is structured for the **pt-essential-workflows** repository.
It is intended for authors creating or editing workflows so that they integrate smoothly with Pseudotools runners and user interfaces.

---

## 1 • Overview

Every workflow JSON file defines:

1. **Metadata** – name, description, optional thumbnail and rank order.
2. **Prompt Capabilities** – what *environmental* and *material* prompts the workflow accepts.
3. **Variables** – tunable parameters that bind to tokens inside the ComfyUI graph.
4. **Endpoint Requirements** – external models or custom nodes needed at runtime.
5. **Workflow Graph** – the complete ComfyUI node graph that generates imagery.

---

## 2 • Top-Level Fields

| Field                                      | Type            | Purpose                                                                                                          |
| ------------------------------------------ | --------------- | ---------------------------------------------------------------------------------------------------------------- |
| **`type`**                                 | string          | Workflow engine family. For ComfyUI workflows use `"comfy"`.                                                     |
| **`name`**                                 | string          | Human-readable title shown in menus and UIs.                                                                     |
| **`description`**                          | string          | One-line summary of what the workflow does or which model family it targets.                                     |
| **`schema_version`** | number          | Schema version; set to `0.2` for this format.                                                                    |
| **`thumbnail`**                            | string (base64) | Optional preview image shown in user interfaces.                                                                 |
| **`rank_order`**                           | number          | Optional integer to control display ordering within a larger library; workflows without a value fall to the end. |
| **`environmental_prompt_capabilities`**    | object          | Declares which global prompts are supported (see §3).                                                            |
| **`material_prompt_capabilities`**         | object          | Declares which per-region prompts are supported (see §3).                                                        |
| **`variables`**                            | array           | Tunable parameters (see §4).                                                                                     |
| **`endpoint_requirements`**                | object          | External resources required (see §5).                                                                            |
| **`workflow`**                             | object          | The ComfyUI node graph itself (see §6).                                                                          |

> **ID note:** A workflow’s ID is **derived from its filename** (e.g. `sdlt-realviz50-ip.json`) and is not specified inside the JSON.

---

## 3 • Prompt Capabilities

### Environmental Prompts

Describe **global** scene controls.

```json
"environmental_prompt_capabilities": {
  "scene_text":    "required" | "optional" | "unsupported",
  "style_text":    "required" | "optional" | "unsupported",
  "negative_text": "required" | "optional" | "unsupported",
  "style_image":   "required" | "optional" | "unsupported"
}
```

* `scene_text` – overall program, massing, composition.
* `style_text` – global look/lighting/lens descriptors.
* `negative_text` – things to avoid globally.
* `style_image` – reference image (e.g., for IP-Adapter) affecting the overall style.

### Material Prompts

Describe **per-region or per-object** content.

```json
"material_prompt_capabilities": {
  "text":  "required" | "optional" | "unsupported",
  "image": "required" | "optional" | "unsupported"
}
```

* `text` – region-specific material or object prompts.
* `image` – region-specific reference images.

Masks are implicitly required for all material prompts and do not need to be declared.

---

## 4 • Variables

Variables expose parameters that can be tuned at runtime and substituted directly into the ComfyUI graph.

### Structure

```json
{
  "name": "Sampling Steps",
  "key": "__STEPS__",
  "type": "int",                    // int | float | bool | string
  "short_description": "Number of denoising steps.",
  "default": 30,
  "min": 10,                        // optional (int/float)
  "max": 100,                       // optional (int/float)
  "step": 1,                         // optional (int/float)
  "precision": 0,                    // optional, floats only
  "unit": "steps",                   // optional UI hint
  "enum_options": {                  // optional key–value pairs if enumerated
    "euler": "Euler sampler",
    "ddim": "DDIM sampler",
    "dpmpp_2m": "DPM++ 2M sampler"
  },
  "binds_to": "__STEPS__",           // single token inside the graph
  "order": 10,                       // optional: display ordering
  "advanced": false                  // optional: UI may group under “Advanced”
}
```

**Key points**

* `short_description` is concise and intended for quick UI help or AI assistance.
* `enum_options` is a map of **key → human label**; keep `type` consistent with the key type (e.g., `"string"` for sampler names).
* `binds_to` must match exactly one token inside the `workflow` graph.

---

## 5 • Endpoint Requirements

Declare which resources must be available to run the workflow:

```json
"endpoint_requirements": {
  "checkpoints": ["RealVis_5.0.safetensors"],
  "custom_nodes": ["pseudocomfy"],
  "ipadapter": ["ip-adapter-plus_sdxl.safetensors"],
  "controlnet": ["depth-model-v21"]
}
```

Runners should pre-check these requirements and report missing assets before execution.

---

## 6 • Workflow Graph

The `workflow` field holds the full **ComfyUI node graph** exported from the ComfyUI interface.
Inside the graph, variables are injected by replacing special tokens:

* `__STEPS__` (example) – bound to a variable.
* `__CFG__`, `__SAMPLER__`, etc. – other variable placeholders as needed.
* **Always available runtime tokens** (provided by the runner):

  * `__PSEUDORANDOM_TEMP_PATH__` – a temporary writable directory for intermediate files.
  * `__PSEUDORANDOM_SEED__` – a reproducible random seed value.

No other reserved tokens are supported.

---

## 7 • Authoring Checklist

1. Name the file descriptively; the **ID is taken from the filename**.
2. Provide a clear **name** and **description** inside the JSON.
3. Add an optional **thumbnail** image and/or **rank\_order** if needed for display.
4. Declare **environmental\_prompt\_capabilities** and **material\_prompt\_capabilities** exactly.
5. Define **variables** with precise `short_description`s and correct `binds_to` tokens.
6. List all required models or nodes in **endpoint\_requirements**.
7. Export the ComfyUI graph and insert it into the `workflow` field, using only the supported variable tokens.

---

## 8 • Versioning

* Current schema: **0.2**.
* Update `schema_version` only when the schema itself changes.
* Incrementing is **not required** for normal graph or default-value tweaks.

---

By following this specification, workflow authors ensure their JSON files are valid, discoverable, and ready to run inside the Pseudotools ecosystem.
