use strict';

const _ = require('lodash');

const gulp = require('gulp');

const argv = require('yargs').argv;

const opens = require('opn');

const header = require('gulp-header');

const connect = require('gulp-connect');

const creative = require('./package.json');

const uglify = require('gulp-uglify');

const gulpClean = require('gulp-clean');

const webpackStream = require('webpack-stream');

const webpackConfig = require('./webpack.conf');

const inject = require('gulp-inject');

const rename = require('gulp-rename');

const KarmaServer = require('karma').Server;

const karmaConfMaker = require('./karma.conf.maker');

const execa = require('execa');

const path = require('path');

const dateString = 'Updated : ' + (new Date()).toISOString().substring(0, 10);

const banner = '/* <%= creative.name %> v<%= creative.version %>\n' + dateString + ' */\n';

const port = 9990;

function clean() {

  return gulp.src(['dist/', 'build/'], {

    read: false,

    allowEmpty: true

  })

    .pipe(gulpClean());

}

function buildDev() {

  return gulp.src(['src/creative.js'])

    .pipe(webpackStream(webpackConfig))

    .pipe(gulp.dest('build'));

}

function buildNativeDev() {

  var cloned = _.cloneDeep(webpackConfig);

  cloned.output.filename = 'native-trk.js';

  return gulp.src(['src/nativeTrackers.js'])

    .pipe(webpackStream(cloned))

    .pipe(gulp.dest('build'));

}

function buildNativeRenderDev() {

  var cloned = _.cloneDeep(webpackConfig);

  cloned.output.filename = 'native-render.js';

  return gulp.src(['src/nativeRender.js'])

    .pipe(webpackStream(cloned))

    .pipe(gulp.dest('build'));

}

function buildCookieSync() {

  let cloned = _.cloneDeep(webpackConfig);

  delete cloned.devtool;

  let target = gulp.src('resources/load-cookie.html');

  let sources = gulp.src(['src/cookieSync.js'])

    .pipe(webpackStream(cloned))

    .pipe(uglify());

  return target.pipe(inject(sources, {

    starttag: '// cookie-sync start',

    endtag: '// end',

    transform: function (filePath, file) {

      return file.contents.toString('utf8')

    }

  }))

    .pipe(gulp.dest('dist'));

}

function buildCookieSyncWithConsent() {

  let cloned = _.cloneDeep(webpackConfig);

  delete cloned.devtool;

  let target = gulp.src('resources/load-cookie-with-consent.html');

  let sources = gulp.src(['src/cookieSyncWithConsent.js'])

    .pipe(webpackStream(cloned))

    .pipe(uglify());

  return target.pipe(inject(sources, {

    starttag: '// cookie-sync start',

    endtag: '// end',

    transform: function (filePath, file) {

      return file.contents.toString('utf8')

    }

  }))

    .pipe(gulp.dest('dist'));

}

function buildUidDev() {

  var cloned = _.cloneDeep(webpackConfig);

  delete cloned.devtool;

  cloned.output.filename = 'uid.js';

  return gulp.src(['src/ssp-userids/uid.js'])

    .pipe(webpackStream(cloned))

    .pipe(gulp.dest('build'));

}

function buildProd() {

  let cloned = _.cloneDeep(webpackConfig);

  delete cloned.devtool;

  return gulp.src(['src/creative.js'])

    .pipe(webpackStream(cloned))

    .pipe(rename({ extname: '.max.js' }))

    .pipe(gulp.dest('dist'))

    .pipe(uglify())

    .pipe(header(banner, { creative: creative }))

    .pipe(rename({

      basename: 'creative',

      extname: '.js'

    }))

    .pipe(gulp.dest('dist'));

}

