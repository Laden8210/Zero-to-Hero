# Responsive Design with Tailwind

## Introduction
Tailwind CSS provides powerful responsive design utilities that work seamlessly with its mobile-first approach. This lesson covers responsive breakpoints, utility patterns, and mobile-first development.

## Breakpoint System

### Default Breakpoints
```html
<!-- Mobile-first responsive design -->
<div class="w-full sm:w-1/2 md:w-1/3 lg:w-1/4 xl:w-1/5">
    <!-- Full width on mobile, half on small screens, third on medium, etc. -->
</div>

<!-- Responsive text sizing -->
<h1 class="text-2xl sm:text-3xl md:text-4xl lg:text-5xl xl:text-6xl">
    Responsive Heading
</h1>

<!-- Responsive spacing -->
<div class="p-4 sm:p-6 md:p-8 lg:p-10 xl:p-12">
    <!-- Responsive padding -->
</div>

<!-- Responsive grid -->
<div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-4">
    <div class="bg-blue-500 p-4">Item 1</div>
    <div class="bg-green-500 p-4">Item 2</div>
    <div class="bg-red-500 p-4">Item 3</div>
    <div class="bg-yellow-500 p-4">Item 4</div>
</div>

<!-- Responsive flexbox -->
<div class="flex flex-col sm:flex-row items-center justify-between">
    <div class="mb-4 sm:mb-0">Left content</div>
    <div>Right content</div>
</div>
```

### Custom Breakpoints
```javascript
// tailwind.config.js
module.exports = {
    theme: {
        screens: {
            'xs': '475px',
            'sm': '640px',
            'md': '768px',
            'lg': '1024px',
            'xl': '1280px',
            '2xl': '1536px',
            '3xl': '1920px',
            // Custom breakpoints
            'tablet': '768px',
            'laptop': '1024px',
            'desktop': '1280px',
            // Max-width breakpoints
            'max-sm': {'max': '639px'},
            'max-md': {'max': '767px'},
            'max-lg': {'max': '1023px'},
            'max-xl': {'max': '1279px'},
        }
    }
}
```

```html
<!-- Using custom breakpoints -->
<div class="w-full tablet:w-1/2 laptop:w-1/3 desktop:w-1/4">
    <!-- Custom breakpoint usage -->
</div>

<!-- Max-width breakpoints -->
<div class="hidden max-md:block">
    <!-- Only visible on screens smaller than md -->
</div>

<div class="block max-md:hidden">
    <!-- Hidden on screens smaller than md -->
</div>
```

## Responsive Utility Patterns

### Stacking Layouts
```html
<!-- Mobile: stacked, Desktop: side-by-side -->
<div class="flex flex-col lg:flex-row gap-6">
    <div class="flex-1">
        <h2 class="text-xl font-bold mb-4">Main Content</h2>
        <p class="text-gray-600">Main content goes here...</p>
    </div>
    
    <div class="lg:w-80">
        <h3 class="text-lg font-semibold mb-3">Sidebar</h3>
        <div class="space-y-3">
            <div class="p-3 bg-gray-100 rounded">Sidebar item 1</div>
            <div class="p-3 bg-gray-100 rounded">Sidebar item 2</div>
            <div class="p-3 bg-gray-100 rounded">Sidebar item 3</div>
        </div>
    </div>
</div>

<!-- Responsive card layout -->
<div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-6">
    <div class="bg-white rounded-lg shadow-md p-6">
        <h3 class="text-lg font-semibold mb-2">Card Title</h3>
        <p class="text-gray-600">Card content goes here...</p>
    </div>
    <!-- More cards... -->
</div>
```

### Typography Scaling
```html
<!-- Responsive typography -->
<div class="space-y-4">
    <h1 class="text-3xl sm:text-4xl md:text-5xl lg:text-6xl font-bold">
        Responsive Heading
    </h1>
    
    <h2 class="text-xl sm:text-2xl md:text-3xl lg:text-4xl font-semibold">
        Subheading
    </h2>
    
    <p class="text-sm sm:text-base md:text-lg lg:text-xl text-gray-600">
        Responsive paragraph text that scales with screen size
    </p>
    
    <p class="text-xs sm:text-sm md:text-base text-gray-500">
        Small responsive text
    </p>
</div>

<!-- Responsive line height -->
<p class="text-lg leading-tight sm:leading-normal md:leading-relaxed lg:leading-loose">
    Responsive line height for better readability
</p>
```

### Spacing Adjustments
```html
<!-- Responsive spacing -->
<div class="p-4 sm:p-6 md:p-8 lg:p-10 xl:p-12">
    <div class="space-y-4 sm:space-y-6 md:space-y-8">
        <div class="p-3 sm:p-4 md:p-5 lg:p-6">
            <h3 class="mb-2 sm:mb-3 md:mb-4">Title</h3>
            <p class="text-sm sm:text-base">Content</p>
        </div>
    </div>
</div>

<!-- Responsive margins -->
<div class="mx-4 sm:mx-6 md:mx-8 lg:mx-12 xl:mx-16">
    <div class="my-8 sm:my-12 md:my-16 lg:my-20">
        <!-- Content with responsive margins -->
    </div>
</div>
```

