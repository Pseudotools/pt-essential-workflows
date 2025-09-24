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
│  ├─ prompt_library.json       # consolidated prompt definitions
│  └─ style_image/              # reference images; filenames are the keys
│     ├─ portra-800.jpg
│     ├─ cine-sky.png
│     └─ ...
└─ regional_guidance/
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
* Only two reserved tokens are available inside each workflow's ComfyUI graph:

  * `__PSEUDORANDOM_TEMP_PATH__`
  * `__PSEUDORANDOM_SEED__`

### Global Guidance

* `global_guidance/prompt_library.json` is a **consolidated prompt library** containing:
  * **defaults**: Keys that represent fallback values for scene, style, and negative prompts. Refers to Keys in the scene, style, and negative ditcs respectively. If this dict is missing (or any keys are missing or do not match dicts below), the first value found in each catetory is used.
  * **scene**: Key → string mappings for scene prompt options
  * **style**: Key → string mappings for style prompt options  
  * **negative**: Key → string mappings for negative prompt options
* `global_guidance/style_image/` holds reference images; the **filename without extension** acts as the key.


#### Sample Prompt Library File
```
{
  "schema_version": "0.2",
  "defaults": {
    "scene": "architectural",
    "style": "photorealistic",
    "negative": "composition"
  },
  "scene": {
    "architectural": "modern architectural visualization with clean lines and geometric forms",
    "interior": "interior architectural photography with natural lighting and clean composition",
    "exterior": "exterior building photography with dramatic lighting and urban context",
    "detail": "architectural detail photography highlighting materials and craftsmanship"
  },
  "style": {
    "photorealistic": "photorealistic architectural rendering, high detail, professional photography",
    "artistic": "artistic architectural visualization with creative lighting and composition",
    "technical": "technical architectural drawing style with precise lines and annotations",
    "sketch": "architectural sketch style with hand-drawn aesthetic and loose lines"
  },
  "negative": {
    "quality": "blurry, low quality, distorted, low detail, bad lighting, jpeg artifacts",
    "style": "cartoon, sketch, painting, illustration, non-photorealistic",
    "composition": "poor composition, awkward framing, cluttered, messy",
    "technical": "rendering artifacts, compression artifacts, noise, grain"
  }
}


```


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

---

## Benefits of the New Structure

1. **Single Source of Truth**: All prompt definitions in one file
2. **Clear Defaults**: Explicit default values for each prompt type
3. **Organized Categories**: Logical grouping of prompt options
4. **Easier Maintenance**: One file to update instead of three
5. **Better Versioning**: Single file to track changes to prompt library
6. **Simpler Loading**: Library manager only needs to parse one JSON file

This structure will make it much easier to manage prompt libraries and provide a cleaner API for the LibraryManager to consume.