function buildNative() {

  var cloned = _.cloneDeep(webpackConfig);

  delete cloned.devtool;

  cloned.output.filename = 'native-trk.js';

  return gulp.src(['src/nativeTrackers.js'])

    .pipe(webpackStream(cloned))

    .pipe(uglify())

    .pipe(header('/* v<%= creative.version %>\n' + dateString + ' */\n', { creative: creative }))

    .pipe(gulp.dest('dist'));

}

function buildNativeRender() {

  var cloned = _.cloneDeep(webpackConfig);

  delete cloned.devtool;

  cloned.output.filename = 'native-render.js';

  return gulp.src(['src/nativeRender.js'])

    .pipe(webpackStream(cloned))

    .pipe(uglify())

    .pipe(header('/* v<%= creative.version %>\n' + dateString + ' */\n', { creative: creative }))

    .pipe(gulp.dest('dist'));

}

function buildUid() {

  var cloned = _.cloneDeep(webpackConfig);

  delete cloned.devtool;

  cloned.output.filename = 'uid.js';

  return gulp.src(['src/ssp-userids/uid.js'])

  .pipe(webpackStream(cloned))

    .pipe(uglify())

    .pipe(header('/* v<%= creative.version %>\n' + dateString + ' */\n', { creative: creative }))

    .pipe(gulp.dest('dist'));

}

// Run the unit tests.

//

// By default, this runs in headless chrome.

//

// If --watch is given, the task will open the karma debug window

// If --browserstack is given, it will run the full suite of currently supported browsers.

// If --e2e is given, it will run test defined in ./test/e2e/specs in browserstack

function test(done) {

  if (argv.e2e) {

    let wdioCmd = path.join(__dirname, 'node_modules/.bin/wdio');

    let wdioConf = path.join(__dirname, 'wdio.conf.js');

    let wdioOpts = [

      wdioConf

    ];

    return execa(wdioCmd, wdioOpts, { stdio: 'inherit' });

  } else {

    let karmaConf = karmaConfMaker(false, argv.browserstack, argv.watch);

    new KarmaServer(karmaConf, newKarmaCallback(done)).start();

  }

}

function newKarmaCallback(done) {

  return function(exitCode) {

    if (exitCode) {

      done(new Error('Karma tests failed with exit code' + exitCode));

      if (argv.browserstack) {

        process.exit(exitCode);

      }

    } else {

      done();

      if (argv.browserstack) {

        process.exit(exitCode);

      }

    }

  } 

}

function setupE2E(done) {

  argv.e2e = true;

  done();

}

gulp.task('test', gulp.series(clean, test));

gulp.task('e2e-test', gulp.series(clean, setupE2E, gulp.parallel(buildDev, buildCookieSync, buildCookieSyncWithConsent, buildNativeDev, buildNativeRenderDev, buildUidDev, watch), test));

function watch(done) {

  const mainWatcher = gulp.watch([

    'src/**/*.js',

    'test/**/*.js'

  ]);

  connect.server({

    https: argv.https,

    livereload: true,

    port,

    root: './'

  });

  mainWatcher.on('all', gulp.series(clean, gulp.parallel(buildDev, buildNativeDev, buildNativeRenderDev, buildCookieSync, buildCookieSyncWithConsent, buildUidDev), test));

  done();

}

function openWebPage() {

  return opens(`${(argv.https) ? 'https' : 'http'}://localhost:${port}`);

}

gulp.task('serve', gulp.series(clean, gulp.parallel(buildDev, buildNativeDev, buildNativeRenderDev, buildCookieSync, buildCookieSyncWithConsent, buildUidDev, watch, test), openWebPage));

gulp.task('build', gulp.parallel(buildProd, buildCookieSync, buildCookieSyncWithConsent, buildNative, buildNativeRender, buildUid));

gulp.task('test-coverage', (done) => {

  new KarmaServer(karmaConfMaker(true, false, false), newKarmaCallback(done)).start();

});

gulp.task('view-coverage', (done) => {

  const coveragePort = 1999;

  const localhost = (argv.host) ? argv.host : 'localhost';

  connect.server({

    port: coveragePort,

    root: 'build/coverage/karma_html',

    livereload: false

  });

  opens('http://' + localhost + ':' + coveragePort);

  done();

});

