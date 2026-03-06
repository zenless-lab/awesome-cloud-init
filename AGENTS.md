# Project Structure and Agent Guidelines

## Language Preference

All files in this project, including `AGENTS.md`, `README.md`, and configuration files, must prioritize the use of **English**. This ensures consistency and accessibility for all contributors and automated agents.

## Project Organization

This repository is organized as a flat collection of cloud-init configuration projects. Each project is contained within its own subdirectory in the root of the repository.

### Root Directory Structure

- **`README.md`**: The main entry point for the entire repository. It provides an overview of the "Awesome Cloud Init" collection, general usage instructions, and high-level project information.
- **`AGENTS.md`**: This file. It defines the global rules, project structure, and guidelines for AI agents and contributors working on this repository.
- **`.agents/`**: Contains specialized agent skills and reference materials used by AI assistants to provide expert guidance on cloud-init and related technologies.
- **`.cloud-init-template/`**: A reference project containing the standardized structure for all new projects.
- **`[project-name]/`**: Multiple subdirectories, each representing a standalone cloud-init configuration.

### Subdirectory Structure

Each project subdirectory should follow the standardized template found in `.cloud-init-template/`. The core components are:

- **`cloud-config.yaml`**: The core cloud-init configuration file. Use professional English comments and ensure idempotency.
- **`README.md`**: Must follow the standardized sections:
  - **Features**: A concise list of highlights.
  - **How It Works**: Breakdown of `packages` and `runcmd` logic.
  - **Prerequisites** (Optional): Hardware/OS requirements.
  - **Network & Security** (Optional): Ports and user permissions.
  - **Deployment Guide**: Quick start commands (e.g., Multipass).
  - **Verification**: Commands to confirm success.
- **`AGENTS.md` (Optional)**: Project-specific rules that override global ones.

## Rules for AI Agents

1. **Strict Adherence to Structure**: When adding new projects, always use `.cloud-init-template/` as the baseline. Ensure `cloud-config.yaml` and `README.md` are present and follow the standardized format.
2. **English First**: Ensure all generated content, comments, and documentation are in English.
3. **Local Context**: Always check for a local `AGENTS.md` within a project subdirectory before performing tasks to ensure project-specific mandates are followed.
4. **No Project Listing in Root AGENTS.md**: Do not list specific configurations here, with the exception of the `.cloud-init-template/` which serves as a procedural reference.
