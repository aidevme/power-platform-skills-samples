# Samples

This folder collects sample projects and reusable components for Microsoft Power Platform, organized by product area. Each subfolder is a placeholder for examples specific to that area — add new samples under the matching directory, or create a new one if a sample spans multiple areas.

## Directory structure

| Folder | Description |
| --- | --- |
| [canvas-apps](canvas-apps/) | Samples for Power Apps canvas apps — screens, controls, formulas, and authoring patterns. |
| [code-apps](code-apps/) | Samples for Power Apps code apps (React/Vite-based apps with Power Platform connectors and Dataverse integration). |
| [mcp-apps](mcp-apps/) | Samples for Model Context Protocol (MCP) apps built on Power Platform. |
| [mobile-apps](mobile-apps/) | Samples for Power Apps mobile apps (Expo/React Native, targeting iOS and Android). |
| [model-apps](model-apps/) | Samples for model-driven apps, including generative pages, tables, forms, and views. |
| [power-apps-mobile-extension](power-apps-mobile-extension/) | Samples and extensions for the Power Apps mobile client. |
| [power-automate](power-automate/) | Samples for Power Automate cloud flows and desktop flows. |
| [power-pages](power-pages/) | Samples for Power Pages websites, including traditional and code (SPA) sites. |

## Adding a sample

- Place new samples in the subfolder that matches their primary product area.
- Include a `README.md` in each sample describing its purpose, prerequisites, and how to run it.
- Do not assume a shared runtime or toolchain across samples — each sample should be self-contained with its own manifest or setup instructions.
