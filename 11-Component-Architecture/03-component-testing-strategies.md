# Component Testing Strategies

## Introduction
Component testing is crucial for maintaining reliable React applications. This lesson covers testing strategies, tools, patterns, and best practices for component testing.

## Testing Setup and Tools

### Testing Environment Setup
```javascript
// jest.config.js
module.exports = {
    testEnvironment: 'jsdom',
    setupFilesAfterEnv: ['<rootDir>/src/setupTests.js'],
    moduleNameMapping: {
        '\\.(css|less|scss|sass)$': 'identity-obj-proxy',
        '\\.(gif|ttf|eot|svg|png)$': '<rootDir>/src/__mocks__/fileMock.js'
    },
    collectCoverageFrom: [
        'src/**/*.{js,jsx}',
        '!src/index.js',
        '!src/reportWebVitals.js'
    ],
    coverageThreshold: {
        global: {
            branches: 80,
            functions: 80,
            lines: 80,
            statements: 80
        }
    }
};

// src/setupTests.js
import '@testing-library/jest-dom';
import { configure } from '@testing-library/react';

// Configure testing library
configure({ testIdAttribute: 'data-testid' });

// Mock IntersectionObserver
global.IntersectionObserver = class IntersectionObserver {
    constructor() {}
    observe() {}
    unobserve() {}
    disconnect() {}
};

// Mock ResizeObserver
global.ResizeObserver = class ResizeObserver {
    constructor() {}
    observe() {}
    unobserve() {}
    disconnect() {}
};

// Mock matchMedia
Object.defineProperty(window, 'matchMedia', {
    writable: true,
    value: jest.fn().mockImplementation(query => ({
        matches: false,
        media: query,
        onchange: null,
        addListener: jest.fn(),
        removeListener: jest.fn(),
        addEventListener: jest.fn(),
        removeEventListener: jest.fn(),
        dispatchEvent: jest.fn(),
    })),
});

// src/__mocks__/fileMock.js
module.exports = 'test-file-stub';
```

### Testing Utilities
```javascript
// src/test-utils.js
import React from 'react';
import { render, screen } from '@testing-library/react';
import { BrowserRouter } from 'react-router-dom';
import { ThemeProvider } from './contexts/ThemeContext';
import { UserProvider } from './contexts/UserContext';

// Custom render function with providers
function renderWithProviders(ui, { 
    initialRoute = '/',
    theme = 'light',
    user = null,
    ...renderOptions 
} = {}) {
    function Wrapper({ children }) {
        return (
            <BrowserRouter>
                <ThemeProvider initialTheme={theme}>
                    <UserProvider initialUser={user}>
                        {children}
                    </UserProvider>
                </ThemeProvider>
            </BrowserRouter>
        );
    }
    
    return render(ui, { wrapper: Wrapper, ...renderOptions });
}

// Mock API responses
const mockApiResponse = (data, status = 200) => {
    return Promise.resolve({
        ok: status >= 200 && status < 300,
        status,
        json: () => Promise.resolve(data)
    });
};

// Mock fetch
const mockFetch = (responses = []) => {
    const responsesQueue = [...responses];
    
    global.fetch = jest.fn(() => {
        const response = responsesQueue.shift();
        if (!response) {
            throw new Error('No more mock responses available');
        }
        return Promise.resolve(response);
    });
    
    return {
        addResponse: (response) => responsesQueue.push(response),
        clear: () => responsesQueue.length = 0
    };
};

// Test data factories
const createUser = (overrides = {}) => ({
    id: 1,
    name: 'John Doe',
    email: 'john@example.com',
    role: 'user',
    ...overrides
});

const createPost = (overrides = {}) => ({
    id: 1,
    title: 'Test Post',
    content: 'This is a test post',
    author: 'John Doe',
    publishedAt: '2024-01-01T00:00:00Z',
    ...overrides
});

// Custom matchers
expect.extend({
    toBeInTheDocument(received) {
        const pass = received && received.ownerDocument && received.ownerDocument.body.contains(received);
        return {
            pass,
            message: () => `Expected element ${pass ? 'not ' : ''}to be in the document`
        };
    }
});

export {
    renderWithProviders,
    mockApiResponse,
    mockFetch,
    createUser,
    createPost
};
```

