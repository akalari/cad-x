# cad-x downstream guidance

This file applies to the full repository. More specific `AGENTS.md` files can add
rules for their directories, but they must not weaken these rules.

## Communication

- Use ASD-STE100 Simplified Technical English for explanations to the user.
- Keep exact identifiers, commands, paths, error text, and quotations unchanged.
- Use short sentences, one meaning per sentence, and consistent technical terms.

## Product boundary

- cad-x is a maintained fork of FreeCAD and an independent product.
- cad-x is not a FreeCAD plugin.
- cad-x is not endorsed by the FreeCAD project or the FreeCAD Project Association.
- Preserve required FreeCAD attribution. Do not imply upstream endorsement.
- The primary application implementation uses C++, Qt, and FreeCAD native APIs.
- Python is permitted where FreeCAD conventions use Python.
- `akalari/cad-x-rust` is reference and porting material. It is not a required
  cad-x runtime component.

## Downstream architecture

- Concentrate product changes in the future `src/Mod/CadX` module and in
  downstream packaging and branding surfaces.
- Minimize changes to shared FreeCAD core code.
- Document each required shared-core hook, its downstream purpose, and why a
  module-level extension point is not sufficient.
- Keep backend-neutral semantic identity in native document types.
- Do not use object order, transient tokens, or names such as `FaceN` and
  `EdgeN` as durable identity.
- Run crash-prone native operations in supervised `FreeCADCmd` worker
  processes. The supervisor must detect failure and report it clearly.

## Development and validation

- Validate native builds and behavior on Windows, macOS, and Linux.
- Do not use a container as a substitute for native platform validation.
- Keep pull requests small and reviewable.
- Separate generic FreeCAD fixes that can be sent upstream from cad-x product
  changes. Submit those fixes as separate commits and pull requests.
- Follow [the downstream synchronization and licensing policy](docs/cadx/upstream-and-licensing.md).

## Contributor and agent responsibility

- Follow FreeCAD's [AI policy](AI_POLICY.md) and contribution guidance.
- A human contributor remains responsible for each change.
- The contributor must understand, review, and test assisted work.
- Disclose AI assistance as required by the applicable repository policy.
- Do not submit unverified generated output or use an agent to replace human
  review and responsibility.
