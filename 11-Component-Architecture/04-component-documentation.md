# Component Documentation

## Introduction
Component documentation is essential for maintaining and scaling React applications. This lesson covers documentation strategies, tools, patterns, and best practices for component documentation.

## Documentation Strategies

### JSDoc Documentation
```jsx
/**
 * A reusable button component with multiple variants and sizes
 * 
 * @component
 * @param {Object} props - The component props
 * @param {string} props.variant - The button variant ('primary', 'secondary', 'danger', 'success')
 * @param {string} props.size - The button size ('small', 'medium', 'large')
 * @param {boolean} props.disabled - Whether the button is disabled
 * @param {Function} props.onClick - Click handler function
 * @param {React.ReactNode} props.children - Button content
 * @param {string} props.className - Additional CSS classes
 * @param {Object} props.style - Inline styles
 * @param {string} props.type - Button type ('button', 'submit', 'reset')
 * @param {string} props.testId - Test identifier for testing
 * 
 * @example
 * // Basic usage
 * <Button onClick={() => console.log('clicked')}>
 *   Click me
 * </Button>
 * 
 * @example
 * // With variant and size
 * <Button variant="primary" size="large" onClick={handleClick}>
 *   Submit
 * </Button>
 * 
 * @example
 * // Disabled button
 * <Button disabled onClick={handleClick}>
 *   Disabled
 * </Button>
 */
function Button({
    variant = 'primary',
    size = 'medium',
    disabled = false,
    onClick,
    children,
    className = '',
    style = {},
    type = 'button',
    testId,
    ...props
}) {
    const buttonClass = `btn btn-${variant} btn-${size} ${className}`.trim();
    
    return (
        <button
            type={type}
            className={buttonClass}
            disabled={disabled}
            onClick={onClick}
            style={style}
            data-testid={testId}
            {...props}
        >
            {children}
        </button>
    );
}

/**
 * PropTypes for Button component
 */
Button.propTypes = {
    variant: PropTypes.oneOf(['primary', 'secondary', 'danger', 'success']),
    size: PropTypes.oneOf(['small', 'medium', 'large']),
    disabled: PropTypes.bool,
    onClick: PropTypes.func,
    children: PropTypes.node.isRequired,
    className: PropTypes.string,
    style: PropTypes.object,
    type: PropTypes.oneOf(['button', 'submit', 'reset']),
    testId: PropTypes.string
};

/**
 * Default props for Button component
 */
Button.defaultProps = {
    variant: 'primary',
    size: 'medium',
    disabled: false,
    className: '',
    style: {},
    type: 'button'
};

export default Button;
```

### Storybook Documentation
```javascript
// Button.stories.js
import React from 'react';
import { action } from '@storybook/addon-actions';
import { Button } from './Button';

export default {
    title: 'Components/Button',
    component: Button,
    parameters: {
        docs: {
            description: {
                component: 'A reusable button component with multiple variants and sizes. Use this component for all button interactions in the application.'
            }
        }
    },
    argTypes: {
        variant: {
            control: { type: 'select' },
            options: ['primary', 'secondary', 'danger', 'success'],
            description: 'The visual style variant of the button',
            table: {
                type: { summary: 'string' },
                defaultValue: { summary: 'primary' }
            }
        },
        size: {
            control: { type: 'select' },
            options: ['small', 'medium', 'large'],
            description: 'The size of the button',
            table: {
                type: { summary: 'string' },
                defaultValue: { summary: 'medium' }
            }
        },
        disabled: {
            control: { type: 'boolean' },
            description: 'Whether the button is disabled',
            table: {
                type: { summary: 'boolean' },
                defaultValue: { summary: 'false' }
            }
        },
        onClick: {
            action: 'clicked',
            description: 'Function called when button is clicked',
            table: {
                type: { summary: 'function' }
            }
        },
        children: {
            control: { type: 'text' },
            description: 'Button content',
            table: {
                type: { summary: 'ReactNode' }
            }
        }
    }
};

// Basic story
export const Primary = {
    args: {
        variant: 'primary',
        children: 'Primary Button',
        onClick: action('primary-clicked')
    }
};

// Secondary story
export const Secondary = {
    args: {
        variant: 'secondary',
        children: 'Secondary Button',
        onClick: action('secondary-clicked')
    }
};

// Danger story
export const Danger = {
    args: {
        variant: 'danger',
        children: 'Danger Button',
        onClick: action('danger-clicked')
    }
};

// Success story
export const Success = {
    args: {
        variant: 'success',
        children: 'Success Button',
        onClick: action('success-clicked')
    }
};

// Size variations
export const Sizes = {
    render: () => (
        <div style={{ display: 'flex', gap: '1rem', alignItems: 'center' }}>
            <Button size="small" onClick={action('small-clicked')}>
                Small
            </Button>
            <Button size="medium" onClick={action('medium-clicked')}>
                Medium
            </Button>
            <Button size="large" onClick={action('large-clicked')}>
                Large
            </Button>
        </div>
    ),
    parameters: {
        docs: {
            description: {
                story: 'Button component supports three different sizes: small, medium, and large.'
            }
        }
    }
};

// Disabled state
export const Disabled = {
    args: {
        disabled: true,
        children: 'Disabled Button',
        onClick: action('disabled-clicked')
    },
    parameters: {
        docs: {
            description: {
                story: 'Disabled buttons cannot be clicked and have a different visual appearance.'
            }
        }
    }
};

// All variants
export const AllVariants = {
    render: () => (
        <div style={{ display: 'flex', gap: '1rem', flexWrap: 'wrap' }}>
            <Button variant="primary" onClick={action('primary-clicked')}>
                Primary
            </Button>
            <Button variant="secondary" onClick={action('secondary-clicked')}>
                Secondary
            </Button>
            <Button variant="danger" onClick={action('danger-clicked')}>
                Danger
            </Button>
            <Button variant="success" onClick={action('success-clicked')}>
                Success
            </Button>
        </div>
    ),
    parameters: {
        docs: {
            description: {
                story: 'All available button variants displayed together.'
            }
        }
    }
};
```

