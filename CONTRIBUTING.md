# Contributing to Incus WSL Learning Laboratory

Thank you for considering contributing to this educational project! This guide helps students learn Linux and FreeBSD using Incus inside WSL2 on Windows.

## How to Contribute

### 1. Report Issues or Suggestions
- Open an issue on the GitHub repository
- Describe what you were trying to do
- Include your environment (Windows version, WSL version, Debian version, Incus version)
- Suggest improvements to make the guide clearer

### 2. Submit Corrections
- Fork the repository
- Make changes to improve accuracy or clarity
- Ensure all commands are tested
- Update version numbers if needed

### 3. Add New Labs or Projects
- Follow the lab structure in the repository
- Include: Objective, Prerequisites, Steps, Commands, Expected Result, Questions, Challenge, Cleanup
- Ensure commands are labeled by execution environment:
  - `[Windows PowerShell]`
  - `[WSL Debian]`
  - `[Incus Debian container]`
  - `[FreeBSD]` (if applicable)
- Test all commands before submitting

### 4. Update Documentation
- Keep version awareness current
- Update test sections when versions change
- Ensure Mermaid diagrams render correctly
- Check that all commands match the expected output

## Development Guidelines

### Code Style
- All commands must be inside fenced code blocks
- Use the execution environment labels:
  ```text
  [Windows PowerShell]
  wsl --install
  ```
  ```text
  [WSL Debian]
  incus launch images:debian/13 debian01
  ```
  ```text
  [Incus Debian container]
  apt update
  ```

- Include warnings for destructive commands
- Use Mermaid diagrams for roadmaps and architecture
- Keep definitions short in the glossary

### Testing
- Verify commands against current versions
- Update "Tested with" sections
- Check that snapshot/restore workflows work
- Ensure all file paths are correct

### Pull Request Process
1. Fork the repository
2. Create a feature branch (`git checkout -b feature/new-lab`)
3. Make your changes
4. Test all commands in a real environment
5. Commit your changes (`git commit -m "Add new lab: ..."`)
6. Push to the branch (`git push origin feature/new-lab`)
7. Open a Pull Request

## Version Awareness

This documentation is version-aware. When updating:

1. Check `incus --version`, `wsl --version`, `uname -r`, `lsb_release -a`
2. Update the "Tested with" sections
3. Note any version-specific behavior
4. Do not claim something was tested if it was not

## Code of Conduct

- Be respectful and inclusive
- Remember this is for students learning
- Focus on clarity and accuracy
- Help others learn, not just show off knowledge

## Questions?

- Review the [Issues](https://github.com/hardyweb/Incus-WSL-Learning-Laboratory/issues) before submitting

Thank you for helping improve this learning laboratory!