## Responsive Navigation

### Mobile-First Navigation
```html
<!-- Responsive navigation -->
<nav class="bg-white shadow-lg">
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
        <div class="flex justify-between h-16">
            <!-- Logo -->
            <div class="flex items-center">
                <div class="flex-shrink-0">
                    <h1 class="text-xl sm:text-2xl font-bold text-gray-800">
                        Logo
                    </h1>
                </div>
            </div>
            
            <!-- Desktop navigation -->
            <div class="hidden md:block">
                <div class="ml-10 flex items-baseline space-x-4">
                    <a href="#" class="text-gray-900 hover:text-blue-600 px-3 py-2 rounded-md text-sm font-medium">
                        Home
                    </a>
                    <a href="#" class="text-gray-600 hover:text-blue-600 px-3 py-2 rounded-md text-sm font-medium">
                        About
                    </a>
                    <a href="#" class="text-gray-600 hover:text-blue-600 px-3 py-2 rounded-md text-sm font-medium">
                        Contact
                    </a>
                </div>
            </div>
            
            <!-- Mobile menu button -->
            <div class="md:hidden flex items-center">
                <button class="mobile-menu-button inline-flex items-center justify-center p-2 rounded-md text-gray-600 hover:text-gray-900 hover:bg-gray-100 focus:outline-none focus:ring-2 focus:ring-inset focus:ring-blue-500">
                    <span class="sr-only">Open main menu</span>
                    <!-- Hamburger icon -->
                    <svg class="block h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 12h16M4 18h16" />
                    </svg>
                </button>
            </div>
        </div>
    </div>
    
    <!-- Mobile menu -->
    <div class="mobile-menu hidden md:hidden">
        <div class="px-2 pt-2 pb-3 space-y-1 sm:px-3">
            <a href="#" class="text-gray-900 hover:text-blue-600 block px-3 py-2 rounded-md text-base font-medium">
                Home
            </a>
            <a href="#" class="text-gray-600 hover:text-blue-600 block px-3 py-2 rounded-md text-base font-medium">
                About
            </a>
            <a href="#" class="text-gray-600 hover:text-blue-600 block px-3 py-2 rounded-md text-base font-medium">
                Contact
            </a>
        </div>
    </div>
</nav>
```

### Responsive Sidebar
```html
<!-- Responsive sidebar layout -->
<div class="flex h-screen bg-gray-100">
    <!-- Sidebar -->
    <div class="hidden lg:flex lg:flex-shrink-0">
        <div class="flex flex-col w-64">
            <div class="flex flex-col h-0 flex-1 bg-white border-r border-gray-200">
                <div class="flex-1 flex flex-col pt-5 pb-4 overflow-y-auto">
                    <div class="flex items-center flex-shrink-0 px-4">
                        <h1 class="text-lg font-semibold text-gray-900">Sidebar</h1>
                    </div>
                    <nav class="mt-5 flex-1 px-2 space-y-1">
                        <a href="#" class="text-gray-900 hover:bg-gray-50 group flex items-center px-2 py-2 text-sm font-medium rounded-md">
                            Dashboard
                        </a>
                        <a href="#" class="text-gray-600 hover:bg-gray-50 group flex items-center px-2 py-2 text-sm font-medium rounded-md">
                            Projects
                        </a>
                        <a href="#" class="text-gray-600 hover:bg-gray-50 group flex items-center px-2 py-2 text-sm font-medium rounded-md">
                            Settings
                        </a>
                    </nav>
                </div>
            </div>
        </div>
    </div>
    
    <!-- Main content -->
    <div class="flex flex-col w-0 flex-1 overflow-hidden">
        <div class="lg:hidden">
            <!-- Mobile sidebar toggle -->
            <button class="mobile-sidebar-toggle ml-4 mt-4 p-2 rounded-md text-gray-600 hover:text-gray-900 hover:bg-gray-100">
                <svg class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 12h16M4 18h16" />
                </svg>
            </button>
        </div>
        
        <main class="flex-1 relative overflow-y-auto focus:outline-none">
            <div class="py-6">
                <div class="max-w-7xl mx-auto px-4 sm:px-6 md:px-8">
                    <h1 class="text-2xl font-semibold text-gray-900">Main Content</h1>
                    <p class="mt-2 text-gray-600">Content goes here...</p>
                </div>
            </div>
        </main>
    </div>
</div>
```

## Responsive Forms

