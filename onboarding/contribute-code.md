---
title: Contributing Code to ICN
description: Learn how to contribute code to the InterCooperative Network project.
---

# Contributing Code to ICN

Thank you for your interest in contributing code to the InterCooperative Network! Your development efforts are crucial for building and enhancing the platform.

This guide provides a basic workflow for code contributions. For more detailed instructions on setting up your development environment, please see `setup-devnet.md` and the `CONTRIBUTING.md` file in the respective repository you are working on (e.g., `icn-core`, `icn-wallet`).

## Finding Issues to Work On

*   **GitHub Issues**: Check the "Issues" tab in the relevant ICN repository (e.g., `icn-core`, `icn-agoranet`). Look for issues tagged with `good first issue`, `help wanted`, or bugs that you might be able to address.
*   **Community Discussions**: Participate in the [InterCooperative Network GitHub Discussions](https://github.com/orgs/InterCooperative-Network/discussions) to understand current needs and ongoing development efforts.
*   **RFCs**: Review approved or in-progress RFCs in the `rfcs/` directory of `icn-docs`. Implementing features based on RFCs is a valuable contribution.

Before starting significant work, it's often a good idea to comment on the issue or create a new one to discuss your intended approach, especially for larger features or changes.

## Development Workflow

1.  **Fork & Clone**: Fork the target repository (e.g., `icn-core`) to your GitHub account and clone it locally.
    ```bash
    git clone https://github.com/YOUR-USERNAME/repository-name.git
    cd repository-name
    ```

2.  **Set Upstream**: Configure the original ICN repository as the `upstream` remote to keep your fork updated.
    ```bash
    git remote add upstream https://github.com/InterCooperative-Network/repository-name.git
    ```

3.  **Create a Branch**: Create a new feature branch for your work.
    ```bash
    git checkout -b feature/your-descriptive-branch-name
    ```

4.  **Develop & Code**: Make your changes. Adhere to the coding style and conventions of the project. Write clear, maintainable code.

5.  **Test Thoroughly**:
    *   **Unit Tests**: Write unit tests for new functionality and ensure existing tests pass. Most ICN Rust crates will use `cargo test`.
    *   **Integration Tests**: If applicable, run or add integration tests to verify interactions between components.
    *   **Devnet Testing**: Test your changes in a local `icn-devnet` environment to see them in action.

6.  **Commit Your Changes**: Make logical, atomic commits with clear messages. We generally follow the [Conventional Commits](https://www.conventionalcommits.org/) specification (e.g., `feat: ...`, `fix: ...`, `docs: ...`, `chore: ...`).
    ```bash
    git add .
    git commit -m "feat: Implement mana regeneration adjustment based on reputation"
    ```

7.  **Keep Your Branch Updated**: Regularly rebase your branch on the latest `main` (or `develop`) branch of the `upstream` repository to incorporate recent changes and simplify merging.
    ```bash
    git fetch upstream
    git rebase upstream/main
    ```
    Resolve any merge conflicts that arise.

8.  **Push Your Changes**: Push your feature branch to your fork on GitHub.
    ```bash
    git push origin feature/your-descriptive-branch-name
    ```

## Submitting a Pull Request (PR)

1.  **Open a PR**: From your fork on GitHub, open a Pull Request to the `main` branch (or relevant development branch) of the original ICN repository.
2.  **Describe Your PR**: Write a clear and detailed description of your changes. Explain the "what" and "why" of your contribution. Link to any relevant issues (e.g., `Closes #123`).
3.  **Code Review**: Project maintainers and other contributors will review your PR. Be responsive to feedback and be prepared to make further changes.
4.  **CI Checks**: Ensure all automated checks (linting, tests, builds) pass.
5.  **Merge**: Once approved and all checks pass, a maintainer will merge your PR.

## RFC Process for Significant Changes

For substantial new features, architectural changes, or modifications to core protocols, an RFC (Request for Comments) might be required *before* extensive development begins. Check the `rfcs/` directory and the governance guidelines.

Thank you for contributing to the development of ICN! 