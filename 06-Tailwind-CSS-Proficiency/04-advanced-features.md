# Tailwind CSS Advanced Features

## Introduction
This lesson covers advanced Tailwind CSS features including custom configurations, plugins, dark mode, animations, and performance optimization techniques.

## Custom Configuration

### Tailwind Config Setup
```javascript
// tailwind.config.js
module.exports = {
  content: [
    "./src/**/*.{js,jsx,ts,tsx}",
    "./public/index.html",
    "./components/**/*.{js,ts,jsx,tsx}",
    "./pages/**/*.{js,ts,jsx,tsx}",
    "./app/**/*.{js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {
      colors: {
        primary: {
          50: '#eff6ff',
          100: '#dbeafe',
          200: '#bfdbfe',
          300: '#93c5fd',
          400: '#60a5fa',
          500: '#3b82f6',
          600: '#2563eb',
          700: '#1d4ed8',
          800: '#1e40af',
          900: '#1e3a8a',
        },
        secondary: {
          50: '#f0fdf4',
          100: '#dcfce7',
          200: '#bbf7d0',
          300: '#86efac',
          400: '#4ade80',
          500: '#22c55e',
          600: '#16a34a',
          700: '#15803d',
          800: '#166534',
          900: '#14532d',
        },
        accent: {
          50: '#fdf2f8',
          100: '#fce7f3',
          200: '#fbcfe8',
          300: '#f9a8d4',
          400: '#f472b6',
          500: '#ec4899',
          600: '#db2777',
          700: '#be185d',
          800: '#9d174d',
          900: '#831843',
        }
      },
      fontFamily: {
        sans: ['Inter', 'system-ui', 'sans-serif'],
        serif: ['Merriweather', 'serif'],
        mono: ['Fira Code', 'monospace'],
      },
      fontSize: {
        'xs': ['0.75rem', { lineHeight: '1rem' }],
        'sm': ['0.875rem', { lineHeight: '1.25rem' }],
        'base': ['1rem', { lineHeight: '1.5rem' }],
        'lg': ['1.125rem', { lineHeight: '1.75rem' }],
        'xl': ['1.25rem', { lineHeight: '1.75rem' }],
        '2xl': ['1.5rem', { lineHeight: '2rem' }],
        '3xl': ['1.875rem', { lineHeight: '2.25rem' }],
        '4xl': ['2.25rem', { lineHeight: '2.5rem' }],
        '5xl': ['3rem', { lineHeight: '1' }],
        '6xl': ['3.75rem', { lineHeight: '1' }],
        '7xl': ['4.5rem', { lineHeight: '1' }],
        '8xl': ['6rem', { lineHeight: '1' }],
        '9xl': ['8rem', { lineHeight: '1' }],
      },
      spacing: {
        '18': '4.5rem',
        '88': '22rem',
        '128': '32rem',
      },
      borderRadius: {
        '4xl': '2rem',
      },
      boxShadow: {
        'inner-lg': 'inset 0 2px 4px 0 rgba(0, 0, 0, 0.1)',
        'glow': '0 0 20px rgba(59, 130, 246, 0.5)',
        'glow-lg': '0 0 40px rgba(59, 130, 246, 0.6)',
      },
      animation: {
        'fade-in': 'fadeIn 0.5s ease-in-out',
        'slide-up': 'slideUp 0.3s ease-out',
        'slide-down': 'slideDown 0.3s ease-out',
        'scale-in': 'scaleIn 0.2s ease-out',
        'bounce-slow': 'bounce 2s infinite',
        'pulse-slow': 'pulse 3s infinite',
      },
      keyframes: {
        fadeIn: {
          '0%': { opacity: '0' },
          '100%': { opacity: '1' },
        },
        slideUp: {
          '0%': { transform: 'translateY(100%)', opacity: '0' },
          '100%': { transform: 'translateY(0)', opacity: '1' },
        },
        slideDown: {
          '0%': { transform: 'translateY(-100%)', opacity: '0' },
          '100%': { transform: 'translateY(0)', opacity: '1' },
        },
        scaleIn: {
          '0%': { transform: 'scale(0)', opacity: '0' },
          '100%': { transform: 'scale(1)', opacity: '1' },
        },
      },
    },
  },
  plugins: [
    require('@tailwindcss/forms'),
    require('@tailwindcss/typography'),
    require('@tailwindcss/aspect-ratio'),
    require('@tailwindcss/line-clamp'),
  ],
  darkMode: 'class',
}
```