### Mobile-Optimized Forms
```html
<!-- Responsive form layout -->
<form class="max-w-2xl mx-auto p-4 sm:p-6 lg:p-8">
    <div class="space-y-6">
        <!-- Form title -->
        <div class="text-center">
            <h2 class="text-2xl sm:text-3xl font-bold text-gray-900">
                Contact Us
            </h2>
            <p class="mt-2 text-sm sm:text-base text-gray-600">
                Get in touch with us
            </p>
        </div>
        
        <!-- Form fields -->
        <div class="grid grid-cols-1 sm:grid-cols-2 gap-4 sm:gap-6">
            <div>
                <label for="firstName" class="block text-sm font-medium text-gray-700">
                    First Name
                </label>
                <input
                    type="text"
                    id="firstName"
                    name="firstName"
                    class="mt-1 block w-full px-3 py-2 sm:py-3 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-blue-500 focus:border-blue-500 text-sm sm:text-base"
                    placeholder="Enter your first name"
                />
            </div>
            
            <div>
                <label for="lastName" class="block text-sm font-medium text-gray-700">
                    Last Name
                </label>
                <input
                    type="text"
                    id="lastName"
                    name="lastName"
                    class="mt-1 block w-full px-3 py-2 sm:py-3 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-blue-500 focus:border-blue-500 text-sm sm:text-base"
                    placeholder="Enter your last name"
                />
            </div>
        </div>
        
        <div>
            <label for="email" class="block text-sm font-medium text-gray-700">
                Email Address
            </label>
            <input
                type="email"
                id="email"
                name="email"
                class="mt-1 block w-full px-3 py-2 sm:py-3 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-blue-500 focus:border-blue-500 text-sm sm:text-base"
                placeholder="Enter your email"
            />
        </div>
        
        <div>
            <label for="message" class="block text-sm font-medium text-gray-700">
                Message
            </label>
            <textarea
                id="message"
                name="message"
                rows="4"
                class="mt-1 block w-full px-3 py-2 sm:py-3 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-blue-500 focus:border-blue-500 text-sm sm:text-base"
                placeholder="Enter your message"
            ></textarea>
        </div>
        
        <!-- Submit button -->
        <div class="flex flex-col sm:flex-row gap-3 sm:gap-4">
            <button
                type="submit"
                class="flex-1 bg-blue-600 text-white px-4 py-2 sm:py-3 rounded-md hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2 text-sm sm:text-base font-medium"
            >
                Send Message
            </button>
            
            <button
                type="button"
                class="flex-1 bg-gray-200 text-gray-800 px-4 py-2 sm:py-3 rounded-md hover:bg-gray-300 focus:outline-none focus:ring-2 focus:ring-gray-500 focus:ring-offset-2 text-sm sm:text-base font-medium"
            >
                Cancel
            </button>
        </div>
    </div>
</form>
```

## Responsive Images and Media

### Responsive Images
```html
<!-- Responsive image with different sizes -->
<div class="w-full max-w-4xl mx-auto">
    <img
        src="image-small.jpg"
        srcset="image-small.jpg 480w, image-medium.jpg 768w, image-large.jpg 1024w"
        sizes="(max-width: 480px) 100vw, (max-width: 768px) 100vw, (max-width: 1024px) 100vw, 1024px"
        alt="Responsive image"
        class="w-full h-auto rounded-lg shadow-lg"
    />
</div>

<!-- Responsive image container -->
<div class="relative w-full h-48 sm:h-64 md:h-80 lg:h-96">
    <img
        src="hero-image.jpg"
        alt="Hero image"
        class="absolute inset-0 w-full h-full object-cover rounded-lg"
    />
    <div class="absolute inset-0 bg-black bg-opacity-40 rounded-lg"></div>
    <div class="absolute inset-0 flex items-center justify-center">
        <h1 class="text-white text-2xl sm:text-3xl md:text-4xl lg:text-5xl font-bold text-center">
            Responsive Hero
        </h1>
    </div>
</div>

<!-- Responsive video -->
<div class="relative w-full h-48 sm:h-64 md:h-80 lg:h-96">
    <video
        class="absolute inset-0 w-full h-full object-cover rounded-lg"
        autoplay
        muted
        loop
    >
        <source src="video-small.mp4" media="(max-width: 768px)" />
        <source src="video-large.mp4" media="(min-width: 769px)" />
    </video>
</div>
```

## Exercise: Responsive Website with Tailwind

Create a responsive website using Tailwind CSS:
- Mobile-first responsive navigation
- Responsive grid layouts
- Responsive typography and spacing
- Mobile-optimized forms
- Responsive images and media
- Custom breakpoints if needed

## Key Takeaways
- Use mobile-first approach with Tailwind's responsive utilities
- Leverage Tailwind's breakpoint system for consistent responsive design
- Apply responsive utilities to typography, spacing, and layout
- Create mobile-optimized navigation patterns
- Use responsive images and media for better performance
- Test responsive design on real devices
- Consider custom breakpoints for specific use cases
- Use responsive spacing utilities for consistent design
- Implement responsive forms for better mobile experience
- Optimize for touch interactions on mobile devices
