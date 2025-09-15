# Pseudotools Workflow Scheme Authoring Guide

*Schema version 0.2*

This document describes how each workflow scheme JSON file is structured for the **pt-essential-workflows** repository.  
It is intended for authors creating or editing workflows so that they integrate smoothly with Pseudotools runners, user interfaces, and the new snapshot format.

---

## 1 • Overview

Every workflow scheme JSON file defines:

1. **Metadata** – name, description, optional thumbnail and rank order.
2. **Global Guidance Capabilities** – which user-defined global prompts (`txt_scene`, `txt_style`, `txt_negative`, `img_style`) the workflow accepts.
3. **Regional Guidance Capabilities** – which region-specific prompts (`txt`, `img`) the workflow accepts.
4. **Spatial Guidance Capabilities** – which model-derived full-frame maps (`depth`, `edge`, …) the workflow can use.
5. **Variables** – tunable parameters that bind to tokens inside the ComfyUI graph.
6. **Endpoint Requirements** – external models or custom nodes needed at runtime.
7. **Workflow Graph** – the complete ComfyUI node graph that generates imagery.

---

## 2 • Top-Level Fields

| Field                              | Type            | Purpose                                                                                                          |
| ----------------------------------- | --------------- | ---------------------------------------------------------------------------------------------------------------- |
| **`type`**                          | string          | Workflow engine family. For ComfyUI workflows use `"comfy"`.                                                     |
| **`name`**                          | string          | Human-readable title shown in menus and UIs.                                                                     |
| **`description`**                   | string          | One-line summary of what the workflow does or which model family it targets.                                     |
| **`schema_version`**                | number          | Schema version; set to `0.2` for this format.                                                                    |
| **`thumbnail`**                     | string (base64) | Optional preview image shown in user interfaces.                                                                 |
| **`rank_order`**                    | number          | Optional integer to control display ordering within a larger library; workflows without a value fall to the end. |
| **`global_guidance_capabilities`**  | object          | Declares which global prompts are supported (see §3).                                                            |
| **`regional_guidance_capabilities`**| object          | Declares which per-region prompts are supported (see §4).                                                        |
| **`spatial_guidance_capabilities`** | object          | Declares which model-derived full-frame guidance maps are supported (see §5).                                    |
| **`variables`**                     | array           | Tunable parameters (see §6).                                                                                     |
| **`endpoint_requirements`**         | object          | External resources required (see §7).                                                                            |
| **`workflow`**                      | object          | The ComfyUI node graph (the 'workflow') itself (see §8).                                                                          |

> **ID note:** A workflow scheme’s ID is **derived from its filename** (e.g. `sdlt-realviz50-ip.json`) and is not specified inside the JSON.

---

## 3 • Global Guidance Capabilities

These correspond to the `global_guidance` object in a snapshot.

```json
"global_guidance_capabilities": {
  "txt_scene":    "required" | "optional" | "unsupported",
  "txt_style":    "required" | "optional" | "unsupported",
  "txt_negative": "required" | "optional" | "unsupported",
  "img_style":    "required" | "optional" | "unsupported"
}
````

* **txt\_scene** – overall program, massing, composition.
* **txt\_style** – global look/lighting/lens descriptors.
* **txt\_negative** – elements to avoid globally.
* **img\_style** – image equivalent of `txt_style`, a full-frame style reference.

---

## 4 • Regional Guidance Capabilities

These correspond to the `regional_guidance` array in a snapshot.

```json
"regional_guidance_capabilities": {
  "text":  "required" | "optional" | "unsupported",
  "image": "required" | "optional" | "unsupported"
}
```

* **text** – region-specific material or object prompts.
* **image** – region-specific reference images.

Masks are implicitly required for every regional prompt and do not need to be declared.

---

## 5 • Spatial Guidance Capabilities

These correspond to the `spatial_guidance` object in a snapshot.

```json
"spatial_guidance_capabilities": {
  "depth": "required" | "optional" | "unsupported",
  "edge":  "required" | "optional" | "unsupported"
}
```

* **depth** – grayscale depth map for geometry guidance.
* **edge** – edge or linework map for contour guidance.
* *(future keys)* – additional model-derived maps such as `normal`, `albedo`, etc., may be added later following the same pattern.

Runners use this section to:

* validate that a snapshot provides the needed spatial guidance,
* drive the UI (for example, enabling depth or edge upload controls only when supported).

---

## 6 • Variables

Variables expose parameters that can be tuned at runtime and substituted directly into the ComfyUI graph.

### Structure

```json
{
  "name": "Sampling Steps",
  "key": "__STEPS__",
  "type": "int",                    // int | float | bool | string
  "short_description": "Number of denoising steps.",
  "default": 30,
  "min": 10,                        // optional
  "max": 100,                       // optional
  "step": 1,                         // optional
  "precision": 0,                    // optional, floats only
  "unit": "steps",                   // optional UI hint
  "enum_options": {                  // optional key–value pairs if enumerated
    "euler": "Euler sampler",
    "ddim": "DDIM sampler",
    "dpmpp_2m": "DPM++ 2M sampler"
  },
  "binds_to": "__STEPS__",           // single token inside the graph
  "order": 10,                       // optional display order
  "advanced": false                  // optional: UI may group under “Advanced”
}
```

Key points:

* `short_description` is concise and intended for quick UI help or AI assistance.
* `enum_options` is a map of **key → human label**; keep `type` consistent with the key type (e.g., `"string"` for sampler names).
* `binds_to` must match exactly one token inside the `workflow` graph.

---

## 7 • Endpoint Requirements

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

## 8 • Workflow Graph

The `workflow` field holds the full **ComfyUI node graph** exported from the ComfyUI interface.
Inside the graph, variables are injected by replacing special tokens:

* `__STEPS__` – bound to a variable.
* `__CFG__`, `__SAMPLER__`, etc. – other variable placeholders as needed.
* **Always available runtime tokens** (provided by the runner):

  * `__PSEUDORANDOM_TEMP_PATH__` – a temporary writable directory for intermediate files.
  * `__PSEUDORANDOM_SEED__` – a reproducible random seed value.

No other reserved tokens are supported.

---

## 9 • Authoring Checklist

1. Name the file descriptively; the **ID is taken from the filename**.
2. Provide a clear **name** and **description** inside the JSON.
3. Add an optional **thumbnail** image and/or **rank\_order** if needed for display.
4. Declare **global\_guidance\_capabilities**, **regional\_guidance\_capabilities**, and **spatial\_guidance\_capabilities** exactly.
5. Define **variables** with precise `short_description`s and correct `binds_to` tokens.
6. List all required models or nodes in **endpoint\_requirements**.
7. Export the ComfyUI graph and insert it into the `workflow` field, using only the supported variable tokens.

---

## 10 • Versioning

* Current schema: **0.2**.
* Update `schema_version` only when the schema itself changes.
* Incrementing is **not required** for normal graph or default-value tweaks.