;

const _ = require('lodash');

const webpackConf = require('./webpack.conf');

const karmaConstants = require('karma').constants;

const path = require('path');

function setBrowsers(karmaConf, browserstack, watchMode) {

  if (browserstack) {

    karmaConf.browserStack = {

      username: process.env.BROWSERSTACK_USERNAME,

      accessKey: process.env.BROWSERSTACK_ACCESS_KEY,

      build: 'PUC Unit Tests ' + new Date().toLocaleString()

    }

    karmaConf.customLaunchers = require('./browsers.json')

    karmaConf.browsers = Object.keys(karmaConf.customLaunchers);

  } else if (watchMode) {

    karmaConf.browsers = ['Chrome'];

  }

}

function setReporters(karmaConf, codeCoverage, browserstack) {

  // In browserstack, the default 'progress' reporter floods the logs.

  // The karma-spec-reporter reports failures more concisely

  if (browserstack) {

    karmaConf.reporters = ['spec'];

    karmaConf.specReporter = {

      maxLogLines: 100,

      suppressErrorSummary: false,

      suppressSkipped: false,

      suppressPassed: true

    };

  }

  

  if (codeCoverage) {

    karmaConf.reporters.push('coverage-istanbul');

    karmaConf.coverageIstanbulReporter = {

      reports: ['html', 'lcovonly', 'text-summary'],

      dir: path.join(__dirname, 'build', 'coverage'),

      'report-config': {

        html: {

          subdir: 'karma_html',

          urlFriendlyName: true, // simply replaces spaces with _ for files/dirs

        }

      }

    }  

  }

}

function newWebpackConfig(codeCoverage) {

  const webpackConfig = _.cloneDeep(webpackConf);

  webpackConfig.devtool = 'inline-source-map';

  if (codeCoverage) {

    webpackConfig.module.rules.push({

      test: /\.js$/,

      enforce: 'post',

      use: {

        loader: 'istanbul-instrumenter-loader',

        options: { esModules: true }

      },

      exclude: /(node_modules)|(test)|(resources)|(template)|(testpages)/

    });

  }

  return webpackConfig;

}

module.exports = function(codeCoverage, browserstack, watchMode) {

  const webpackConfig = newWebpackConfig(codeCoverage);

  const files = ['test/test_index.js'];

  if (watchMode) {

    files.push('test/helpers/karma-init.js');

  }

  const config = {

    // base path that will be used to resolve all patterns (eg. files, exclude)

    basePath: './',

    plugins: [

      'karma-mocha',

      'karma-chrome-launcher',

      'karma-webpack',

      'karma-chai',

      'karma-sourcemap-loader',

      'karma-sinon',

      'karma-coverage',

      'karma-browserstack-launcher',

      'karma-spec-reporter',

      'karma-mocha-reporter',

      'karma-coverage-istanbul-reporter'

    ],

    webpack: webpackConfig,

    webpackMiddleware: {

      logLevel: 'error'

    },

    // frameworks to use

    // available frameworks: https://npmjs.org/browse/keyword/karma-adapter

    frameworks: ['mocha', 'chai', 'sinon'],

    // list of files / patterns to load in the browser

    files: files,

    // list of files / patterns to exclude

    exclude: [

    ],

    // preprocess matching files before serving them to the browser

    // available preprocessors: https://npmjs.org/browse/keyword/karma-preprocessor

    preprocessors: {

      'test/test_index.js': [ 'webpack', 'sourcemap' ]

    },

    // test results reporter to use

    // possible values: 'dots', 'progress'

    // available reporters: https://npmjs.org/browse/keyword/karma-reporter

    reporters: ['mocha'],

    mochaReporter: {

      showDiff: true,

      output: 'minimal'

    },

    // web server port

    port: 9876,

    // enable / disable colors in the output (reporters and logs)

    colors: true,

    // level of logging

    // possible values: config.LOG_DISABLE || config.LOG_ERROR || config.LOG_WARN || config.LOG_INFO || config.LOG_DEBUG

    logLevel: karmaConstants.LOG_INFO,

    // enable / disable watching file and executing tests whenever any file changes

    autoWatch: true,

    // start these browsers

    // available browser launchers: https://npmjs.org/browse/keyword/karma-launcher

    browsers: ['ChromeHeadless'],

    // Continuous Integration mode

    // if true, Karma captures browsers, runs the tests and exits

    singleRun: !watchMode,

    // Concurrency level

    // how many browser should be started simultaneous

    concurrency: Infinity,

    browserDisconnectTimeout: 100000,

    browserDisconnectTolerance: 1, // default 0

    browserNoActivityTimeout: 4 * 60 * 1000, // default 10000

    captureTimeout: 4 * 60 * 1000, // default 60000

  }

  setReporters(config, codeCoverage, browserstack);

  setBrowsers(config, browserstack, watchMode);

  return config;

}