## Component Testing Patterns

### Basic Component Testing
```javascript
// src/components/Button.test.js
import React from 'react';
import { render, screen, fireEvent } from '@testing-library/react';
import { Button } from './Button';

describe('Button', () => {
    it('renders with correct text', () => {
        render(<Button>Click me</Button>);
        expect(screen.getByRole('button', { name: 'Click me' })).toBeInTheDocument();
    });
    
    it('calls onClick when clicked', () => {
        const handleClick = jest.fn();
        render(<Button onClick={handleClick}>Click me</Button>);
        
        fireEvent.click(screen.getByRole('button'));
        expect(handleClick).toHaveBeenCalledTimes(1);
    });
    
    it('applies correct variant class', () => {
        render(<Button variant="primary">Primary</Button>);
        expect(screen.getByRole('button')).toHaveClass('btn-primary');
    });
    
    it('applies correct size class', () => {
        render(<Button size="large">Large</Button>);
        expect(screen.getByRole('button')).toHaveClass('btn-large');
    });
    
    it('is disabled when disabled prop is true', () => {
        render(<Button disabled>Disabled</Button>);
        expect(screen.getByRole('button')).toBeDisabled();
    });
    
    it('does not call onClick when disabled', () => {
        const handleClick = jest.fn();
        render(<Button disabled onClick={handleClick}>Disabled</Button>);
        
        fireEvent.click(screen.getByRole('button'));
        expect(handleClick).not.toHaveBeenCalled();
    });
    
    it('renders with custom className', () => {
        render(<Button className="custom-class">Custom</Button>);
        expect(screen.getByRole('button')).toHaveClass('custom-class');
    });
});
```

### Form Component Testing
```javascript
// src/components/ContactForm.test.js
import React from 'react';
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { ContactForm } from './ContactForm';
import { mockFetch } from '../test-utils';

describe('ContactForm', () => {
    beforeEach(() => {
        mockFetch.clear();
    });
    
    it('renders all form fields', () => {
        render(<ContactForm />);
        
        expect(screen.getByLabelText(/name/i)).toBeInTheDocument();
        expect(screen.getByLabelText(/email/i)).toBeInTheDocument();
        expect(screen.getByLabelText(/message/i)).toBeInTheDocument();
        expect(screen.getByRole('button', { name: /submit/i })).toBeInTheDocument();
    });
    
    it('shows validation errors for empty fields', async () => {
        const user = userEvent.setup();
        render(<ContactForm />);
        
        const submitButton = screen.getByRole('button', { name: /submit/i });
        await user.click(submitButton);
        
        await waitFor(() => {
            expect(screen.getByText(/name is required/i)).toBeInTheDocument();
            expect(screen.getByText(/email is required/i)).toBeInTheDocument();
            expect(screen.getByText(/message is required/i)).toBeInTheDocument();
        });
    });
    
    it('shows validation error for invalid email', async () => {
        const user = userEvent.setup();
        render(<ContactForm />);
        
        const emailInput = screen.getByLabelText(/email/i);
        await user.type(emailInput, 'invalid-email');
        
        const submitButton = screen.getByRole('button', { name: /submit/i });
        await user.click(submitButton);
        
        await waitFor(() => {
            expect(screen.getByText(/invalid email format/i)).toBeInTheDocument();
        });
    });
    
    it('submits form with valid data', async () => {
        const user = userEvent.setup();
        const mockOnSubmit = jest.fn();
        
        mockFetch.addResponse(mockApiResponse({ success: true }));
        
        render(<ContactForm onSubmit={mockOnSubmit} />);
        
        await user.type(screen.getByLabelText(/name/i), 'John Doe');
        await user.type(screen.getByLabelText(/email/i), 'john@example.com');
        await user.type(screen.getByLabelText(/message/i), 'Hello world');
        
        const submitButton = screen.getByRole('button', { name: /submit/i });
        await user.click(submitButton);
        
        await waitFor(() => {
            expect(mockOnSubmit).toHaveBeenCalledWith({
                name: 'John Doe',
                email: 'john@example.com',
                message: 'Hello world'
            });
        });
    });
    
    it('shows loading state during submission', async () => {
        const user = userEvent.setup();
        
        // Mock a delayed response
        mockFetch.addResponse(
            new Promise(resolve => 
                setTimeout(() => resolve(mockApiResponse({ success: true })), 100)
            )
        );
        
        render(<ContactForm />);
        
        await user.type(screen.getByLabelText(/name/i), 'John Doe');
        await user.type(screen.getByLabelText(/email/i), 'john@example.com');
        await user.type(screen.getByLabelText(/message/i), 'Hello world');
        
        const submitButton = screen.getByRole('button', { name: /submit/i });
        await user.click(submitButton);
        
        expect(screen.getByText(/submitting/i)).toBeInTheDocument();
        expect(submitButton).toBeDisabled();
    });
    
    it('shows error message on submission failure', async () => {
        const user = userEvent.setup();
        
        mockFetch.addResponse(mockApiResponse({ error: 'Server error' }, 500));
        
        render(<ContactForm />);
        
        await user.type(screen.getByLabelText(/name/i), 'John Doe');
        await user.type(screen.getByLabelText(/email/i), 'john@example.com');
        await user.type(screen.getByLabelText(/message/i), 'Hello world');
        
        const submitButton = screen.getByRole('button', { name: /submit/i });
        await user.click(submitButton);
        
        await waitFor(() => {
            expect(screen.getByText(/failed to submit/i)).toBeInTheDocument();
        });
    });
});
```

