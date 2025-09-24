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


Global guidance is now defined **outside individual workflow schemes** in a single consolidated prompt library.
This supports consistent scene, style, and negative prompts across all workflows and makes it easier to maintain and extend prompt sets.

#### Files & Folders

* **`global_guidance/prompt_library.json`**
  The main prompt library file. It contains:

  * **`schema_version`** – Version of this prompt library schema (e.g. `"0.2"`).
  * **`defaults`** – Fallback selections for each category.

    * Each key (`scene`, `style`, `negative`, `style_image`) must match an item in the corresponding array (or filename for `style_image`).
    * If a key is missing or does not match, the **first element** of the array or directory is used instead.
  * **`scene`** – Array of objects with `name` and `prompt` defining scene prompt options.
  * **`style`** – Array of objects with `name` and `prompt` defining style prompt options.
  * **`negative`** – Array of objects with `name` and `prompt` defining negative prompt options.

* **`global_guidance/style_image/`**
  Folder of reference images used for style conditioning.

  * The **filename without extension** serves as the key (e.g. `solarpunk.jpg` → `"solarpunk"`).
  * The default style image is set in the `defaults.style_image` field.

---

#### Sample Prompt Library File

```json
{
  "schema_version": "0.2",
  "defaults": {
    "scene": "third",
    "style": "quattro",
    "negative": "sure",
    "style_image": "solarpunk.jpg"
  },
  "scene": [
    {"name": "Goto", "prompt": "modern architectural visualization with clean lines and geometric forms"},
    {"name": "Farm", "prompt": "a photo of a farm with a barn, silo, and fields"},
    {"name": "City", "prompt": "a photo of a city skyline with tall buildings and busy streets"},
    {"name": "Beach", "prompt": "a photo of a beach with sand, ocean, and palm trees"},
    {"name": "First And Longest", "prompt": "a photo of a wild kid's birthday party with balloons, cake, and presents"},
    {"name": "Second", "prompt": "zoo animals gone wild, running amok"},
    {"name": "Third", "prompt": "objects in liminal space"},
    {"name": "Fourth", "prompt": "a surreal dreamscape with floating islands and impossible architecture"},
    {"name": "Fifth", "prompt": "a futuristic cityscape at sunset with flying cars and neon lights"},
    {"name": "Sixth", "prompt": "a cozy cabin in the woods during a snowstorm"},
    {"name": "Seventh", "prompt": "a bustling marketplace in a fantasy world with colorful stalls and exotic goods"},
    {"name": "Eighth", "prompt": "a serene beach at sunrise with gentle waves and palm trees"},
    {"name": "Ninth", "prompt": "a majestic mountain range with a crystal-clear lake in the foreground"},
    {"name": "Tenth", "prompt": "a vibrant coral reef teeming with marine life"},
    {"name": "Eleventh", "prompt": "a magical forest with glowing plants and mythical creatures"},
    {"name": "Twelfth", "prompt": "a post-apocalyptic wasteland with abandoned buildings and overgrown vegetation"}
  ],
  "style": [
    {"name": "Uno", "prompt": "cotton candy colors, whimsical and playful"},
    {"name": "Due", "prompt": "french baroque style with ornate details and luxurious textures"},
    {"name": "Twa", "prompt": "lush hobbitseque greenery with rustic wooden elements"},
    {"name": "Quattro", "prompt": "kodachrome film still, 1976"}
  ],
  "negative": [
    {"name": "Sure", "prompt": "poor, low res, just nasty"},
    {"name": "One", "prompt": "low res, poorly drawn, deformed, blurry"},
    {"name": "Two", "prompt": "unrealistic, cartoonish, low res, poorly drawn, deformed, blurry"}
  ]
}
```


#### Authoring Guidelines

* Each `name` is the **display key** for UI selection and must be unique within its category.
* Prompts should be concise but descriptive, as they are directly injected into text-to-image models.
* When adding a new style image, place it in `style_image/` and reference the filename (without extension) in `defaults.style_image` if it should be the fallback.




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
