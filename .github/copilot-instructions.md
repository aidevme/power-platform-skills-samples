# Power Platform Skills samples

## Repository scope

- This repository contains sample implementations demonstrating Power Platform Skills for architects and developers working with Dataverse, Power Apps, and Power Automate.
- The skill implementations themselves live in the companion repository linked from `README.md`: <https://github.com/aidevme/power-platform-skills>. Keep skill-definition changes there; keep runnable examples and supporting sample material here.
- There is currently no repository-wide build, test, lint, or package-management setup. Do not assume a root-level toolchain or command; inspect the specific sample being changed for its own validation workflow.

## Sample architecture

- Organize samples under `samples/` by the Power Platform artifact they demonstrate:
  - `canvas-apps/`
  - `model-apps/`
  - `mobile-apps/`
  - `code-apps/`
  - `power-pages/`
  - `power-automate/`
  - `mcp-apps/`
- Treat each category as an independent collection of examples rather than as part of one deployable application.
- Use `docs/` for shared documentation and `assets/` for shared supporting media when content applies across samples; keep sample-specific files with their sample.

## Repository-specific conventions

- For commit messages, follow [`instructions/commit-messages.instructions.md`](instructions/commit-messages.instructions.md).
- Place new examples in the category matching the Power Platform artifact instead of adding them at the repository root.
- Keep each sample self-contained. Include its prerequisites, setup or import steps, configuration requirements, and sample-specific build/test commands alongside the sample.
- Never commit tenant-specific URLs, environment IDs, connection references, credentials, exported secrets, or other organization-specific Power Platform configuration. Use clearly named placeholders for values users must supply.
- When a sample depends on a Power Platform Skill, identify the relevant skill from the companion repository and document that dependency in the sample.
- Update the root `README.md` when adding a sample so users can discover it from the repository entry point.