### Hook Testing
```javascript
// src/hooks/useForm.test.js
import { renderHook, act } from '@testing-library/react';
import { useForm } from './useForm';

describe('useForm', () => {
    it('initializes with default values', () => {
        const { result } = renderHook(() => useForm({ name: '', email: '' }));
        
        expect(result.current.values).toEqual({ name: '', email: '' });
        expect(result.current.errors).toEqual({});
        expect(result.current.touched).toEqual({});
    });
    
    it('updates values when setValue is called', () => {
        const { result } = renderHook(() => useForm({ name: '', email: '' }));
        
        act(() => {
            result.current.setValue('name', 'John Doe');
        });
        
        expect(result.current.values.name).toBe('John Doe');
    });
    
    it('marks field as touched when setFieldTouched is called', () => {
        const { result } = renderHook(() => useForm({ name: '', email: '' }));
        
        act(() => {
            result.current.setFieldTouched('name');
        });
        
        expect(result.current.touched.name).toBe(true);
    });
    
    it('validates required fields', () => {
        const { result } = renderHook(() => useForm({ name: '', email: '' }));
        
        act(() => {
            result.current.validate();
        });
        
        expect(result.current.errors.name).toBe('name is required');
        expect(result.current.errors.email).toBe('email is required');
    });
    
    it('clears errors when values change', () => {
        const { result } = renderHook(() => useForm({ name: '', email: '' }));
        
        // First validate to create errors
        act(() => {
            result.current.validate();
        });
        
        expect(result.current.errors.name).toBe('name is required');
        
        // Then update value
        act(() => {
            result.current.setValue('name', 'John Doe');
        });
        
        expect(result.current.errors.name).toBeNull();
    });
    
    it('resets form to initial values', () => {
        const { result } = renderHook(() => useForm({ name: '', email: '' }));
        
        // Update values
        act(() => {
            result.current.setValue('name', 'John Doe');
            result.current.setValue('email', 'john@example.com');
        });
        
        expect(result.current.values.name).toBe('John Doe');
        expect(result.current.values.email).toBe('john@example.com');
        
        // Reset form
        act(() => {
            result.current.reset();
        });
        
        expect(result.current.values).toEqual({ name: '', email: '' });
        expect(result.current.errors).toEqual({});
        expect(result.current.touched).toEqual({});
    });
});
```

