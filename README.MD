### Steps for webpack-setup

- run npm init
- run npm i --save-dev webpack webpack-dev-server 

[comment]: <> (--save-dev is for development only dependencies)

- create src folder
- create index.html file in src folder and enter the html5 DOCTYPE declaration
- create a div with id root in the body
- create an assets folder in src
- create components and containers folder in src
- then create index.js and index.css file in the src folder
- fill in any global style in index.css and import the file in index.js
- then create App.js in src folder
- code your project
- now, install all dependencies

        npm i --save react rect-dom react-router-dom
        npm i -D babel-loader babel-core babel-preset-react babel-preset-env babel-preset-stage-2 css-loader style-loader postcss-loader autoprefixer url-loader file-loader babel-plugin-syntax-dynamic-import html-webpack-plugin uglifyjs-webpack-plugin

- now to configure babel create a new file **.babelrc**
- add following code to .babelrc file

        {
            "presets": [
                ["env", {
                    "targets": {
                        "browsers": [
                            "> 1%",
                            "last 2 versions"
                        ]
                    }
                }],
                "stage-2",
                "react"
            ],
            "plugins": [
                "syntax-dynamic-import"
            ]
        }

- add ["start": "webpack-dev-server"] in scripts in package.json
- now create a file in root "webpack.config.js"
- add following code in webpack.config.js

        const path = require('path');
        const autoprefixer = require('autoprefixer');

        module.exports = {
            devtool: 'cheap-module-eval-source-map',
            entry: './src/index.js',
            output: {
                path: path.resolve(__dirname, 'dist'),
                filename: 'bundle.js',
                chunkFilename: '[id].js',
                publicPath: ''
            },
            resolve: {
                extensions: ['.js', '.jsx']
            },
            module: {
                rules: [
                    {
                        test: /\.js$/,
                        loader: 'babel-loader',
                        exclude: /node_modules/
                    },
                    {
                        test: /\.css$/,
                        exclude: /node_modules/,
                        use: [
                            { loader: 'style-loader' },
                            { 
                                loader: 'css-loader',
                                options: {
                                    importLoaders: 1,
                                    modules: true,
                                    localIdentName: '[name]__[local]__[hash:base64:5]'
                                }
                            },
                            {
                                loader: 'postcss-loader',
                                options: {
                                    ident: 'postcss',
                                    plugins: () => [
                                        autoprefixer({
                                            browsers: [
                                                "> 1%",
                                                "last 2 versions"
                                            ]
                                        })
                                    ]
                                }
                            }
                        ]
                    },
                    {
                        test: /\.(png|jpe?g|gif)/,
                        loader: 'url-loader?limit=8000&name=images/[name].[ext]'
                    }
                ]
            }
        };

- The above setups are complete for development workflow
- Now, for production workflow
    - add "build": "rimraf dist && webpack --config webpack.prod.config.js --progress --profile --color"
    - run `npm i --save-dev rimraf`
    - create a new file named webpack.prod.config.js
    - add following code to the file

            const path = require('path');
            const autoprefixer = require('autoprefixer');
            const HtmlWebpackPlugin = require('html-webpack-plugin');
            const UglifyJSPlugin = require('uglifyjs-webpack-plugin');

            module.exports = {
                mode: 'development',
                devtool: 'cheap-module-source-map',
                entry: './src/index.js',
                output: {
                    path: path.resolve(__dirname, 'dist'),
                    filename: 'bundle.js',
                    chunkFilename: '[id].js',
                    publicPath: ''
                },
                resolve: {
                    extensions: ['.js', '.jsx']
                },
                module: {
                    rules: [
                        {
                            test: /\.js$/,
                            loader: 'babel-loader',
                            exclude: /node_modules/
                        },
                        {
                            test: /\.css$/,
                            exclude: /node_modules/,
                            use: [
                                { loader: 'style-loader' },
                                { 
                                    loader: 'css-loader',
                                    options: {
                                        importLoaders: 1,
                                        modules: true,
                                        localIdentName: '[name]__[local]__[hash:base64:5]'
                                    }
                                },
                                {
                                    loader: 'postcss-loader',
                                    options: {
                                        ident: 'postcss',
                                        plugins: () => [
                                            autoprefixer({
                                                browsers: [
                                                    "> 1%",
                                                    "last 2 versions"
                                                ]
                                            })
                                        ]
                                    }
                                }
                            ]
                        },
                        {
                            test: /\.(png|jpe?g|gif)/,
                            loader: 'url-loader?limit=8000&name=images/[name].[ext]'
                        }
                    ]
                },
                plugins: [
                    new HtmlWebpackPlugin({
                        template: __dirname + '/src/index.html',
                        filename: 'index.html',
                        inject: 'body'
                    }),
                ],
                optimization: {
                    minimizer: [
                        new UglifyJSPlugin({
                            sourceMap: true,
                            parallel: true,
                            cache: true
                        })
                    ]
                }
            };

> To support older browsers, follow following steps
> - run `npm i --save babel-polyfill`
> - add the following import to the top of index.js file
> - `import 'babel-polyfill'`
> - change the config of env babel preset in `.babelrc`

    "presets": [
        ["env", {
            "targets": {
                "browsers": [
                    "> 1%",
                    "last 2 versions"
                ]
            },
            "useBuiltIns": "entry"
        }],
        "stage-2",
        "react"
    ]