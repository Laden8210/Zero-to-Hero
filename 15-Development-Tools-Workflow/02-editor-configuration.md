# Editor Configuration and Setup

## Introduction
Proper editor configuration is crucial for productive frontend development. This lesson covers VS Code setup, extensions, settings, and workflow optimization.

## VS Code Configuration

### Basic Settings
```json
// .vscode/settings.json
{
    "editor.tabSize": 2,
    "editor.insertSpaces": true,
    "editor.detectIndentation": false,
    "editor.formatOnSave": true,
    "editor.formatOnPaste": true,
    "editor.codeActionsOnSave": {
        "source.fixAll.eslint": true,
        "source.organizeImports": true
    },
    "files.trimTrailingWhitespace": true,
    "files.insertFinalNewline": true,
    "files.trimFinalNewlines": true,
    "emmet.includeLanguages": {
        "javascript": "javascriptreact",
        "typescript": "typescriptreact"
    },
    "emmet.triggerExpansionOnTab": true,
    "typescript.preferences.importModuleSpecifier": "relative",
    "javascript.preferences.importModuleSpecifier": "relative"
}
```

### Advanced Settings
```json
// .vscode/settings.json
{
    "editor.tabSize": 2,
    "editor.insertSpaces": true,
    "editor.detectIndentation": false,
    "editor.formatOnSave": true,
    "editor.formatOnPaste": true,
    "editor.codeActionsOnSave": {
        "source.fixAll.eslint": true,
        "source.organizeImports": true,
        "source.fixAll.prettier": true
    },
    "files.trimTrailingWhitespace": true,
    "files.insertFinalNewline": true,
    "files.trimFinalNewlines": true,
    "files.exclude": {
        "**/node_modules": true,
        "**/dist": true,
        "**/build": true,
        "**/.git": true,
        "**/.DS_Store": true,
        "**/Thumbs.db": true
    },
    "search.exclude": {
        "**/node_modules": true,
        "**/dist": true,
        "**/build": true,
        "**/.git": true
    },
    "emmet.includeLanguages": {
        "javascript": "javascriptreact",
        "typescript": "typescriptreact"
    },
    "emmet.triggerExpansionOnTab": true,
    "typescript.preferences.importModuleSpecifier": "relative",
    "javascript.preferences.importModuleSpecifier": "relative",
    "typescript.suggest.autoImports": true,
    "javascript.suggest.autoImports": true,
    "typescript.updateImportsOnFileMove.enabled": "always",
    "javascript.updateImportsOnFileMove.enabled": "always",
    "css.validate": true,
    "scss.validate": true,
    "less.validate": true,
    "html.validate.styles": true,
    "html.validate.scripts": true,
    "json.schemas": [
        {
            "fileMatch": ["package.json"],
            "url": "https://json.schemastore.org/package.json"
        }
    ]
}
```

## Essential Extensions

### Core Extensions
```json
// .vscode/extensions.json
{
    "recommendations": [
        "esbenp.prettier-vscode",
        "dbaeumer.vscode-eslint",
        "bradlc.vscode-tailwindcss",
        "ms-vscode.vscode-typescript-next",
        "formulahendry.auto-rename-tag",
        "christian-kohler.path-intellisense",
        "ms-vscode.vscode-json",
        "redhat.vscode-yaml",
        "ms-vscode.vscode-css-peek",
        "ms-vscode.vscode-html-css-support"
    ]
}
```

### React Development Extensions
```json
// .vscode/extensions.json
{
    "recommendations": [
        "esbenp.prettier-vscode",
        "dbaeumer.vscode-eslint",
        "bradlc.vscode-tailwindcss",
        "ms-vscode.vscode-typescript-next",
        "formulahendry.auto-rename-tag",
        "christian-kohler.path-intellisense",
        "ms-vscode.vscode-json",
        "redhat.vscode-yaml",
        "ms-vscode.vscode-css-peek",
        "ms-vscode.vscode-html-css-support",
        "ms-vscode.vscode-react-native",
        "ms-vscode.vscode-react-snippets",
        "ms-vscode.vscode-react-hooks-snippets",
        "ms-vscode.vscode-react-typescript-snippets"
    ]
}
```

## Snippets Configuration

### Custom Snippets
```json
// .vscode/snippets/javascript.json
{
    "React Functional Component": {
        "prefix": "rfc",
        "body": [
            "import React from 'react';",
            "",
            "const ${1:ComponentName} = () => {",
            "  return (",
            "    <div>",
            "      ${2:content}",
            "    </div>",
            "  );",
            "};",
            "",
            "export default ${1:ComponentName};"
        ],
        "description": "React Functional Component"
    },
    "React Hook": {
        "prefix": "rhook",
        "body": [
            "import { useState, useEffect } from 'react';",
            "",
            "const use${1:HookName} = () => {",
            "  const [${2:state}, set${3:State}] = useState(${4:initialValue});",
            "",
            "  useEffect(() => {",
            "    ${5:effect}",
            "  }, [${6:dependencies}]);",
            "",
            "  return { ${2:state}, set${3:State} };",
            "};",
            "",
            "export default use${1:HookName};"
        ],
        "description": "React Custom Hook"
    }
}
```