### Integration Testing
```javascript
// src/components/UserList.test.js
import React from 'react';
import { render, screen, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { UserList } from './UserList';
import { mockFetch, createUser } from '../test-utils';

describe('UserList Integration', () => {
    beforeEach(() => {
        mockFetch.clear();
    });
    
    it('loads and displays users', async () => {
        const users = [
            createUser({ id: 1, name: 'John Doe' }),
            createUser({ id: 2, name: 'Jane Smith' })
        ];
        
        mockFetch.addResponse(mockApiResponse(users));
        
        render(<UserList />);
        
        expect(screen.getByText(/loading/i)).toBeInTheDocument();
        
        await waitFor(() => {
            expect(screen.getByText('John Doe')).toBeInTheDocument();
            expect(screen.getByText('Jane Smith')).toBeInTheDocument();
        });
    });
    
    it('handles user selection', async () => {
        const users = [createUser({ id: 1, name: 'John Doe' })];
        mockFetch.addResponse(mockApiResponse(users));
        
        const mockOnUserSelect = jest.fn();
        render(<UserList onUserSelect={mockOnUserSelect} />);
        
        await waitFor(() => {
            expect(screen.getByText('John Doe')).toBeInTheDocument();
        });
        
        const user = userEvent.setup();
        await user.click(screen.getByText('View'));
        
        expect(mockOnUserSelect).toHaveBeenCalledWith(users[0]);
    });
    
    it('handles user deletion', async () => {
        const users = [createUser({ id: 1, name: 'John Doe' })];
        mockFetch.addResponse(mockApiResponse(users));
        mockFetch.addResponse(mockApiResponse({ success: true }));
        
        render(<UserList />);
        
        await waitFor(() => {
            expect(screen.getByText('John Doe')).toBeInTheDocument();
        });
        
        const user = userEvent.setup();
        await user.click(screen.getByText('Delete'));
        
        await waitFor(() => {
            expect(screen.queryByText('John Doe')).not.toBeInTheDocument();
        });
    });
    
    it('handles API errors gracefully', async () => {
        mockFetch.addResponse(mockApiResponse({ error: 'Server error' }, 500));
        
        render(<UserList />);
        
        await waitFor(() => {
            expect(screen.getByText(/error loading users/i)).toBeInTheDocument();
        });
    });
    
    it('refreshes data when refresh button is clicked', async () => {
        const users = [createUser({ id: 1, name: 'John Doe' })];
        mockFetch.addResponse(mockApiResponse(users));
        mockFetch.addResponse(mockApiResponse(users));
        
        render(<UserList />);
        
        await waitFor(() => {
            expect(screen.getByText('John Doe')).toBeInTheDocument();
        });
        
        const user = userEvent.setup();
        await user.click(screen.getByText(/refresh/i));
        
        expect(screen.getByText(/loading/i)).toBeInTheDocument();
        
        await waitFor(() => {
            expect(screen.getByText('John Doe')).toBeInTheDocument();
        });
    });
});
```

## Advanced Testing Patterns

