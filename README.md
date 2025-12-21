# Pseudotools Essential Workflows

This repository is the canonical example of a **workflow library** for the Pseudotools ecosystem.
It provides a curated set of **ComfyUI-based rendering workflows**, together with reusable **environmental prompts** and **material definitions**, all designed to load directly inside CAD applications such as Rhino.

> **Note:** The current repository layout will be **refactored** to a simpler, more maintainable structure described below.
> The description here represents the intended target state.

---


# Henriette

## Henriette
todo
## Hugo
todo
## Lisette
todo
## Lumen
todo
## Unit Test
todo



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






