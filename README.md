# ALXprodev-advanced_git

## Complete Git-Flow Implementation Guide

This repository demonstrates a complete Git-Flow workflow implementation with automated hooks. This guide serves as a comprehensive reference for implementing Git-Flow in any project.

## Table of Contents

1. [Git-Flow Overview](#git-flow-overview)
2. [Project Setup](#project-setup)
3. [Feature Development Workflow](#feature-development-workflow)
4. [Release Management](#release-management)
5. [Git Hooks Implementation](#git-hooks-implementation)
6. [Commands Reference](#commands-reference)
7. [Project Structure](#project-structure)

## Git-Flow Overview

Git-Flow is a branching model that defines a strict branching model designed around project releases. It provides a robust framework for managing larger projects.

### Branch Types:

- **main/master**: Production-ready code
- **develop**: Integration branch for features
- **feature/\***: New features in development
- **release/\***: Preparation for production releases
- **hotfix/\***: Critical bug fixes for production

## Project Setup

### Step 1: Repository Initialization

```bash
# Clone or create your repository
git clone https://github.com/devshad-01/ALXprodev-advanced_git.git
cd ALXprodev-advanced_git

# Create and push develop branch
git checkout -b develop
git push -u origin develop

# Initialize Git-Flow with default settings
git flow init -d
```

### Step 2: Setup Remote Repository

```bash
# Add remote origin if not already set
git remote add origin https://github.com/your-username/your-repo.git
```

## Feature Development Workflow

### Creating a Feature Branch

```bash
# Start a new feature
git flow feature start implement-login

# This creates and switches to: feature/implement-login
```

### Working on Features

```bash
# Create your feature structure
mkdir login-page
echo "Login Feature Coming soon" > login-page/README.md

# Commit your changes
git add .
git commit -m "feat: scaffolding login page"

# Push feature branch to remote
git push -u origin feature/implement-login
```

### Finishing a Feature

```bash
# Finish the feature (merges into develop)
git flow feature finish implement-login

# Push updated develop branch
git push origin develop
```

## Release Management

### Creating a Release

```bash
# Start a release branch
git flow release start 1.0.0

# Make release-specific changes
echo "data requirements: email, firstName, lastName, profilePic]" >> signup-page/README.md

# Commit release changes
git add .
git commit -m "Add data requirements to signup page"

# Push release branch
git push -u origin release/1.0.0
```

### Finishing a Release

```bash
# Finish release (merges to main and develop, creates tag)
git flow release finish 1.0.0

# Push everything including tags
git push origin main
git push origin develop
git push origin --tags
```

## Git Hooks Implementation

### Pre-commit Hook

Location: `.git/hooks/pre-commit`

**Purpose**: Ensures every directory has a README file before allowing commits.

```bash
#!/bin/bash
echo "Running pre-commit hook: Checking for README files in directories..."

# Find all directories (excluding .git and hidden directories)
directories=$(find . -type d -not -path "./.git*" -not -path "./.*" -not -name "." | sort)

missing_readme=false

for dir in $directories; do
    if [ -f "$dir/README.md" ] || [ -f "$dir/README.txt" ] || [ -f "$dir/README.rst" ] || [ -f "$dir/README" ]; then
        echo "✓ Directory '$dir' has a README file"
    else
        echo "ERROR: Directory '$dir' is missing a README file"
        missing_readme=true
    fi
done

if [ "$missing_readme" = true ]; then
    echo ""
    echo "Pre-commit hook failed: Some directories are missing README files"
    echo "Please add README files to all directories before committing"
    exit 1
fi

echo "Pre-commit hook passed: All directories have README files"
exit 0
```

### Post-merge Hook

Location: `.git/hooks/post-merge`

**Purpose**: Logs all merges into the main/master branch.

```bash
#!/bin/bash

# Check if we're on the main/master branch
current_branch=$(git branch --show-current)

if [ "$current_branch" = "main" ] || [ "$current_branch" = "master" ]; then
    # Get merge information
    merge_commit=$(git rev-parse HEAD)
    merge_date=$(date)
    merge_author=$(git log -1 --pretty=format:'%an <%ae>')
    merge_message=$(git log -1 --pretty=format:'%s')

    # Log the merge
    echo "=== MERGE INTO $current_branch ===" >> merge.log
    echo "Date: $merge_date" >> merge.log
    echo "Commit: $merge_commit" >> merge.log
    echo "Author: $merge_author" >> merge.log
    echo "Message: $merge_message" >> merge.log
    echo "----------------------------------------" >> merge.log
    echo "" >> merge.log

    echo "Post-merge hook: Logged merge into $current_branch branch"
fi
```

### Making Hooks Executable

```bash
chmod +x .git/hooks/pre-commit
chmod +x .git/hooks/post-merge
```

## Commands Reference

### Git-Flow Commands

```bash
# Initialize Git-Flow
git flow init [-d]  # -d for default settings

# Feature branches
git flow feature start <feature-name>
git flow feature finish <feature-name>
git flow feature publish <feature-name>

# Release branches
git flow release start <version>
git flow release finish <version>

# Hotfix branches
git flow hotfix start <version>
git flow hotfix finish <version>
```

### Standard Git Commands Used

```bash
# Branch management
git checkout -b <branch-name>
git branch -a
git push -u origin <branch-name>

# Committing
git add .
git commit -m "message"
git push

# Tagging
git tag <tag-name>
git push origin --tags
```

## Project Structure

```
ALXprodev-advanced_git/
├── .git/
│   └── hooks/
│       ├── pre-commit          # README validation hook
│       └── post-merge          # Merge logging hook
├── login-page/
│   └── README.md               # Login feature documentation
├── signup-page/
│   └── README.md               # Signup feature documentation
├── README.md                   # This comprehensive guide
└── merge.log                   # Merge history log
```

## Workflow Example

### Complete Feature Implementation

```bash
# 1. Start feature
git flow feature start user-authentication

# 2. Create feature structure
mkdir user-auth
echo "User Authentication System" > user-auth/README.md

# 3. Implement feature
# ... add your code ...

# 4. Commit and push
git add .
git commit -m "feat: implement user authentication system"
git push -u origin feature/user-authentication

# 5. Finish feature
git flow feature finish user-authentication
git push origin develop
```

### Complete Release Cycle

```bash
# 1. Start release
git flow release start 1.1.0

# 2. Make release preparations
# ... update version numbers, documentation, etc ...

# 3. Commit release changes
git add .
git commit -m "chore: prepare release 1.1.0"
git push -u origin release/1.1.0

# 4. Finish release
git flow release finish 1.1.0

# 5. Push everything
git push origin main
git push origin develop
git push origin --tags
```

## Best Practices Implemented

1. **Automated Quality Checks**: Pre-commit hook ensures documentation standards
2. **Merge Tracking**: Post-merge hook maintains audit trail
3. **Structured Branching**: Clear separation of feature, release, and production code
4. **Documentation**: Every directory requires README for maintainability
5. **Version Tagging**: Proper release versioning with Git tags

## Troubleshooting

### Pre-commit Hook Issues

- **Error**: "Directory missing README file"
- **Solution**: Add README.md to the directory or exclude it from version control

### Merge Conflicts

- **Prevention**: Keep feature branches small and merge frequently
- **Resolution**: Use `git status` and resolve conflicts manually

### Hook Not Running

- **Issue**: Hooks not executable
- **Solution**: `chmod +x .git/hooks/hook-name`

---

This repository demonstrates a production-ready Git-Flow implementation with automated quality assurance through Git hooks. The workflow ensures code quality, proper documentation, and maintains a clear project history suitable for collaborative development.