### Mocking External Dependencies
```javascript
// src/components/DataVisualization.test.js
import React from 'react';
import { render, screen } from '@testing-library/react';
import { DataVisualization } from './DataVisualization';

// Mock Chart.js
jest.mock('chart.js', () => ({
    Chart: jest.fn(() => ({
        destroy: jest.fn(),
        update: jest.fn()
    }))
}));

// Mock WebSocket
const mockWebSocket = {
    addEventListener: jest.fn(),
    removeEventListener: jest.fn(),
    send: jest.fn(),
    close: jest.fn()
};

global.WebSocket = jest.fn(() => mockWebSocket);

describe('DataVisualization', () => {
    beforeEach(() => {
        jest.clearAllMocks();
    });
    
    it('renders chart with initial data', () => {
        const data = [
            { label: 'Jan', value: 100 },
            { label: 'Feb', value: 200 }
        ];
        
        render(<DataVisualization data={data} />);
        
        expect(screen.getByTestId('chart-container')).toBeInTheDocument();
    });
    
    it('updates chart when data changes', () => {
        const initialData = [
            { label: 'Jan', value: 100 }
        ];
        
        const { rerender } = render(<DataVisualization data={initialData} />);
        
        const newData = [
            { label: 'Jan', value: 100 },
            { label: 'Feb', value: 200 }
        ];
        
        rerender(<DataVisualization data={newData} />);
        
        // Chart should be updated with new data
        expect(screen.getByTestId('chart-container')).toBeInTheDocument();
    });
    
    it('connects to WebSocket for real-time updates', () => {
        render(<DataVisualization realTime={true} />);
        
        expect(global.WebSocket).toHaveBeenCalledWith('ws://localhost:8080/data');
        expect(mockWebSocket.addEventListener).toHaveBeenCalledWith('message', expect.any(Function));
    });
    
    it('disconnects WebSocket on unmount', () => {
        const { unmount } = render(<DataVisualization realTime={true} />);
        
        unmount();
        
        expect(mockWebSocket.close).toHaveBeenCalled();
    });
});
```

### Testing Error Boundaries
```javascript
// src/components/ErrorBoundary.test.js
import React from 'react';
import { render, screen } from '@testing-library/react';
import { ErrorBoundary } from './ErrorBoundary';

// Component that throws an error
const ThrowError = ({ shouldThrow }) => {
    if (shouldThrow) {
        throw new Error('Test error');
    }
    return <div>No error</div>;
};

describe('ErrorBoundary', () => {
    beforeEach(() => {
        // Suppress console.error for tests
        jest.spyOn(console, 'error').mockImplementation(() => {});
    });
    
    afterEach(() => {
        console.error.mockRestore();
    });
    
    it('renders children when there is no error', () => {
        render(
            <ErrorBoundary>
                <ThrowError shouldThrow={false} />
            </ErrorBoundary>
        );
        
        expect(screen.getByText('No error')).toBeInTheDocument();
    });
    
    it('renders error UI when there is an error', () => {
        render(
            <ErrorBoundary>
                <ThrowError shouldThrow={true} />
            </ErrorBoundary>
        );
        
        expect(screen.getByText(/something went wrong/i)).toBeInTheDocument();
        expect(screen.getByText('Test error')).toBeInTheDocument();
    });
    
    it('calls onError callback when error occurs', () => {
        const onError = jest.fn();
        
        render(
            <ErrorBoundary onError={onError}>
                <ThrowError shouldThrow={true} />
            </ErrorBoundary>
        );
        
        expect(onError).toHaveBeenCalledWith(
            expect.any(Error),
            expect.any(Object)
        );
    });
    
    it('resets error state when reset button is clicked', () => {
        const { rerender } = render(
            <ErrorBoundary>
                <ThrowError shouldThrow={true} />
            </ErrorBoundary>
        );
        
        expect(screen.getByText(/something went wrong/i)).toBeInTheDocument();
        
        const resetButton = screen.getByText(/try again/i);
        fireEvent.click(resetButton);
        
        rerender(
            <ErrorBoundary>
                <ThrowError shouldThrow={false} />
            </ErrorBoundary>
        );
        
        expect(screen.getByText('No error')).toBeInTheDocument();
    });
});
```

## Exercise: Component Testing System

Create a comprehensive component testing system with:
- Testing environment setup
- Component testing patterns
- Form component testing
- Hook testing
- Integration testing
- Mocking external dependencies
- Error boundary testing
- Test utilities and helpers

## Key Takeaways
- Set up proper testing environment with necessary tools
- Test component behavior, not implementation details
- Use testing library queries that reflect user interactions
- Mock external dependencies appropriately
- Test error states and edge cases
- Write integration tests for complex workflows
- Use custom render functions for providers
- Test hooks in isolation
- Mock API calls and external services
- Maintain good test coverage and quality