## Custom Plugins

### Creating Custom Plugins
```javascript
// plugins/custom-utilities.js
const plugin = require('tailwindcss/plugin')

module.exports = plugin(function({ addUtilities, addComponents, theme }) {
  // Custom utilities
  addUtilities({
    '.text-shadow': {
      textShadow: '0 2px 4px rgba(0,0,0,0.10)',
    },
    '.text-shadow-md': {
      textShadow: '0 4px 8px rgba(0,0,0,0.12), 0 2px 4px rgba(0,0,0,0.08)',
    },
    '.text-shadow-lg': {
      textShadow: '0 15px 35px rgba(0,0,0,0.12), 0 5px 15px rgba(0,0,0,0.07)',
    },
    '.text-shadow-none': {
      textShadow: 'none',
    },
    '.scrollbar-hide': {
      '-ms-overflow-style': 'none',
      'scrollbar-width': 'none',
      '&::-webkit-scrollbar': {
        display: 'none'
      }
    },
    '.scrollbar-default': {
      '-ms-overflow-style': 'auto',
      'scrollbar-width': 'auto',
      '&::-webkit-scrollbar': {
        display: 'block'
      }
    }
  })

  // Custom components
  addComponents({
    '.btn': {
      padding: `${theme('spacing.2')} ${theme('spacing.4')}`,
      borderRadius: theme('borderRadius.md'),
      fontWeight: theme('fontWeight.medium'),
      transition: 'all 0.2s ease-in-out',
      '&:focus': {
        outline: 'none',
        boxShadow: `0 0 0 3px ${theme('colors.blue.500')}33`,
      },
    },
    '.btn-primary': {
      backgroundColor: theme('colors.blue.500'),
      color: theme('colors.white'),
      '&:hover': {
        backgroundColor: theme('colors.blue.600'),
      },
    },
    '.btn-secondary': {
      backgroundColor: theme('colors.gray.200'),
      color: theme('colors.gray.900'),
      '&:hover': {
        backgroundColor: theme('colors.gray.300'),
      },
    },
    '.card': {
      backgroundColor: theme('colors.white'),
      borderRadius: theme('borderRadius.lg'),
      boxShadow: theme('boxShadow.md'),
      padding: theme('spacing.6'),
    },
    '.card-dark': {
      backgroundColor: theme('colors.gray.800'),
      borderRadius: theme('borderRadius.lg'),
      boxShadow: theme('boxShadow.md'),
      padding: theme('spacing.6'),
    },
  })
})
```

## Dark Mode Implementation

