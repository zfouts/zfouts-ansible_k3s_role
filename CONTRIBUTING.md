# Contributing to K3s Ansible Role

Thank you for your interest in contributing to this project! This document provides guidelines and instructions for contributing.

## Code of Conduct

By participating in this project, you agree to maintain a respectful and inclusive environment for everyone.

## How to Contribute

### Reporting Bugs

1. **Check existing issues** - Search the [issue tracker](../../issues) to see if the bug has already been reported
2. **Create a new issue** - If not found, open a new issue with:
   - Clear, descriptive title
   - Steps to reproduce the problem
   - Expected vs actual behavior
   - Ansible version (`ansible --version`)
   - Target OS and version
   - K3s version (if applicable)
   - Relevant configuration snippets (sanitized of secrets)

### Suggesting Features

1. Open a new issue with the `enhancement` label
2. Describe the feature and its use case
3. Explain why it would benefit other users

### Pull Requests

#### Setup Development Environment

```bash
# Clone your fork
git clone https://github.com/YOUR_USERNAME/ansible_k3s_role.git
cd ansible_k3s_role

# Add upstream remote
git remote add upstream https://github.com/zfouts/ansible_k3s_role.git

# Install development dependencies
pip install ansible ansible-lint yamllint

# Install required collections
ansible-galaxy collection install -r requirements.yml
```

#### Development Workflow

1. **Create a feature branch**
   ```bash
   git checkout -b feature/your-feature-name
   ```

2. **Make your changes**
   - Follow the existing code style
   - Update documentation if needed

3. **Run linters locally**
   ```bash
   # YAML lint
   yamllint .

   # Ansible lint
   ansible-lint

   # Verify playbook syntax
   ansible-playbook --syntax-check playbooks/*.yml
   ```

4. **Commit your changes**
   ```bash
   git add .
   git commit -m "feat: add support for feature X"
   ```
   
   Follow [Conventional Commits](https://www.conventionalcommits.org/):
   - `feat:` - New feature
   - `fix:` - Bug fix
   - `docs:` - Documentation changes
   - `test:` - Adding/updating tests
   - `refactor:` - Code refactoring
   - `chore:` - Maintenance tasks

5. **Push and create PR**
   ```bash
   git push origin feature/your-feature-name
   ```
   Then open a Pull Request against the `main` branch.

#### Pull Request Guidelines

- **One feature/fix per PR** - Keep changes focused
- **Update CHANGELOG** - Document your changes
- **Pass all CI checks** - Ensure linting passes
- **Respond to feedback** - Address review comments promptly

### Code Style

#### YAML Files

- Use 2 spaces for indentation
- Use `true`/`false` for booleans (not `yes`/`no`)
- Quote strings that could be interpreted as other types
- Include meaningful comments for complex logic

#### Task Files

```yaml
- name: Task names should be descriptive and start with capital letter
  ansible.builtin.module_name:
    parameter: value
  when: condition
  tags:
    - k3s
    - relevant_tag
```

#### Variables

- Use `snake_case` for variable names
- Prefix role variables with the role name (e.g., `k3s_server_`, `k3s_agent_`, `common_`)
- Document all variables in `defaults/main.yml` with comments
- Provide sensible defaults

#### Templates

- Include `{{ ansible_managed }}` comment at the top
- Use meaningful conditionals with comments
- Handle edge cases gracefully

### Testing

#### Running Tests Locally

```bash
# Test against your own infrastructure
ansible-playbook playbooks/site.yml -i your-inventory.yml --check

# Run the full deployment
ansible-playbook playbooks/site.yml -i your-inventory.yml
```

#### Writing Tests

- Test both positive and negative cases
- Verify idempotency (running twice produces no changes)

### Documentation

- Update `README.md` for new features
- Include examples for new variables
- Document any breaking changes prominently

## Release Process

Releases are automated via GitHub Actions when a tag is pushed:

```bash
git tag -a v1.0.0 -m "Release v1.0.0"
git push origin v1.0.0
```

## Getting Help

- Open a [Discussion](../../discussions) for questions
- Join the community chat (if available)
- Review existing issues and PRs for context

## Recognition

Contributors will be recognized in:
- GitHub contributors list
- Release notes for significant contributions

Thank you for contributing! 🎉
