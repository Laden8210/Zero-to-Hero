# Build Tools and Bundlers

## Introduction
Build tools and bundlers are essential for modern frontend development. This lesson covers webpack, Vite, esbuild, and other build tools for creating efficient development and production builds.

## Webpack Configuration

### Basic Webpack Setup
```javascript
// webpack.config.js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const { CleanWebpackPlugin } = require('clean-webpack-plugin');

module.exports = {
    entry: './src/index.js',
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: '[name].[contenthash].js',
        chunkFilename: '[name].[contenthash].chunk.js',
        publicPath: '/',
        clean: true
    },
    
    module: {
        rules: [
            {
                test: /\.(js|jsx)$/,
                exclude: /node_modules/,
                use: {
                    loader: 'babel-loader',
                    options: {
                        presets: [
                            ['@babel/preset-env', { targets: 'defaults' }],
                            ['@babel/preset-react', { runtime: 'automatic' }]
                        ],
                        plugins: [
                            '@babel/plugin-proposal-class-properties',
                            '@babel/plugin-syntax-dynamic-import'
                        ]
                    }
                }
            },
            {
                test: /\.css$/,
                use: [
                    MiniCssExtractPlugin.loader,
                    'css-loader',
                    'postcss-loader'
                ]
            },
            {
                test: /\.(scss|sass)$/,
                use: [
                    MiniCssExtractPlugin.loader,
                    'css-loader',
                    'postcss-loader',
                    'sass-loader'
                ]
            },
            {
                test: /\.(png|jpg|jpeg|gif|svg)$/,
                type: 'asset/resource',
                generator: {
                    filename: 'images/[name].[contenthash][ext]'
                }
            },
            {
                test: /\.(woff|woff2|eot|ttf|otf)$/,
                type: 'asset/resource',
                generator: {
                    filename: 'fonts/[name].[contenthash][ext]'
                }
            }
        ]
    },
    
    plugins: [
        new CleanWebpackPlugin(),
        new HtmlWebpackPlugin({
            template: './public/index.html',
            filename: 'index.html',
            inject: true
        }),
        new MiniCssExtractPlugin({
            filename: '[name].[contenthash].css',
            chunkFilename: '[name].[contenthash].chunk.css'
        })
    ],
    
    resolve: {
        extensions: ['.js', '.jsx', '.json'],
        alias: {
            '@': path.resolve(__dirname, 'src'),
            '@components': path.resolve(__dirname, 'src/components'),
            '@utils': path.resolve(__dirname, 'src/utils'),
            '@hooks': path.resolve(__dirname, 'src/hooks')
        }
    },
    
    optimization: {
        splitChunks: {
            chunks: 'all',
            cacheGroups: {
                vendor: {
                    test: /[\\/]node_modules[\\/]/,
                    name: 'vendors',
                    chunks: 'all'
                },
                common: {
                    name: 'common',
                    minChunks: 2,
                    chunks: 'all',
                    enforce: true
                }
            }
        }
    },
    
    devServer: {
        static: {
            directory: path.join(__dirname, 'public')
        },
        compress: true,
        port: 3000,
        hot: true,
        historyApiFallback: true,
        open: true
    }
};
```

**Code Explanation:**
- `const path = require('path')`: Node.js module for working with file paths
- `entry: './src/index.js'`: Entry point where webpack starts bundling
- `path: path.resolve(__dirname, 'dist')`: Output directory for built files
- `filename: '[name].[contenthash].js'`: Output filename with content-based hash for caching
- `chunkFilename: '[name].[contenthash].chunk.js'`: Naming for code-split chunks
- `publicPath: '/'`: Public URL path for assets
- `clean: true`: Cleans output directory before each build
- `test: /\.(js|jsx)$/`: Regular expression to match JavaScript/JSX files
- `exclude: /node_modules/`: Excludes node_modules from processing
- `loader: 'babel-loader'`: Transpiles modern JavaScript to compatible code
- `@babel/preset-env`: Transpiles ES6+ syntax based on browser targets
- `@babel/preset-react`: Transforms JSX syntax to JavaScript
- `runtime: 'automatic'`: Uses new JSX transform (React 17+)
- `MiniCssExtractPlugin.loader`: Extracts CSS into separate files
- `css-loader`: Processes CSS imports and resolves dependencies
- `postcss-loader`: Applies PostCSS transformations (autoprefixer, etc.)
- `sass-loader`: Compiles Sass/SCSS to CSS
- `type: 'asset/resource'`: Webpack 5 asset module for files
- `generator: { filename: ... }`: Custom filename pattern for assets
- `new CleanWebpackPlugin()`: Cleans dist folder before build
- `new HtmlWebpackPlugin({...})`: Generates HTML file with script tags
- `template: './public/index.html'`: HTML template to use
- `inject: true`: Automatically injects bundled assets
- `extensions: ['.js', '.jsx', '.json']`: File extensions to resolve
- `alias: { '@': ... }`: Path aliases for cleaner imports

