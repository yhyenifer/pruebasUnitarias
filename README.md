 

# Configuración Karma + Jasmine en Ionic V3

Antes de iniciar se debe garantizar que se cuente con la version de ***node 10***, y verificar que el proyecto corra sin problemas

    ionic serve
una vez el proyecto corre, lo bajamos y verificamos que contemos la version ***"typescript": "~2.9.2"*** en las dependencias de desarrollo, esto lo encontramos en el archivo ***package.json -> "devDependencies"****, seguido a esto hacemos lo siguiente:

 - en ***devDependencies*** agregamos las siguientes librerías
 
			    "@ionic/lab": "1.0.8",
				"@types/jasmine": "^3.5.2",
				"@types/jasminewd2": "^2.0.6",
				"@types/node": "^13.7.0",
				"angular2-template-loader": "^0.6.2",
				"html-loader": "^0.5.5",
				"jasmine": "^3.3.1",
				"jasmine-core": "^3.5.0",
				"jasmine-spec-reporter": "^4.2.1"
 - ejecutamos `npm i`
 - si todo instala correctamente, adicionamos las siguientes librerías, en caso de una falla, se de agregar una a una las librerías e ir ejecutando `npm i`

				"karma": "^4.4.1",
				"karma-chrome-launcher": "^2.2.0",
				"karma-cli": "^2.0.0",
				"karma-jasmine": "^2.0.1",
				"karma-jasmine-html-reporter": "^1.5.1",
				"karma-mocha-reporter": "^2.2.5",
				"karma-sourcemap-loader": "^0.3.7",
				"karma-webpack": "^3.0.5",
 - ejecutamos `npm i`   
 - nuevamente si todo instala correctamente, adicionamos las siguientes librerías, teniendo en cuanta el punto 3, en caso de fallas:

				"null-loader": "^0.1.1",
				"ts-loader": "^5.4.5",
				"ts-node": "^8.0.2",
				"webpack": "^4.29.3",
				"webpack-cli": "^3.2.3"
 - ejecutamos `npm i`
 - en el archivo package.json debemos tener algo como esto:
	 ![enter image description here](https://user-images.githubusercontent.com/10029769/86478152-739c3e80-bd0f-11ea-8306-e8e3d2714599.png)

 - dentro del archivo package.json debemos agregar la siguiente linea, dentro del apartado Scripts
 `"test": "karma start ./testing/karma.conf.js"` 		
 ![enter image description here](https://user-images.githubusercontent.com/10029769/86478455-e3122e00-bd0f-11ea-80d1-81d1459db9d5.png)
 9. creamos una carpeta al mismo nivel de la carpeta ***src***, dentro del proyecto, llamada ***testing*** y dentro de esta carpeta creamos los siguientes archivos:
		

	 - karma-test-shim.js
	 - karma.conf.js
	 - webpack.test.js

 10. dentro del archivo ***karma-test-shim.js***, agregamos en siguiente código:
 

    Error.stackTraceLimit = Infinity;
    require('core-js/es6');
    require('core-js/es7/reflect');
    require('zone.js/dist/zone');
    require('zone.js/dist/long-stack-trace-zone');
    require('zone.js/dist/proxy');
    require('zone.js/dist/sync-test');
    require('zone.js/dist/jasmine-patch');
    require('zone.js/dist/async-test');
    require('zone.js/dist/fake-async-test');
    
    var  appContext = require.context('../src', true, /\.spec\.ts/);
    
    appContext.keys().forEach(appContext);
    var  testing = require('@angular/core/testing');
    var  browser = require('@angular/platform-browser-dynamic/testing');
    testing.TestBed.initTestEnvironment(browser.BrowserDynamicTestingModule,
    browser.platformBrowserDynamicTesting()
    );
11. dentro del archivo  ***karma.conf.js***, adicionamos el 
  
   siguiente código: 
    
    var  webpackConfig = require('./webpack.test.js');
    module.exports = function(config) {
    var  _config = {
    basePath:  '',
    frameworks: ['jasmine'],
    files: [{ pattern:  './karma-test-shim.js', watched:  true }],
    preprocessors: {
    './karma-test-shim.js': ['webpack', 'sourcemap'],
    },
    webpack:  webpackConfig,
    webpackMiddleware: {
    stats:  'errors-only',
    },
    webpackServer: {
    noInfo:  true,
    },
    browserConsoleLogOptions: {
    level:  'log',
    format:  '%b %T: %m',
    terminal:  true,
    },
    reporters: ['mocha', 'kjhtml'],
    port:  9876,
    colors:  true,
    logLevel:  config.LOG_INFO,
    autoWatch:  true,
    browsers: ['Chrome'],
    singleRun:  false,
    };
    config.set(_config);
    };

12. dentro del archivo  ***webpack.test.js***, adicionamos el 

 siguiente código: 
    
    var  webpack = require('webpack');
    var  path = require('path');
    module.exports = {
    devtool:  'inline-source-map',
    resolve: {
    extensions: ['.ts', '.js'],
    },
    module: {
    rules: [{
    test: /\.ts$/,
    loaders: [{
    loader:  'ts-loader',
    },
    'angular2-template-loader',
    ],
    },
    {
    test: /\.html$/,
    loader:  'html-loader',
    },
    {
    test: /\.(png|jpeg|gif|svg|woff|woff2|ttf|eot|ico)$/,loader:
    'null-loader',},],},
    plugins: [
    new  webpack.ContextReplacementPlugin(
    // The (\\|\/) piece accounts for path separators in *nix and Windows
    /angular(\\|\/)core(\\|\/)(esm(\\|\/)src|src)(\\|\/)linker/,
    root('./src'), // location of your src
    {} // a map of your routes),],};
    function  root(localPath) {
    return  path.resolve(__dirname, localPath);
    }


13. ejecutamos el servidor de pruebas, para verificar su funcionamiento `npm test`, si este se ejecuta de manera correcta, debe salir esto en el navegador
![enter image description here](https://user-images.githubusercontent.com/10029769/86486612-583a2f00-bd21-11ea-8573-8a592f7a3035.png)

14. bajamos este servidor `ctrl + c` y ya podemos agregar archivos .spec.ts en cada unos de los componentes que necesitemos, para realizar las pruebas unitarias