### TypeScript Snippets
```json
// .vscode/snippets/typescript.json
{
    "TypeScript Interface": {
        "prefix": "tsi",
        "body": [
            "interface ${1:InterfaceName} {",
            "  ${2:property}: ${3:type};",
            "}"
        ],
        "description": "TypeScript Interface"
    },
    "TypeScript Type": {
        "prefix": "tst",
        "body": [
            "type ${1:TypeName} = ${2:type};"
        ],
        "description": "TypeScript Type"
    }
}
```

## Workspace Configuration

### Multi-root Workspace
```json
// frontend.code-workspace
{
    "folders": [
        {
            "name": "Frontend App",
            "path": "./frontend"
        },
        {
            "name": "Backend API",
            "path": "./backend"
        },
        {
            "name": "Shared Components",
            "path": "./shared"
        }
    ],
    "settings": {
        "editor.tabSize": 2,
        "editor.insertSpaces": true,
        "editor.formatOnSave": true,
        "files.exclude": {
            "**/node_modules": true,
            "**/dist": true,
            "**/build": true
        }
    },
    "extensions": {
        "recommendations": [
            "esbenp.prettier-vscode",
            "dbaeumer.vscode-eslint",
            "bradlc.vscode-tailwindcss"
        ]
    }
}
```

## Debug Configuration

### Launch Configuration
```json
// .vscode/launch.json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Launch Chrome",
            "type": "chrome",
            "request": "launch",
            "url": "http://localhost:3000",
            "webRoot": "${workspaceFolder}/src",
            "sourceMaps": true,
            "userDataDir": false
        },
        {
            "name": "Attach to Chrome",
            "type": "chrome",
            "request": "attach",
            "port": 9222,
            "webRoot": "${workspaceFolder}/src",
            "sourceMaps": true
        },
        {
            "name": "Debug Jest Tests",
            "type": "node",
            "request": "launch",
            "program": "${workspaceFolder}/node_modules/.bin/jest",
            "args": ["--runInBand"],
            "console": "integratedTerminal",
            "internalConsoleOptions": "neverOpen"
        }
    ]
}
```

## Tasks Configuration

### Build Tasks
```json
// .vscode/tasks.json
{
    "version": "2.0.0",
    "tasks": [
        {
            "label": "Start Development Server",
            "type": "shell",
            "command": "npm start",
            "group": "build",
            "presentation": {
                "echo": true,
                "reveal": "always",
                "focus": false,
                "panel": "shared"
            },
            "problemMatcher": []
        },
        {
            "label": "Build Production",
            "type": "shell",
            "command": "npm run build",
            "group": "build",
            "presentation": {
                "echo": true,
                "reveal": "always",
                "focus": false,
                "panel": "shared"
            },
            "problemMatcher": []
        },
        {
            "label": "Run Tests",
            "type": "shell",
            "command": "npm test",
            "group": "test",
            "presentation": {
                "echo": true,
                "reveal": "always",
                "focus": false,
                "panel": "shared"
            },
            "problemMatcher": []
        },
        {
            "label": "Lint Code",
            "type": "shell",
            "command": "npm run lint",
            "group": "build",
            "presentation": {
                "echo": true,
                "reveal": "always",
                "focus": false,
                "panel": "shared"
            },
            "problemMatcher": []
        }
    ]
}
```

## Keyboard Shortcuts

### Custom Keybindings
```json
// keybindings.json
[
    {
        "key": "ctrl+shift+f",
        "command": "editor.action.formatDocument",
        "when": "editorTextFocus"
    },
    {
        "key": "ctrl+shift+l",
        "command": "eslint.executeAutofix",
        "when": "editorTextFocus"
    },
    {
        "key": "ctrl+shift+t",
        "command": "workbench.action.terminal.new",
        "when": "!terminalFocus"
    },
    {
        "key": "ctrl+shift+d",
        "command": "workbench.action.debug.start",
        "when": "!inDebugMode"
    }
]
```

## Exercise: Editor Setup

Set up a complete development environment with:
- VS Code configuration
- Essential extensions
- Custom snippets
- Debug configuration
- Build tasks
- Keyboard shortcuts
- Workspace settings

## Key Takeaways
- Configure your editor for maximum productivity
- Use essential extensions for your tech stack
- Create custom snippets for common patterns
- Set up debugging configurations
- Use workspace settings for team consistency
- Configure build tasks for easy access
- Customize keyboard shortcuts for efficiency
- Use multi-root workspaces for monorepos
- Set up proper file exclusions
- Configure language-specific settings