**Benefits:**
- Bundles and optimizes code for production
- Enables modern JavaScript features through transpilation
- Handles different file types (CSS, images, fonts)
- Provides content-based hashing for optimal caching
- Supports code splitting and lazy loading
- Offers development server with hot reloading

### Advanced Webpack Configuration
```javascript
// webpack.config.js
const path = require('path');
const webpack = require('webpack');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const { CleanWebpackPlugin } = require('clean-webpack-plugin');
const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin;
const TerserPlugin = require('terser-webpack-plugin');
const CssMinimizerPlugin = require('css-minimizer-webpack-plugin');

module.exports = (env, argv) => {
    const isProduction = argv.mode === 'production';
    const isDevelopment = !isProduction;
    
    return {
        entry: {
            main: './src/index.js',
            vendor: ['react', 'react-dom']
        },
        
        output: {
            path: path.resolve(__dirname, 'dist'),
            filename: isProduction ? '[name].[contenthash].js' : '[name].js',
            chunkFilename: isProduction ? '[name].[contenthash].chunk.js' : '[name].chunk.js',
            publicPath: '/',
            clean: true
        },
        
        module: {
            rules: [
                {
                    test: /\.(js|jsx)$/,
                    exclude: /node_modules/,
                    use: {
                        loader: 'babel-loader',
                        options: {
                            presets: [
                                ['@babel/preset-env', { 
                                    targets: 'defaults',
                                    useBuiltIns: 'usage',
                                    corejs: 3
                                }],
                                ['@babel/preset-react', { runtime: 'automatic' }]
                            ],
                            plugins: [
                                '@babel/plugin-proposal-class-properties',
                                '@babel/plugin-syntax-dynamic-import',
                                isDevelopment && 'react-refresh/babel'
                            ].filter(Boolean)
                        }
                    }
                },
                {
                    test: /\.css$/,
                    use: [
                        isProduction ? MiniCssExtractPlugin.loader : 'style-loader',
                        'css-loader',
                        'postcss-loader'
                    ]
                },
                {
                    test: /\.(scss|sass)$/,
                    use: [
                        isProduction ? MiniCssExtractPlugin.loader : 'style-loader',
                        'css-loader',
                        'postcss-loader',
                        'sass-loader'
                    ]
                },
                {
                    test: /\.(png|jpg|jpeg|gif|svg)$/,
                    type: 'asset/resource',
                    generator: {
                        filename: 'images/[name].[contenthash][ext]'
                    }
                },
                {
                    test: /\.(woff|woff2|eot|ttf|otf)$/,
                    type: 'asset/resource',
                    generator: {
                        filename: 'fonts/[name].[contenthash][ext]'
                    }
                }
            ]
        },
        
        plugins: [
            new CleanWebpackPlugin(),
            new HtmlWebpackPlugin({
                template: './public/index.html',
                filename: 'index.html',
                inject: true,
                minify: isProduction ? {
                    removeComments: true,
                    collapseWhitespace: true,
                    removeRedundantAttributes: true,
                    useShortDoctype: true,
                    removeEmptyAttributes: true,
                    removeStyleLinkTypeAttributes: true,
                    keepClosingSlash: true,
                    minifyJS: true,
                    minifyCSS: true,
                    minifyURLs: true
                } : false
            }),
            isProduction && new MiniCssExtractPlugin({
                filename: '[name].[contenthash].css',
                chunkFilename: '[name].[contenthash].chunk.css'
            }),
            isDevelopment && new webpack.HotModuleReplacementPlugin(),
            isDevelopment && new webpack.NoEmitOnErrorsPlugin(),
            new webpack.DefinePlugin({
                'process.env.NODE_ENV': JSON.stringify(isProduction ? 'production' : 'development')
            }),
            isProduction && new BundleAnalyzerPlugin({
                analyzerMode: 'static',
                openAnalyzer: false,
                reportFilename: 'bundle-report.html'
            })
        ].filter(Boolean),
        
        resolve: {
            extensions: ['.js', '.jsx', '.json'],
            alias: {
                '@': path.resolve(__dirname, 'src'),
                '@components': path.resolve(__dirname, 'src/components'),
                '@utils': path.resolve(__dirname, 'src/utils'),
                '@hooks': path.resolve(__dirname, 'src/hooks'),
                '@styles': path.resolve(__dirname, 'src/styles')
            }
        },
        
        optimization: {
            minimize: isProduction,
            minimizer: [
                new TerserPlugin({
                    terserOptions: {
                        compress: {
                            drop_console: isProduction,
                            drop_debugger: isProduction
                        }
                    }
                }),
                new CssMinimizerPlugin()
            ],
            splitChunks: {
                chunks: 'all',
                cacheGroups: {
                    vendor: {
                        test: /[\\/]node_modules[\\/]/,
                        name: 'vendors',
                        chunks: 'all'
                    },
                    common: {
                        name: 'common',
                        minChunks: 2,
                        chunks: 'all',
                        enforce: true
                    }
                }
            }
        },
        
        devServer: {
            static: {
                directory: path.join(__dirname, 'public')
            },
            compress: true,
            port: 3000,
            hot: true,
            historyApiFallback: true,
            open: true,
            client: {
                overlay: {
                    errors: true,
                    warnings: false
                }
            }
        },
        
        devtool: isDevelopment ? 'eval-source-map' : 'source-map'
    };
};
```