### Component API Documentation
```markdown
# Button Component

A reusable button component with multiple variants and sizes.

## Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `variant` | `'primary' \| 'secondary' \| 'danger' \| 'success'` | `'primary'` | The visual style variant of the button |
| `size` | `'small' \| 'medium' \| 'large'` | `'medium'` | The size of the button |
| `disabled` | `boolean` | `false` | Whether the button is disabled |
| `onClick` | `function` | - | Function called when button is clicked |
| `children` | `ReactNode` | - | Button content |
| `className` | `string` | `''` | Additional CSS classes |
| `style` | `object` | `{}` | Inline styles |
| `type` | `'button' \| 'submit' \| 'reset'` | `'button'` | Button type |
| `testId` | `string` | - | Test identifier for testing |

## Examples

### Basic Usage

```jsx
<Button onClick={() => console.log('clicked')}>
  Click me
</Button>
```

### With Variant and Size

```jsx
<Button variant="primary" size="large" onClick={handleClick}>
  Submit
</Button>
```

### Disabled Button

```jsx
<Button disabled onClick={handleClick}>
  Disabled
</Button>
```

### Form Submission

```jsx
<form onSubmit={handleSubmit}>
  <Button type="submit" variant="primary">
    Submit Form
  </Button>
</form>
```

## Styling

The Button component uses CSS classes for styling. You can customize the appearance by:

1. **Using CSS custom properties:**
```css
.btn-primary {
  --btn-bg: #007bff;
  --btn-color: white;
  --btn-border: #007bff;
}
```

2. **Adding custom classes:**
```jsx
<Button className="custom-button" onClick={handleClick}>
  Custom Button
</Button>
```

3. **Using inline styles:**
```jsx
<Button style={{ backgroundColor: 'red' }} onClick={handleClick}>
  Red Button
</Button>
```

## Accessibility

The Button component includes the following accessibility features:

- Proper ARIA attributes
- Keyboard navigation support
- Screen reader compatibility
- Focus management

## Testing

Use the `testId` prop for reliable testing:

```jsx
<Button testId="submit-button" onClick={handleClick}>
  Submit
</Button>
```

```javascript
// In your tests
const submitButton = screen.getByTestId('submit-button');
fireEvent.click(submitButton);
```

## Best Practices

1. **Use semantic variants:** Choose variants that match the action (e.g., 'danger' for delete actions)
2. **Provide clear labels:** Use descriptive text for button content
3. **Handle loading states:** Show loading indicators for async actions
4. **Test interactions:** Always test button click handlers
5. **Consider accessibility:** Ensure buttons are keyboard accessible
```

## Documentation Tools

### Storybook Configuration
```javascript
// .storybook/main.js
module.exports = {
    stories: ['../src/**/*.stories.@(js|jsx|ts|tsx)'],
    addons: [
        '@storybook/addon-essentials',
        '@storybook/addon-docs',
        '@storybook/addon-controls',
        '@storybook/addon-actions',
        '@storybook/addon-viewport',
        '@storybook/addon-backgrounds',
        '@storybook/addon-a11y'
    ],
    features: {
        buildStoriesJson: true
    }
};

// .storybook/preview.js
import { addParameters } from '@storybook/react';
import { themes } from '@storybook/theming';

addParameters({
    docs: {
        theme: themes.light
    },
    backgrounds: {
        default: 'light',
        values: [
            { name: 'light', value: '#ffffff' },
            { name: 'dark', value: '#333333' }
        ]
    },
    viewport: {
        viewports: {
            mobile: {
                name: 'Mobile',
                styles: { width: '375px', height: '667px' }
            },
            tablet: {
                name: 'Tablet',
                styles: { width: '768px', height: '1024px' }
            },
            desktop: {
                name: 'Desktop',
                styles: { width: '1024px', height: '768px' }
            }
        }
    }
});
```

