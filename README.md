# Pseudotools Essential Workflows

This repository is the canonical example of a **workflow library** for the Pseudotools ecosystem.
It provides a curated set of **ComfyUI-based rendering workflows**, together with reusable **environmental prompts** and **material definitions**, all designed to load directly inside CAD applications such as Rhino.

> **Note:** The current repository layout will be **refactored** to a simpler, more maintainable structure described below.
> The description here represents the intended target state.

---

## Purpose

* **Central source of ready-to-use workflows**
  Each JSON file at the top level describes a complete rendering pipeline (scene setup, style and negative prompts, and tunable variables) following the Pseudotools workflow schema.

* **Reusable prompt and material collections**
  Shared environment prompts and material definitions provide consistent vocabulary and reference data across workflows.

* **Seamless CAD integration**
  CAD software can load the library directly from GitHub (public or private), present thumbnails and variables in its UI, and run the contained workflows without extra configuration.

---

## Planned Repository Structure

When the refactor is complete, the repository will adopt this **flat, predictable layout**:

```
pt-essential-workflows/
├─ LIBRARY.json                 # minimal library manifest
├─ <workflow-a>.json            # individual workflows (one per file)
├─ <workflow-b>.json
├─ global_guidance/
│  ├─ scene_text.json           # key → string scene prompts
│  ├─ style_text.json           # key → string style prompts
│  ├─ negative_text.json        # key → string negative prompts
│  └─ style_image/              # reference images; filenames are the keys
│     ├─ portra-800.jpg
│     ├─ cine-sky.png
│     └─ ...
└─ regional_guidanc/
   ├─ oak-plank.json            # each material is a single JSON file
   ├─ brushed-aluminum.json
   └─ ...
```

### LIBRARY.json

A single minimal manifest describing the library:

```json
{
  "schema_version": "0.2",
  "name": "Pseudotools Essential Workflows",
  "description": "Core workflows and prompt presets for architectural rendering."
}
```

* **schema\_version** must match the `pseudorandom_workflow_scheme_version` inside every workflow file.
* **name** and **description** provide basic metadata for UIs and loaders.
* No internal ID field is needed—IDs are derived from filenames.

### Workflows

* Each `*.json` file at the repository root is a complete workflow.
* Workflows follow the **v0.2 workflow schema** with optional `thumbnail` and `rank_order` fields.
* Only two reserved tokens are available inside each workflow’s ComfyUI graph:

  * `__PSEUDORANDOM_TEMP_PATH__`
  * `__PSEUDORANDOM_SEED__`

### Global Guidance

* `global_guidance/scene_text.json`, `global_guidance/style_text.json`, and `global_guidance/negative_text.json` are simple **key → string** maps of reusable prompt options.
* `global_guidance/style_image/` holds reference images; the **filename without extension** acts as the key.

### Regional Guidance

* Each file in `regional_guidance/` represents a single material and may contain:

  ```json
  {
    "schema_version": "0.2",       // same as library schema for now
    "name": "White Oak Plank",
    "text": "white oak planks, matte finish, tight grain",
    "image_base64": null,          // optional embedded reference image
    "rank_order": 120              // optional ordering hint
  }
  ```

---

## Development Roadmap

* **Refactor repository** to match the structure described above.
* **Update all workflows** to the latest Pseudotools workflow schema (currently `0.2`).
* **Populate environmental prompts and materials** with canonical starting sets.
* **Provide GitHub releases** so CAD applications can pin to a stable library version.

---

## License

This library is released under the **MIT License**, allowing both commercial and non-commercial use.
