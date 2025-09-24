# Version Control with Git

## Introduction
Version control is essential for collaborative development. This lesson covers Git fundamentals, branching strategies, and advanced Git techniques for frontend development.

## Git Fundamentals

### Basic Git Commands
```bash
# Initialize a new repository
git init

# Clone an existing repository
git clone https://github.com/username/repository.git

# Check repository status
git status

# Add files to staging area
git add .
git add filename.js
git add src/

# Commit changes
git commit -m "Add new feature"
git commit -am "Quick commit with message"

# View commit history
git log
git log --oneline
git log --graph --oneline --all

# View changes
git diff
git diff --staged
git diff HEAD~1
```

### Git Configuration
```bash
# Set user information
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# Set default branch name
git config --global init.defaultBranch main

# Set default editor
git config --global core.editor "code --wait"

# Set default merge tool
git config --global merge.tool vscode

# View configuration
git config --list
git config --global --list
```

## Branching Strategies

### Git Flow
```bash
# Create feature branch
git checkout -b feature/new-feature
git checkout -b feature/user-authentication

# Create release branch
git checkout -b release/1.0.0

# Create hotfix branch
git checkout -b hotfix/critical-bug

# Merge feature branch
git checkout main
git merge feature/new-feature
git branch -d feature/new-feature

# Merge release branch
git checkout main
git merge release/1.0.0
git tag -a v1.0.0 -m "Release version 1.0.0"
git checkout develop
git merge release/1.0.0
git branch -d release/1.0.0
```

### GitHub Flow
```bash
# Create feature branch from main
git checkout main
git pull origin main
git checkout -b feature/new-feature

# Work on feature
git add .
git commit -m "Implement new feature"

# Push feature branch
git push origin feature/new-feature

# Create pull request (via GitHub UI)
# After PR approval, merge and delete branch
git checkout main
git pull origin main
git branch -d feature/new-feature
```

### GitLab Flow
```bash
# Create feature branch
git checkout -b feature/new-feature

# Work on feature
git add .
git commit -m "Implement new feature"

# Push feature branch
git push origin feature/new-feature

# Create merge request (via GitLab UI)
# After MR approval, merge and delete branch
git checkout main
git pull origin main
git branch -d feature/new-feature
```

## Advanced Git Techniques

### Stashing
```bash
# Stash current changes
git stash
git stash push -m "Work in progress"

# List stashes
git stash list

# Apply stash
git stash apply
git stash apply stash@{0}

# Pop stash
git stash pop

# Drop stash
git stash drop stash@{0}

# Clear all stashes
git stash clear
```

### Rebasing
```bash
# Interactive rebase
git rebase -i HEAD~3

# Rebase feature branch onto main
git checkout feature-branch
git rebase main

# Continue rebase after resolving conflicts
git rebase --continue

# Abort rebase
git rebase --abort

# Squash commits
git rebase -i HEAD~3
# In editor: change 'pick' to 'squash' for commits to squash
```

### Cherry Picking
```bash
# Cherry pick specific commit
git cherry-pick commit-hash

# Cherry pick multiple commits
git cherry-pick commit1 commit2 commit3

# Cherry pick range
git cherry-pick start-commit..end-commit
```

## Git Hooks

### Pre-commit Hook
```bash
#!/bin/sh
# .git/hooks/pre-commit

# Run linter
npm run lint
if [ $? -ne 0 ]; then
    echo "Linting failed. Please fix errors before committing."
    exit 1
fi

# Run tests
npm test
if [ $? -ne 0 ]; then
    echo "Tests failed. Please fix tests before committing."
    exit 1
fi

# Check for console.log statements
if git diff --cached --name-only | xargs grep -l "console\.log"; then
    echo "Warning: console.log statements found in staged files."
    echo "Consider removing them before committing."
fi
```

### Commit Message Hook
```bash
#!/bin/sh
# .git/hooks/commit-msg

# Check commit message format
commit_regex='^(feat|fix|docs|style|refactor|test|chore)(\(.+\))?: .{1,50}'

if ! grep -qE "$commit_regex" "$1"; then
    echo "Invalid commit message format!"
    echo "Format: type(scope): description"
    echo "Types: feat, fix, docs, style, refactor, test, chore"
    exit 1
fi
```