### Documentation Generation
```javascript
// scripts/generate-docs.js
const fs = require('fs');
const path = require('path');
const { execSync } = require('child_process');

function generateDocs() {
    console.log('Generating component documentation...');
    
    // Generate Storybook build
    execSync('npm run build-storybook', { stdio: 'inherit' });
    
    // Generate API documentation
    execSync('npm run docs:api', { stdio: 'inherit' });
    
    // Generate component index
    generateComponentIndex();
    
    console.log('Documentation generated successfully!');
}

function generateComponentIndex() {
    const componentsDir = path.join(__dirname, '../src/components');
    const components = fs.readdirSync(componentsDir)
        .filter(file => fs.statSync(path.join(componentsDir, file)).isDirectory())
        .map(component => ({
            name: component,
            path: `./${component}`,
            description: getComponentDescription(component)
        }));
    
    const indexContent = `# Component Library

This is the component library documentation for the application.

## Components

${components.map(comp => `- [${comp.name}](${comp.path}) - ${comp.description}`).join('\n')}

## Usage

Import components from the component library:

\`\`\`javascript
import { Button, Input, Modal } from '@/components';
\`\`\`

## Development

To add a new component:

1. Create a new directory in \`src/components\`
2. Add the component file
3. Create a Storybook story
4. Add tests
5. Update this documentation

## Testing

Run tests with:

\`\`\`bash
npm test
\`\`\`

## Storybook

View the interactive component library:

\`\`\`bash
npm run storybook
\`\`\`
`;
    
    fs.writeFileSync(path.join(__dirname, '../docs/README.md'), indexContent);
}

function getComponentDescription(componentName) {
    // Try to read description from component file
    const componentPath = path.join(__dirname, '../src/components', componentName, 'index.js');
    
    if (fs.existsSync(componentPath)) {
        const content = fs.readFileSync(componentPath, 'utf8');
        const match = content.match(/@description\s+(.+)/);
        if (match) {
            return match[1];
        }
    }
    
    return 'Component description not available';
}

generateDocs();
```

## Documentation Best Practices

### Component Documentation Template
```jsx
/**
 * ComponentName - Brief description of what the component does
 * 
 * @component
 * @param {Object} props - The component props
 * @param {string} props.prop1 - Description of prop1
 * @param {number} props.prop2 - Description of prop2
 * @param {Function} props.onAction - Description of callback
 * @param {React.ReactNode} props.children - Description of children
 * 
 * @example
 * // Basic usage
 * <ComponentName prop1="value" onAction={handleAction}>
 *   Content
 * </ComponentName>
 * 
 * @example
 * // Advanced usage
 * <ComponentName 
 *   prop1="value" 
 *   prop2={42} 
 *   onAction={handleAction}
 *   className="custom-class"
 * >
 *   <div>Custom content</div>
 * </ComponentName>
 * 
 * @see {@link https://example.com} External documentation
 * @since 1.0.0
 * @version 1.2.0
 */
function ComponentName({
    prop1,
    prop2 = 0,
    onAction,
    children,
    className = '',
    ...props
}) {
    // Component implementation
    return (
        <div className={`component-name ${className}`} {...props}>
            {children}
        </div>
    );
}

// PropTypes
ComponentName.propTypes = {
    prop1: PropTypes.string.isRequired,
    prop2: PropTypes.number,
    onAction: PropTypes.func,
    children: PropTypes.node,
    className: PropTypes.string
};

// Default props
ComponentName.defaultProps = {
    prop2: 0,
    className: ''
};

export default ComponentName;
```

## Exercise: Component Documentation System

Create a comprehensive component documentation system with:
- JSDoc documentation
- Storybook stories
- API documentation
- Usage examples
- Accessibility guidelines
- Testing documentation
- Best practices guide

## Key Takeaways
- Document all component props and their types
- Provide clear usage examples
- Include accessibility information
- Document testing strategies
- Use consistent documentation format
- Keep documentation up to date
- Include visual examples in Storybook
- Document component behavior and edge cases
- Provide migration guides for breaking changes
- Use automated documentation generation tools