## Vite Configuration

### Basic Vite Setup
```javascript
// vite.config.js
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import { resolve } from 'path';

export default defineConfig({
    plugins: [react()],
    
    root: './src',
    
    build: {
        outDir: '../dist',
        emptyOutDir: true,
        sourcemap: true,
        rollupOptions: {
            input: {
                main: resolve(__dirname, 'src/index.html')
            },
            output: {
                manualChunks: {
                    vendor: ['react', 'react-dom'],
                    utils: ['lodash', 'moment']
                }
            }
        }
    },
    
    resolve: {
        alias: {
            '@': resolve(__dirname, 'src'),
            '@components': resolve(__dirname, 'src/components'),
            '@utils': resolve(__dirname, 'src/utils'),
            '@hooks': resolve(__dirname, 'src/hooks')
        }
    },
    
    server: {
        port: 3000,
        open: true,
        cors: true
    },
    
    css: {
        preprocessorOptions: {
            scss: {
                additionalData: `@import "@/styles/variables.scss";`
            }
        }
    }
});
```

### Advanced Vite Configuration
```javascript
// vite.config.js
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import { resolve } from 'path';
import { visualizer } from 'rollup-plugin-visualizer';

export default defineConfig(({ command, mode }) => {
    const isProduction = mode === 'production';
    const isDevelopment = !isProduction;
    
    return {
        plugins: [
            react({
                babel: {
                    plugins: [
                        ['@babel/plugin-proposal-decorators', { legacy: true }],
                        ['@babel/plugin-proposal-class-properties', { loose: true }]
                    ]
                }
            }),
            isProduction && visualizer({
                filename: 'dist/bundle-analysis.html',
                open: false,
                gzipSize: true,
                brotliSize: true
            })
        ].filter(Boolean),
        
        root: './src',
        
        build: {
            outDir: '../dist',
            emptyOutDir: true,
            sourcemap: isDevelopment,
            minify: isProduction ? 'terser' : false,
            terserOptions: isProduction ? {
                compress: {
                    drop_console: true,
                    drop_debugger: true
                }
            } : undefined,
            rollupOptions: {
                input: {
                    main: resolve(__dirname, 'src/index.html')
                },
                output: {
                    manualChunks: {
                        vendor: ['react', 'react-dom'],
                        router: ['react-router-dom'],
                        utils: ['lodash', 'moment', 'axios']
                    }
                }
            }
        },
        
        resolve: {
            alias: {
                '@': resolve(__dirname, 'src'),
                '@components': resolve(__dirname, 'src/components'),
                '@utils': resolve(__dirname, 'src/utils'),
                '@hooks': resolve(__dirname, 'src/hooks'),
                '@styles': resolve(__dirname, 'src/styles')
            }
        },
        
        server: {
            port: 3000,
            open: true,
            cors: true,
            hmr: {
                overlay: true
            }
        },
        
        css: {
            preprocessorOptions: {
                scss: {
                    additionalData: `@import "@/styles/variables.scss";`
                }
            }
        },
        
        define: {
            __DEV__: isDevelopment,
            __PROD__: isProduction
        }
    };
});
```