### Dark Mode Setup
```html
<!-- Dark Mode Toggle Component -->
<div class="min-h-screen bg-white dark:bg-gray-900 transition-colors duration-300">
  <!-- Header -->
  <header class="bg-white dark:bg-gray-800 shadow-sm border-b border-gray-200 dark:border-gray-700">
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
      <div class="flex justify-between items-center h-16">
        <div class="flex items-center">
          <h1 class="text-xl font-bold text-gray-900 dark:text-white">
            My App
          </h1>
        </div>
        
        <!-- Dark Mode Toggle -->
        <button
          id="theme-toggle"
          class="p-2 rounded-lg bg-gray-100 dark:bg-gray-700 text-gray-500 dark:text-gray-400 hover:bg-gray-200 dark:hover:bg-gray-600 transition-colors duration-200"
          aria-label="Toggle dark mode"
        >
          <!-- Sun Icon (visible in dark mode) -->
          <svg class="w-5 h-5 dark:hidden" fill="currentColor" viewBox="0 0 20 20">
            <path fill-rule="evenodd" d="M10 2a1 1 0 011 1v1a1 1 0 11-2 0V3a1 1 0 011-1zm4 8a4 4 0 11-8 0 4 4 0 018 0zm-.464 4.95l.707.707a1 1 0 001.414-1.414l-.707-.707a1 1 0 00-1.414 1.414zm2.12-10.607a1 1 0 010 1.414l-.706.707a1 1 0 11-1.414-1.414l.707-.707a1 1 0 011.414 0zM17 11a1 1 0 100-2h-1a1 1 0 100 2h1zm-7 4a1 1 0 011 1v1a1 1 0 11-2 0v-1a1 1 0 011-1zM5.05 6.464A1 1 0 106.465 5.05l-.708-.707a1 1 0 00-1.414 1.414l.707.707zm1.414 8.486l-.707.707a1 1 0 01-1.414-1.414l.707-.707a1 1 0 011.414 1.414zM4 11a1 1 0 100-2H3a1 1 0 000 2h1z" clip-rule="evenodd"></path>
          </svg>
          
          <!-- Moon Icon (visible in light mode) -->
          <svg class="w-5 h-5 hidden dark:block" fill="currentColor" viewBox="0 0 20 20">
            <path d="M17.293 13.293A8 8 0 016.707 2.707a8.001 8.001 0 1010.586 10.586z"></path>
          </svg>
        </button>
      </div>
    </div>
  </header>

  <!-- Main Content -->
  <main class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
    <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
      <!-- Card 1 -->
      <div class="card dark:card-dark">
        <h3 class="text-lg font-semibold text-gray-900 dark:text-white mb-2">
          Card Title
        </h3>
        <p class="text-gray-600 dark:text-gray-300">
          This is a sample card that adapts to dark mode.
        </p>
      </div>
      
      <!-- Card 2 -->
      <div class="card dark:card-dark">
        <h3 class="text-lg font-semibold text-gray-900 dark:text-white mb-2">
          Another Card
        </h3>
        <p class="text-gray-600 dark:text-gray-300">
          Another card with dark mode support.
        </p>
      </div>
      
      <!-- Card 3 -->
      <div class="card dark:card-dark">
        <h3 class="text-lg font-semibold text-gray-900 dark:text-white mb-2">
          Third Card
        </h3>
        <p class="text-gray-600 dark:text-gray-300">
          Third card demonstrating dark mode.
        </p>
      </div>
    </div>
  </main>
</div>

<script>
// Dark Mode Toggle Script
const themeToggle = document.getElementById('theme-toggle');
const html = document.documentElement;

// Check for saved theme preference or default to 'light'
const currentTheme = localStorage.getItem('theme') || 'light';
html.classList.toggle('dark', currentTheme === 'dark');

themeToggle.addEventListener('click', () => {
  const isDark = html.classList.contains('dark');
  html.classList.toggle('dark', !isDark);
  localStorage.setItem('theme', !isDark ? 'dark' : 'light');
});
</script>
```

## Advanced Animations

