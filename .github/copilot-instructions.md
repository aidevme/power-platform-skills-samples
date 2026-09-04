# Copilot instructions

## Repository scope and architecture

- This repository is a collection point for Microsoft Power Platform samples and reusable components spanning Power Apps, Power Automate, Power BI, and Dataverse.
- The repository is currently a documentation-only scaffold: the tracked root contains `README.md` and `LICENSE`, with no committed sample projects, package manifests, solution files, or automation.
- Do not assume a shared runtime or monorepo toolchain across future samples. Inspect the nearest sample-level documentation and manifests before choosing commands or dependencies.

## Build, test, and lint

- There are currently no repository-wide build, test, lint, or single-test commands.
- When working in a sample added later, run commands from that sample's directory and use its own manifest or README as the source of truth. Do not introduce root-level tooling solely to run one sample.

## Repository conventions

- Keep additions aligned with the repository's sample-focused purpose: examples should belong to Power Apps, Power Automate, Power BI, Dataverse, or shared Power Platform components.

### Commit messages

- Follow Conventional Commits using `<type>(<scope>): <subject>`.
- Use a scope when it identifies the affected sample or subsystem.
- Use these commit types:
  - `feat` - New features
  - `fix` - Bug fixes
  - `docs` - Documentation changes
  - `style` - Code style changes such as formatting
  - `refactor` - Code restructuring without functionality changes
  - `test` - Test additions or updates
  - `chore` - Maintenance, dependency, or build changes
  - `perf` - Performance improvements
  - `ci` - CI/CD changes
- Examples:
  - `feat: add connection export functionality`
  - `feat(tools): add tool verification system`
  - `fix: resolve tool loading error on startup`
  - `fix(connections): handle invalid connection URLs`
  - `docs: update README with new API examples`
  - `docs(api): add missing type definitions`
  - `style: format code according to ESLint rules`
  - `refactor: restructure tool manager for better maintainability`
  - `refactor(auth): simplify authentication flow`
  - `test: add unit tests for settings manager`
  - `chore: update dependencies to latest versions`
  - `chore(build): update TypeScript configuration`
- Keep `.github/instructions/commit-messages.instructions.md` synchronized with this section; `.vscode/settings.json` references that file for Copilot commit generation.