;

const browsers = require('./browsers.json');

function getCapabilities() {

  function getPlatform(os) {

    const platformMap = {

      'Windows': 'WINDOWS',

      'OS X': 'MAC',

    }

    return platformMap[os];

  }

  let capabilities = []

  Object.keys(browsers).forEach(key => {

    let browser = browsers[key];

    capabilities.push({

      browserName: browser.browser,

      platform: getPlatform(browser.os),

      version: browser.browser_version,

      acceptSslCerts: true,

      build: 'UC ' + new Date().toLocaleString()

    });

  });

  return capabilities;

}

exports.config = {

  specs: [

    './test/e2e/specs/*.js'

  ],

  services: ['browserstack'],

  user: process.env.BROWSERSTACK_USERNAME,

  key: process.env.BROWSERSTACK_ACCESS_KEY,

  browserstackLocal: true,

  // Do not increase this, since we have only 5 parallel tests in browserstack account

  maxInstances: 5,

  capabilities: getCapabilities(),

  logLevel: 'silent',               // Level of logging verbosity: silent | verbose | command | data | result | error

  coloredLogs: true,

  waitforTimeout: 90000,            // Default timeout for all waitFor* commands.

  connectionRetryTimeout: 90000,    // Default timeout in milliseconds for request if Selenium Grid doesn't send response

  connectionRetryCount: 3,          // Default request retries count

  framework: 'mocha',

  mochaOpts: {

    ui: 'bdd',

    timeout: 90000,

    compilers: ['@babel/register'],

  },

  // if you see error, update this to spec reporter and logLevel above to get detailed report.

  reporters: ['concise']

};;

var creative = require('./package.json');

var StringReplacePlugin = require('string-replace-webpack-plugin');

var path = require('path');

module.exports = {

  devtool: 'source-map',

  resolve: {

    modules: [

      path.resolve('.'),

      'node_modules'

    ],

  },

  output: {

    filename: 'creative.js',

  },

  module: {

    rules: [

      {

        test: /\.js$/,

        exclude: path.resolve('./node_modules'), // required to prevent loader from choking non-Prebid.js node_modules

        use: [

          {

            loader: 'babel-loader',

            options: {

              presets: ['@babel/preset-env']

            }

          }

        ]

      },

      {

        test: /\.js$/,

        include: /(src|test|testpages)/,

        loader: StringReplacePlugin.replace({

          replacements: [

            {

              pattern: /\$\$PREBID_GLOBAL\$\$/g,

              replacement: function (match, p1, offset, string) {

                return creative.globalVarName;

              }

            }

          ]

        })

      }

    ]

  }

};

//# sourceMappingURL=/sm/9ff1f7b1bf333614ca21706f01c3d8c7d6b6c32a1667f3627487d1929f793565.map