### Custom Animation Components
```html
<!-- Animated Loading Spinner -->
<div class="flex items-center justify-center min-h-screen">
  <div class="animate-spin rounded-full h-32 w-32 border-b-2 border-blue-500"></div>
</div>

<!-- Fade In Animation -->
<div class="animate-fade-in">
  <h1 class="text-4xl font-bold text-gray-900 dark:text-white">
    Welcome to Our App
  </h1>
</div>

<!-- Slide Up Animation -->
<div class="animate-slide-up">
  <div class="bg-white dark:bg-gray-800 rounded-lg shadow-lg p-6">
    <h2 class="text-2xl font-semibold text-gray-900 dark:text-white mb-4">
      Slide Up Animation
    </h2>
    <p class="text-gray-600 dark:text-gray-300">
      This content slides up from the bottom.
    </p>
  </div>
</div>

<!-- Scale In Animation -->
<div class="animate-scale-in">
  <button class="bg-blue-500 hover:bg-blue-600 text-white font-bold py-2 px-4 rounded-lg transition-colors duration-200">
    Scale In Button
  </button>
</div>

<!-- Bounce Animation -->
<div class="animate-bounce-slow">
  <div class="w-4 h-4 bg-red-500 rounded-full"></div>
</div>

<!-- Pulse Animation -->
<div class="animate-pulse-slow">
  <div class="w-8 h-8 bg-green-500 rounded-full"></div>
</div>

<!-- Hover Animations -->
<div class="group cursor-pointer">
  <div class="transform group-hover:scale-105 transition-transform duration-300 ease-in-out">
    <div class="bg-gradient-to-r from-purple-400 to-pink-400 rounded-lg p-6 text-white">
      <h3 class="text-xl font-bold mb-2">Hover to Scale</h3>
      <p>This card scales up on hover</p>
    </div>
  </div>
</div>

<!-- Rotate Animation -->
<div class="group cursor-pointer">
  <div class="transform group-hover:rotate-3 transition-transform duration-300 ease-in-out">
    <div class="bg-gradient-to-r from-blue-400 to-cyan-400 rounded-lg p-6 text-white">
      <h3 class="text-xl font-bold mb-2">Hover to Rotate</h3>
      <p>This card rotates slightly on hover</p>
    </div>
  </div>
</div>
```

## Performance Optimization

### Purge Configuration
```javascript
// tailwind.config.js - Optimized for production
module.exports = {
  content: [
    "./src/**/*.{js,jsx,ts,tsx}",
    "./public/index.html",
  ],
  theme: {
    extend: {
      // Only extend what you actually use
    },
  },
  plugins: [
    // Only include plugins you actually use
    require('@tailwindcss/forms'),
  ],
  // Enable JIT mode for better performance
  mode: 'jit',
  // Purge unused styles in production
  purge: {
    enabled: process.env.NODE_ENV === 'production',
    content: [
      "./src/**/*.{js,jsx,ts,tsx}",
      "./public/index.html",
    ],
    options: {
      safelist: [
        // Keep these classes even if not detected
        'animate-pulse',
        'animate-spin',
        'animate-bounce',
      ]
    }
  }
}
```

### CSS-in-JS Integration
```javascript
// styled-components with Tailwind
import styled from 'styled-components';
import tw from 'twin.macro';

const Button = styled.button`
  ${tw`
    px-4 py-2 rounded-lg font-medium
    bg-blue-500 hover:bg-blue-600
    text-white transition-colors duration-200
    focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-opacity-50
  `}
  
  ${props => props.variant === 'secondary' && tw`
    bg-gray-200 hover:bg-gray-300 text-gray-900
    focus:ring-gray-500
  `}
  
  ${props => props.size === 'large' && tw`
    px-6 py-3 text-lg
  `}
`;

// Usage
<Button variant="secondary" size="large">
  Click me
</Button>
```

## Exercise: Advanced Tailwind Features

Create a comprehensive project using:
- Custom Tailwind configuration
- Custom plugins and utilities
- Dark mode implementation
- Advanced animations
- Performance optimization
- CSS-in-JS integration

## Key Takeaways
- Configure Tailwind for your specific needs
- Create custom plugins and utilities
- Implement dark mode effectively
- Use advanced animations and transitions
- Optimize Tailwind for production
- Integrate with CSS-in-JS libraries
- Use custom color palettes
- Implement responsive design patterns
- Use custom typography settings
- Optimize bundle size with purging
- Create reusable component styles
- Use custom spacing and sizing
- Implement custom shadows and effects
- Use custom border radius values
- Create custom animation keyframes