## Git Workflow for Frontend Development

### Feature Development
```bash
# Start new feature
git checkout main
git pull origin main
git checkout -b feature/user-dashboard

# Work on feature
git add src/components/Dashboard.js
git commit -m "feat(dashboard): add user dashboard component"

git add src/styles/dashboard.css
git commit -m "style(dashboard): add dashboard styles"

# Push feature branch
git push origin feature/user-dashboard

# Create pull request
# After review and approval, merge via GitHub UI
```

### Bug Fixes
```bash
# Start bug fix
git checkout main
git pull origin main
git checkout -b fix/login-validation

# Fix bug
git add src/utils/validation.js
git commit -m "fix(validation): fix login form validation"

# Push fix branch
git push origin fix/login-validation

# Create pull request
# After review and approval, merge via GitHub UI
```

### Hotfixes
```bash
# Start hotfix
git checkout main
git checkout -b hotfix/critical-security-issue

# Fix critical issue
git add src/utils/security.js
git commit -m "fix(security): patch critical security vulnerability"

# Push hotfix branch
git push origin hotfix/critical-security-issue

# Create pull request
# After review and approval, merge via GitHub UI
# Tag release
git tag -a v1.0.1 -m "Hotfix release v1.0.1"
git push origin v1.0.1
```

## Git Best Practices

### Commit Messages
```bash
# Good commit messages
git commit -m "feat(auth): add user authentication system"
git commit -m "fix(ui): resolve button alignment issue"
git commit -m "docs(readme): update installation instructions"
git commit -m "style(css): format code according to style guide"
git commit -m "refactor(components): extract reusable button component"
git commit -m "test(utils): add unit tests for validation functions"
git commit -m "chore(deps): update dependencies to latest versions"

# Bad commit messages
git commit -m "fix"
git commit -m "update"
git commit -m "changes"
git commit -m "WIP"
```

### Branch Naming
```bash
# Good branch names
feature/user-authentication
feature/payment-integration
fix/login-validation
fix/responsive-design
hotfix/security-patch
hotfix/critical-bug
docs/api-documentation
docs/readme-update
chore/dependency-update
chore/build-configuration

# Bad branch names
new-feature
fix
update
changes
work
temp
```

### Gitignore
```gitignore
# Dependencies
node_modules/
npm-debug.log*
yarn-debug.log*
yarn-error.log*

# Build outputs
dist/
build/
out/

# Environment variables
.env
.env.local
.env.development.local
.env.test.local
.env.production.local

# IDE files
.vscode/
.idea/
*.swp
*.swo
*~

# OS files
.DS_Store
Thumbs.db

# Logs
logs/
*.log

# Runtime data
pids/
*.pid
*.seed
*.pid.lock

# Coverage directory used by tools like istanbul
coverage/
.nyc_output/

# Dependency directories
jspm_packages/

# Optional npm cache directory
.npm

# Optional eslint cache
.eslintcache

# Microbundle cache
.rpt2_cache/
.rts2_cache_cjs/
.rts2_cache_es/
.rts2_cache_umd/

# Optional REPL history
.node_repl_history

# Output of 'npm pack'
*.tgz

# Yarn Integrity file
.yarn-integrity

# parcel-bundler cache (https://parceljs.org/)
.cache
.parcel-cache

# Next.js build output
.next

# Nuxt.js build / generate output
.nuxt

# Gatsby files
.cache/
public

# Storybook build outputs
.out
.storybook-out

# Temporary folders
tmp/
temp/
```

## Exercise: Git Workflow

Practice Git workflow with:
- Repository initialization
- Branch creation and management
- Commit best practices
- Merge strategies
- Conflict resolution
- Stashing and rebasing
- Git hooks setup
- Pull request workflow

## Key Takeaways
- Use Git for version control
- Follow branching strategies
- Write clear commit messages
- Use proper branch naming
- Set up Git hooks
- Practice merge strategies
- Handle conflicts effectively
- Use stashing and rebasing
- Follow team workflows
- Use Gitignore effectively
- Tag releases properly
- Use pull requests for code review
- Practice hotfix workflows
- Maintain clean commit history
- Use Git aliases for efficiency