## esbuild Configuration

### Basic esbuild Setup
```javascript
// build.js
const esbuild = require('esbuild');
const path = require('path');

const build = async () => {
    try {
        await esbuild.build({
            entryPoints: ['src/index.js'],
            bundle: true,
            outfile: 'dist/bundle.js',
            platform: 'browser',
            target: 'es2015',
            format: 'iife',
            sourcemap: true,
            minify: true,
            define: {
                'process.env.NODE_ENV': '"production"'
            }
        });
        
        console.log('Build completed successfully!');
    } catch (error) {
        console.error('Build failed:', error);
        process.exit(1);
    }
};

build();
```

### Advanced esbuild Configuration
```javascript
// build.js
const esbuild = require('esbuild');
const path = require('path');
const fs = require('fs');

const isProduction = process.env.NODE_ENV === 'production';
const isDevelopment = !isProduction;

const build = async () => {
    const startTime = Date.now();
    
    try {
        // Build main bundle
        await esbuild.build({
            entryPoints: ['src/index.js'],
            bundle: true,
            outfile: 'dist/bundle.js',
            platform: 'browser',
            target: 'es2015',
            format: 'iife',
            sourcemap: isDevelopment,
            minify: isProduction,
            define: {
                'process.env.NODE_ENV': `"${process.env.NODE_ENV || 'development'}"`
            },
            loader: {
                '.js': 'jsx',
                '.jsx': 'jsx',
                '.ts': 'ts',
                '.tsx': 'tsx',
                '.css': 'css',
                '.png': 'file',
                '.jpg': 'file',
                '.jpeg': 'file',
                '.gif': 'file',
                '.svg': 'file',
                '.woff': 'file',
                '.woff2': 'file',
                '.eot': 'file',
                '.ttf': 'file',
                '.otf': 'file'
            },
            plugins: [
                {
                    name: 'css-plugin',
                    setup(build) {
                        build.onLoad({ filter: /\.css$/ }, async (args) => {
                            const css = await fs.promises.readFile(args.path, 'utf8');
                            return {
                                contents: css,
                                loader: 'css'
                            };
                        });
                    }
                }
            ]
        });
        
        // Build vendor bundle
        await esbuild.build({
            entryPoints: ['src/vendor.js'],
            bundle: true,
            outfile: 'dist/vendor.js',
            platform: 'browser',
            target: 'es2015',
            format: 'iife',
            sourcemap: isDevelopment,
            minify: isProduction,
            external: ['react', 'react-dom']
        });
        
        const endTime = Date.now();
        console.log(`Build completed in ${endTime - startTime}ms`);
        
    } catch (error) {
        console.error('Build failed:', error);
        process.exit(1);
    }
};

build();
```

## Package Management

