Создание базового приложения реакт

1. Создаём директорию приложения:
   ```
     mkdir 'project name'
   ```
2. Переходим в созданную директорию:
   ```
     cd 'project name'
   ```
3. Инициализируем проект средствами npm или yarn:
   ```
     npm/yarn init
   ```
   в процессе `npm` предложит заполнить базовую информацию. Всё это можно пропустить нажатием enter и поправить потом в файле `package.json`
   ```
     package name:
     version: (1.0.0) 
     description: 
     entry point: (index.js) 
     test command: 
     git repository: 
     keywords: 
     author: 
     license: (ISC)
   ```
4. Инициализациализируем git-репозиторий:
   ```
     git init
   ```
5. Создадим файл `.gitignore` следующего содержания:
   ```
      /node_modules
      /build
      /.vscode
      
      build.zip
      .npmrc
   ```
   Это позволит нам не включать в состав репозитория папки, имена которых присутствуют в файле.
6. (Не обязательный пункт) Добавим файл `.npmrc` с настройками репозиториев.
7. (Не обязательный пункт) Добавим файл `.yarnrc` со следующим содержимым:
   ```
     unsafe-disable-integrity-migration true
     strict-ssl false
     network-timeout 60000
   ```
8. Теперь установим зависимости, которые нам нужны для того чтобы собрать и запустить React-приложение:
   ```
     npm install react react-dom --save
   ```
   Транспилятор Babel преобразует код, соответствующий стандартам ECMAScript 2015+, в код, который будет работать и в новых, и в устаревших браузерах. Babel, благодаря применению пресетов, используется и для обработки JSX-кода
   ```
     npm install @babel/core @babel/preset-env @babel/preset-react --save-dev
   ```
   Вот как выглядит простая конфигурация Babel, предназначенная для подготовки к работе React-приложений. Эту конфигурацию можно добавить в файл .babelrc или в package.json:
   ```js
   {
     "presets": [
        "@babel/preset-env",
        "@babel/preset-react"
     ]
   }
   ```
   Бандлер Webpack отвечает за сборку проекта, формируя на основе кода проекта и кода его зависимостей единственный файл (бандл) приложения. При использовании таких техник оптимизации проектов, как разделение кода, в бандл приложения могут входить и несколько файлов.
   ```
     npm install webpack webpack-cli webpack-dev-server babel-loader css-loader style-loader html-webpack-plugin --save-dev
   ```
   В корне создаём папку config и создаём три файла `webpack.common.js`, `webpack.dev.js`, `webpack.build.js`, тем самым мы разделим конфигурации для наших нужд.
   - Простая конфигурация Webpack.common, выглядит так, как показано ниже:
   ```js
     const path = require('path');
     const HtmlWebPackPlugin = require('html-webpack-plugin');

     module.exports = {
       entry: path.resolve(__dirname, "../src", "index.jsx"),
       target: ["web", "es5"],
       output: {
         publicPath: '/',
         path: path.resolve(__dirname, 'build'),
         filename: 'bundle.js',
       },
       resolve: {
         extensions: ['.js', '.jsx', '.css']
       },
       module: {
         rules: [
           {
             test: /\.(js|jsx)$/,
             exclude: /node_modules/,
             use: {
               loader: 'babel-loader',
             },
           },
           {
             test: /\.css$/,
             use: [
               { loader: 'style-loader' },
               {
                 loader: 'css-loader',
                 options: { sourceMap: true }
               },
             ],
           },
           {
             test: /\.(woff|woff2|eot|ttf|otf)$/i,
             type: 'asset/resource',
             generator: {
               emit: true,
               filename: `static/fonts/[name][ext]`,
             },
           },
         ],
       },
       plugins: [
         new HtmlWebPackPlugin({
            template: './public/index.html',
         }),
       ],
     };
   ```
   - Простая конфигурация Webpack.dev, выглядит так, как показано ниже:
   ```
      const path = require('path');
      const webpack = require('webpack');
      const common = require("./webpack.common");

      module.exports = {
         ...common,
         mode: 'development',
         devtool: 'eval-source-map',
         devServer: {
            host: 'localhost',
            historyApiFallback: true,
            port: 3000,
            proxy: {
               "Тут ваши адреса апи": {
                  target: domain,
               }
            },
         },
      };
   ```
   - Простая конфигурация Webpack.build, выглядит так, как показано ниже:
   ```
      const webpack = require('webpack');
      const common = require("./webpack.common");

      module.exports = {
         ...common,
         mode: 'production',
      };
   ```
   Теперь создадим в корне папку public и добавим в проект шаблонный index.html файл, со следующим содержимым:
   ```html
      <!DOCTYPE html>
      <html lang="en">
         <head>
            <meta charset="UTF-8">
            <title>React Boilerplate</title>
         </head>
         <body>
            <div id="root"></div>
         </body>
      </html>
   ```
   Добавляем в проект TypeScript
   ```
   npm install typescript @types/react @types/react-dom --save-dev
   ```
   Исправляем правила в конфиге webpack'a, необходимо включить файлы .ts и .tsx в загрузчики, поправить entry и extensions
   ```js
      const path = require('path');
      const HtmlWebPackPlugin = require('html-webpack-plugin');

      module.exports = {
         entry: path.resolve(__dirname, "../src", "index.tsx"),
         target: ["web", "es5"],
         output: {
            publicPath: '/',
            path: path.resolve(__dirname, 'build'),
            filename: 'bundle.js',
         },
         resolve: {
            extensions: ['.ts', '.tsx', '.js', '.jsx', '.css']
         },
         module: {
            rules: [
               {
                  test: /\.(j|t)sx?$/,
                  exclude: /node_modules/,
                  use: {
                     loader: 'babel-loader',
                  },
               },
               {
                  test: /\.css$/,
                  use: [
                     { loader: 'style-loader' },
                     {
                        loader: 'css-loader',
                        options: { sourceMap: true }
                     },
                  ],
               },
               {
                  test: /\.(woff|woff2|eot|ttf|otf)$/i,
                  type: 'asset/resource',
                  generator: {
                     emit: true,
                     filename: `static/fonts/[name][ext]`,
                  },
               },
            ],
         },
         plugins: [
            new HtmlWebPackPlugin({
               template: './public/index.html',
            }),
         ],
      };
   ```
   Добавляем в проект библиотеку Admiral
   ```
   npm install @admiral-ds/icons @admiral-ds/react-ui styled-components --save
   npm install @svgr/webpack @types/styled-components url-loader --save-dev
   ```
   Делаем изменения в конфиге webpack для того чтобы Admiral работал правильно(Добавляем загрузчик для svg)
   ```js
      const path = require('path');
      const HtmlWebPackPlugin = require('html-webpack-plugin');

      module.exports = {
         entry: path.resolve(__dirname, "../src", "index.tsx"),
         target: ["web", "es5"],
         output: {
            publicPath: '/',
            path: path.resolve(__dirname, 'build'),
            filename: 'bundle.js',
         },
         resolve: {
            extensions: ['.ts', '.tsx', '.js', '.jsx', '.css']
         },
         module: {
            rules: [
               {
                  test: /\.(j|t)sx?$/,
                  exclude: /node_modules/,
                  use: {
                     loader: 'babel-loader',
                  },
               },
               {
                  test: /\.css$/,
                  use: [
                     { loader: 'style-loader' },
                     {
                        loader: 'css-loader',
                        options: { sourceMap: true }
                     },
                  ],
               },
               {
                  test: /\.svg$/,
                  use: ["@svgr/webpack", "url-loader"],
                  type: "javascript/auto",
                  issuer: /\.(js|ts)x?$/,
               },
               {
                  test: /\.(woff|woff2|eot|ttf|otf)$/i,
                  type: 'asset/resource',
                  generator: {
                     emit: true,
                     filename: `static/fonts/[name][ext]`,
                  },
               },
            ],
         },
         plugins: [
            new HtmlWebPackPlugin({
               template: './public/index.html',
            }),
         ],
      };
   ```