### npm Configuration
```json
{
    "name": "frontend-app",
    "version": "1.0.0",
    "description": "Frontend application",
    "main": "src/index.js",
    "scripts": {
        "start": "webpack serve --mode development",
        "build": "webpack --mode production",
        "build:dev": "webpack --mode development",
        "test": "jest",
        "test:watch": "jest --watch",
        "test:coverage": "jest --coverage",
        "lint": "eslint src --ext .js,.jsx",
        "lint:fix": "eslint src --ext .js,.jsx --fix",
        "format": "prettier --write src/**/*.{js,jsx,css,scss,md}",
        "analyze": "webpack-bundle-analyzer dist/bundle-report.html",
        "clean": "rimraf dist",
        "prebuild": "npm run clean",
        "postbuild": "npm run analyze"
    },
    "dependencies": {
        "react": "^18.0.0",
        "react-dom": "^18.0.0",
        "react-router-dom": "^6.0.0",
        "axios": "^1.0.0",
        "lodash": "^4.17.21"
    },
    "devDependencies": {
        "@babel/core": "^7.20.0",
        "@babel/preset-env": "^7.20.0",
        "@babel/preset-react": "^7.18.0",
        "@babel/plugin-proposal-class-properties": "^7.18.0",
        "@babel/plugin-syntax-dynamic-import": "^7.18.0",
        "babel-loader": "^9.1.0",
        "webpack": "^5.75.0",
        "webpack-cli": "^5.0.0",
        "webpack-dev-server": "^4.11.0",
        "html-webpack-plugin": "^5.5.0",
        "mini-css-extract-plugin": "^2.7.0",
        "css-loader": "^6.7.0",
        "sass-loader": "^13.2.0",
        "sass": "^1.56.0",
        "postcss-loader": "^7.0.0",
        "postcss": "^8.4.0",
        "autoprefixer": "^10.4.0",
        "clean-webpack-plugin": "^4.0.0",
        "terser-webpack-plugin": "^5.3.0",
        "css-minimizer-webpack-plugin": "^4.2.0",
        "webpack-bundle-analyzer": "^4.8.0",
        "eslint": "^8.30.0",
        "eslint-plugin-react": "^7.31.0",
        "eslint-plugin-react-hooks": "^4.6.0",
        "prettier": "^2.8.0",
        "jest": "^29.3.0",
        "@testing-library/react": "^13.4.0",
        "@testing-library/jest-dom": "^5.16.0",
        "rimraf": "^3.0.0"
    },
    "browserslist": {
        "production": [
            ">0.2%",
            "not dead",
            "not op_mini all"
        ],
        "development": [
            "last 1 chrome version",
            "last 1 firefox version",
            "last 1 safari version"
        ]
    },
    "engines": {
        "node": ">=16.0.0",
        "npm": ">=8.0.0"
    }
}
```

### Yarn Configuration
```yaml
# .yarnrc.yml
nodeLinker: node-modules
yarnPath: .yarn/releases/yarn-3.3.0.cjs

# package.json
{
    "name": "frontend-app",
    "version": "1.0.0",
    "packageManager": "yarn@3.3.0",
    "scripts": {
        "start": "yarn webpack serve --mode development",
        "build": "yarn webpack --mode production",
        "test": "yarn jest",
        "lint": "yarn eslint src --ext .js,.jsx",
        "format": "yarn prettier --write src/**/*.{js,jsx,css,scss,md}"
    },
    "dependencies": {
        "react": "^18.0.0",
        "react-dom": "^18.0.0"
    },
    "devDependencies": {
        "webpack": "^5.75.0",
        "webpack-cli": "^5.0.0"
    }
}
```

## Exercise: Build Tools System

Create a comprehensive build tools system with:
- Webpack configuration
- Vite configuration
- esbuild setup
- Package management
- Build optimization
- Development server setup
- Production builds

## Key Takeaways
- Choose the right build tool for your project
- Configure build tools for optimal performance
- Use code splitting for better loading times
- Optimize builds for production
- Set up development servers for hot reloading
- Use package managers effectively
- Monitor bundle sizes and performance
- Configure build tools for different environments
- Use build tools for asset optimization
- Maintain build configurations over time
