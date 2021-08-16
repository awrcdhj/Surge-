fterWrite:u,autoFix:!0,beforeEnqueue:u,beforeWriteToken:function(t){return t},beforeWrite:function(t){return t},done:u,error:function(t){throw new Error(t.msg)},releaseAsync:!1},l=0,p=[],f=null;function d(){var t=p.shift();if(t){var e=i.last(t);e.afterDequeue(),t.stream=function(t,e,n){(f=new c.default(t,n)).id=l++,f.name=n.name||f.id,h.streams[f.name]=f;var r=t.ownerDocument,o={close:r.close,open:r.open,write:r.write,writeln:r.writeln};function i(t){t=n.beforeWrite(t),f.write(t),n.afterWrite(t)}s(r,{close:u,open:u,write:function(){for(var t=arguments.length,e=Array(t),r=0;r<t;r++)e[r]=arguments[r];return i(e.join(""))},writeln:function(){for(var t=arguments.length,e=Array(t),r=0;r<t;r++)e[r]=arguments[r];return i(e.join("")+"\n")}});var a=f.win.onerror||u;return f.win.onerror=function(t,e,r){n.error({msg:t+" - "+e+": "+r}),a.apply(f.win,[t,e,r])},f.write(e,function(){s(r,o),f.win.onerror=a,n.done(),f=null,d()}),f}.apply(void 0,t),e.afterStreamStart()}}function h(t,e,r){if(i.isFunction(r))r={done:r};else if("clear"===r)return p=[],f=null,void(l=0);r=i.defaults(r,a);var n=[t=/^#/.test(t)?window.document.getElementById(t.substr(1)):t.jquery?t[0]:t,e,r];return t.postscribe={cancel:function(){n.stream?n.stream.abort():n[1]=u}},r.beforeEnqueue(n),p.push(n),f||d(),t.postscribe}s(h,{streams:{},queue:p,WriteStream:c.default})},function(t,e,r){"use strict";e.__esModule=!0;var n,s=Object.assign||function(t){for(var e=1;e<arguments.length;e++){var r=arguments[e];for(var n in r)Object.prototype.hasOwnProperty.call(r,n)&&(t[n]=r[n])}return t},o=r(3),i=(n=o)&&n.__esModule?n:{default:n},a=function(t){{if(t&&t.__esModule)return t;var e={};if(null!=t)for(var r in t)Object.prototype.hasOwnProperty.call(t,r)&&(e[r]=t[r]);return e.default=t,e}}(r(4));var l="data-ps-",p="ps-style",f="ps-script";function c(t,e){var r=l+e,n=t.getAttribute(r);return a.existy(n)?String(n):n}function u(t,e,r){var n=2<arguments.length&&void 0!==r?r:null,o=l+e;a.existy(n)&&""!==n?t.setAttribute(o,n):t.removeAttribute(o)}var d=(h.prototype.write=function(){var t;for((t=this.writeQueue).push.apply(t,arguments);!this.deferredRemote&&this.writeQueue.length;){var e=this.writeQueue.shift();a.isFunction(e)?this._callFunction(e):this._writeImpl(e)}},h.prototype._callFunction=function(t){var e={type:"function",value:t.name||t.toString()};this._onScriptStart(e),t.call(this.win,this.doc),this._onScriptDone(e)},h.prototype._writeImpl=function(t){this.parser.append(t);for(var e=void 0,r=void 0,n=void 0,o=[];(e=this.parser.readToken())&&!(r=a.isScript(e))&&!(n=a.isStyle(e));)(e=this.options.beforeWriteToken(e))&&o.push(e);0<o.length&&this._writeStaticTokens(o),r&&this._handleScriptToken(e),n&&this._handleStyleToken(e)},h.prototype._writeStaticTokens=function(t){var e=this._buildChunk(t);return e.actual?(e.html=this.proxyHistory+e.actual,this.proxyHistory+=e.proxy,this.proxyRoot.innerHTML=e.html,this._walkChunk(),e):null},h.prototype._buildChunk=function(t){for(var e=this.actuals.length,r=[],n=[],o=[],i=t.length,a=0;a<i;a++){var s=t[a],c=s.toString();if(r.push(c),s.attrs){if(!/^noscript$/i.test(s.tagName)){var u=e++;n.push(c.replace(/(\/?>)/," "+l+"id="+u+" $1")),s.attrs.id!==f&&s.attrs.id!==p&&o.push("atomicTag"===s.type?"":"<"+s.tagName+" "+l+"proxyof="+u+(s.unary?" />":">"))}}else n.push(c),o.push("endTag"===s.type?c:"")}return{tokens:t,raw:r.join(""),actual:n.join(""),proxy:o.join("")}},h.prototype._walkChunk=function(){for(var t=void 0,e=[this.proxyRoot];a.existy(t=e.shift());){var r=1===t.nodeType;if(!r||!c(t,"proxyof")){r&&u(this.actuals[c(t,"id")]=t,"id");var n=t.parentNode&&c(t.parentNode,"proxyof");n&&this.actuals[n].appendChild(t)}e.unshift.apply(e,a.toArray(t.childNodes))}},h.prototype._handleScriptToken=function(t){var e=this,r=this.parser.clear();r&&this.writeQueue.unshift(r),t.src=t.attrs.src||t.attrs.SRC,(t=this.options.beforeWriteToken(t))&&(t.src&&this.scriptStack.length?this.deferredRemote=t:this._onScriptStart(t),this._writeScriptToken(t,function(){e._onScriptDone(t)}))},h.prototype._handleStyleToken=function(t){var e=this.parser.clear();e&&this.writeQueue.unshift(e),t.type=t.attrs.type||t.attrs.TYPE||"text/css",(t=this.options.beforeWriteToken(t))&&this._writeStyleToken(t),e&&this.write()},h.prototype._writeStyleToken=function(t){var e=this._buildStyle(t);this._insertCursor(e,p),t.content&&(e.styleSheet&&!e.sheet?e.styleSheet.cssText=t.content:e.appendChild(this.doc.createTextNode(t.content)))},h.prototype._buildStyle=function(t){var r=this.doc.createElement(t.tagName);return r.setAttribute("type",t.type),a.eachKey(t.attrs,function(t,e){r.setAttribute(t,e)}),r},h.prototype._insertCursor=function(t,e){this._writeImpl('<span id="'+e+'"/>');var r=this.doc.getElementById(e);r&&r.parentNode.replaceChild(t,r)},h.prototype._onScriptStart=function(t){t.outerWrites=this.writeQueue,this.writeQueue=[],this.scriptStack.unshift(t)},h.prototype._onScriptDone=function(t){t===this.scriptStack[0]?(this.scriptStack.shift(),this.write.apply(this,t.outerWrites),!this.scriptStack.length&&this.deferredRemote&&(this._onScriptStart(this.deferredRemote),this.deferredRemote=null)):this.options.error({msg:"Bad script nesting or script finished twice"})},h.prototype._writeScriptToken=function(t,e){var r=this._buildScript(t),n=this._shouldRelease(r),o=this.options.afterAsync;t.src&&(r.src=t.src,this._scriptLoadHandler(r,n?o:function(){e(),o()}));try{this._insertCursor(r,f),r.src&&!n||e()}catch(t){this.options.error(t),e()}},h.prototype._buildScript=function(t){var r=this.doc.createElement(t.tagName);return a.eachKey(t.attrs,function(t,e){r.setAttribute(t,e)}),t.content&&(r.text=t.content),r},h.prototype._scriptLoadHandler=function(e,r){function n(){e=e.onload=e.onreadystatechange=e.onerror=null}var o=this.options.error;function t(){n(),null!=r&&r(),r=null}function i(t){n(),o(t),null!=r&&r(),r=null}function a(t,e){var r=t["on"+e];null!=r&&(t["_on"+e]=r)}a(e,"load"),a(e,"error"),s(e,{onload:function(){if(e._onload)try{e._onload.apply(this,Array.prototype.slice.call(arguments,0))}catch(t){i({msg:"onload handler failed "+t+" @ "+e.src})}t()},onerror:function(){if(e._onerror)try{e._onerror.apply(this,Array.prototype.slice.call(arguments,0))}catch(t){return void i({msg:"onerror handler failed "+t+" @ "+e.src})}i({msg:"remote script failed "+e.src})},onreadystatechange:function(){/^(loaded|complete)$/.test(e.readyState)&&t()}})},h.prototype._shouldRelease=function(t){return!/^script$/i.test(t.nodeName)||!!(this.options.releaseAsync&&t.src&&t.hasAttribute("async"))},h);function h(t){var e=1<arguments.length&&void 0!==arguments[1]?arguments[1]:{};!function(t,e){if(!(t instanceof e))throw new TypeError("Cannot call a class as a function")}(this,h),this.root=t,this.options=e,this.doc=t.ownerDocument,this.win=this.doc.defaultView||this.doc.parentWindow,this.parser=new i.default("",{autoFix:e.autoFix}),this.actuals=[t],this.proxyHistory="",this.proxyRoot=this.doc.createElement(t.nodeName),this.scriptStack=[],this.writeQueue=[],u(this.proxyRoot,"proxyof",0)}e.default=d},function(t,e,r){var n;n=function(){return o={},r.m=n=[function(t,e,r){"use strict";var n,o=r(1),i=(n=o)&&n.__esModule?n:{default:n};t.exports=i.default},function(t,e,r){"use strict";e.__esModule=!0;var n,a=u(r(2)),o=u(r(3)),i=r(6),s=(n=i)&&n.__esModule?n:{default:n},c=r(5);function u(t){if(t&&t.__esModule)return t;var e={};if(null!=t)for(var r in t)Object.prototype.hasOwnProperty.call(t,r)&&(e[r]=t[r]);return e.default=t,e}var l={comment:/^<!--/,endTag:/^<\//,atomicTag:/^<\s*(script|style|noscript|iframe|textarea)[\s\/>]/i,startTag:/^</,chars:/^[^<]/},p=(f.prototype.append=function(t){this.stream+=t},f.prototype.prepend=function(t){this.stream=t+this.stream},f.prototype._readTokenImpl=function(){var t=this._peekTokenImpl();if(t)return this.stream=this.stream.slice(t.length),t},f.prototype._peekTokenImpl=function(){for(var t in l)if(l.hasOwnProperty(t)&&l[t].test(this.stream)){var e=o[t](this.stream);if(e)return"startTag"===e.type&&/script|style/i.test(e.tagName)?null:(e.text=this.stream.substr(0,e.length),e)}},f.prototype.peekToken=function(){return this._peekToken()},f.prototype.readToken=function(){return this._readToken()},f.prototype.readTokens=function(t){for(var e=void 0;e=this.readToken();)if(t[e.type]&&!1===t[e.type](e))return},f.prototype.clear=function(){var t=this.stream;return this.stream="",t},f.prototype.rest=function(){return this.stream},f);function f(){var t=this,e=0<arguments.length&&void 0!==arguments[0]?arguments[0]:"",r=1<arguments.length&&void 0!==arguments[1]?arguments[1]:{};!function(t,e){if(!(t instanceof e))throw new TypeError("Cannot call a class as a function")}(this,f),this.stream=e;var n=!1,o={};for(var i in a)a.hasOwnProperty(i)&&(r.autoFix&&(o[i+"Fix"]=!0),n=n||o[i+"Fix"]);n?(this._readToken=(0,s.default)(this,o,function(){return t._readTokenImpl()}),this._peekToken=(0,s.default)(this,o,function(){return t._peekTokenImpl()})):(this._readToken=this._readTokenImpl,this._peekToken=this._peekTokenImpl)}for(var d in(e.default=p).tokenToString=function(t){return t.toString()},p.escapeAttributes=function(t){var e={};for(var r in t)t.hasOwnProperty(r)&&(e[r]=(0,c.escapeQuotes)(t[r],null));return e},p.supports=a)a.hasOwnProperty(d)&&(p.browserHasFlaw=p.browserHasFlaw||!a[d]&&d)},function(t,e){"use strict";var r=!(e.__esModule=!0),n=!1,o=window.document.createElement("div");try{var i="<P><I></P></I>";o.innerHTML=i,e.tagSoup=r=o.innerHTML!==i}catch(t){e.tagSoup=r=!1}try{o.innerHTML="<P><i><P></P></i></P>",e.selfClose=n=2===o.childNodes.length}catch(t){e.selfClose=n=!1}o=null,e.tagSoup=r,e.selfClose=n},function(t,e,r){"use strict";e.__esModule=!0;var a="function"==typeof Symbol&&"symbol"==typeof Symbol.iterator?function(t){return typeof t}:function(t){return t&&"function"==typeof Symbol&&t.constructor===Symbol&&t!==Symbol.prototype?"symbol":typeof t};e.comment=function(t){var e=t.indexOf("--\x3e");if(0<=e)return new s.CommentToken(t.substr(4,e-1),e+3)},e.chars=function(t){var e=t.indexOf("<");return new s.CharsToken(0<=e?e:t.length)},e.startTag=o,e.atomicTag=function(t){var e=o(t);if(e){var r=t.slice(e.length);if(r.match(new RegExp("</\\s*"+e.tagName+"\\s*>","i"))){var n=r.match(new RegExp("([\\s\\S]*?)</\\s*"+e.tagName+"\\s*>","i"));if(n)return new s.AtomicTagToken(e.tagName,n[0].length+e.length,e.attrs,e.booleanAttrs,n[1])}}},e.endTag=function(t){var e=t.match(c.endTag);if(e)return new s.EndTagToken(e[1],e[0].length)};var s=r(4),c={startTag:/^<([\-A-Za-z0-9_]+)((?:\s+[\w\-]+(?:\s*=?\s*(?:(?:"[^"]*")|(?:'[^']*')|[^>\s]+))?)*)\s*(\/?)>/,endTag:/^<\/([\-A-Za-z0-9_]+)[^>]*>/,attr:/(?:([\-A-Za-z0-9_]+)\s*=\s*(?:(?:"((?:\\.|[^"])*)")|(?:'((?:\\.|[^'])*)')|([^>\s]+)))|(?:([\-A-Za-z0-9_]+)(\s|$)+)/g,fillAttr:/^(checked|compact|declare|defer|disabled|ismap|multiple|nohref|noresize|noshade|nowrap|readonly|selected)$/i};function o(t){var r,n,o;if(-1!==t.indexOf(">")){var e=t.match(c.startTag);if(e){var i=(r={},n={},o=e[2],e[2].replace(c.attr,function(t,e){arguments[2]||arguments[3]||arguments[4]||arguments[5]?arguments[5]?(r[arguments[5]]="",n[arguments[5]]=!0):r[e]=arguments[2]||arguments[3]||arguments[4]||c.fillAttr.test(e)&&e||"":r[e]="",o=o.replace(t,"")}),{v:new s.StartTagToken(e[1],e[0].length,r,n,!!e[3],o.replace(/^[\s\uFEFF\xA0]+|[\s\uFEFF\xA0]+$/g,""))});if("object"===(void 0===i?"undefined":a(i)))return i.v}}}},function(t,e,r){"use strict";e.__esModule=!0,e.EndTagToken=e.AtomicTagToken=e.StartTagToken=e.TagToken=e.CharsToken=e.CommentToken=e.Token=void 0;var a=r(5);function s(t,e){if(!(t instanceof e))throw new TypeError("Cannot call a class as a function")}e.Token=function t(e,r){s(this,t),this.type=e,this.length=r,this.text=""},e.CommentToken=(n.prototype.toString=function(){return"\x3c!--"+this.content},n);function n(t,e){s(this,n),this.type="comment",this.length=e||(t?t.length:0),this.text="",this.content=t}e.CharsToken=(o.prototype.toString=function(){return this.text},o);function o(t){s(this,o),this.type="chars",this.length=t,this.text=""}var i=e.TagToken=(c.formatTag=function(t,e){var r=1<arguments.length&&void 0!==e?e:null,n="<"+t.tagName;for(var o in t.attrs)if(t.attrs.hasOwnProperty(o)){n+=" "+o;var i=t.attrs[o];void 0!==t.booleanAttrs&&void 0!==t.booleanAttrs[o]||(n+='="'+(0,a.escapeQuotes)(i)+'"')}return t.rest&&(n+=" "+t.rest),t.unary&&!t.html5Unary?n+="/>":n+=">",null!=r&&(n+=r+"</"+t.tagName+">"),n},c);function c(t,e,r,n,o){s(this,c),this.type=t,this.length=r,this.text="",this.tagName=e,this.attrs=n,this.booleanAttrs=o,this.unary=!1,this.html5Unary=!1}e.StartTagToken=(u.prototype.toString=function(){return i.formatTag(this)},u);function u(t,e,r,n,o,i){s(this,u),this.type="startTag",this.length=e,this.text="",this.tagName=t,this.attrs=r,this.booleanAttrs=n,this.html5Unary=!1,this.unary=o,this.rest=i}e.AtomicTagToken=(l.prototype.toString=function(){return i.formatTag(this,this.content)},l);function l(t,e,r,n,o){s(this,l),this.type="atomicTag",this.length=e,this.text="",this.tagName=t,this.attrs=r,this.booleanAttrs=n,this.unary=!1,this.html5Unary=!1,this.content=o}e.EndTagToken=(p.prototype.toString=function(){return"</"+this.tagName+">"},p);function p(t,e){s(this,p),this.type="endTag",this.length=e,this.text="",this.tagName=t}},function(t,e){"use strict";e.__esModule=!0,e.escapeQuotes=function(t){var e=1<arguments.length&&void 0!==arguments[1]?arguments[1]:"";return t?t.replace(/([^"]*)"/g,function(t,e){return/\\/.test(e)?e+'"':e+'\\"'}):e}},function(t,e){"use strict";e.__esModule=!0,e.default=function(r,n,e){var o=function(){var t=[];return t.last=function(){return this[this.length-1]},t.lastTagNameEq=function(t){var e=this.last();return e&&e.tagName&&e.tagName.toUpperCase()===t.toUpperCase()},t.containsTagName=function(t){for(var e,r=0;e=this[r];r++)if(e.tagName===t)return!0;return!1},t}(),i={startTag:function(t){var e=t.tagName;"TR"===e.toUpperCase()&&o.lastTagNameEq("TABLE")?(r.prepend("<TBODY>"),a()):n.selfCloseFix&&s.test(e)&&o.containsTagName(e)?o.lastTagNameEq(e)?u(r,o):(r.prepend("</"+t.tagName+">"),a()):t.unary||o.push(t)},endTag:function(t){o.last()?n.tagSoupFix&&!o.lastTagNameEq(t.tagName)?u(r,o):o.pop():n.tagSoupFix&&(e(),a())}};function a(){var t=function(t,e){var r=t.stream,n=c(e());return t.stream=r,n}(r,e);t&&i[t.type]&&i[t.type](t)}return function(){return a(),c(e())}};var r=/^(AREA|BASE|BASEFONT|BR|COL|FRAME|HR|IMG|INPUT|ISINDEX|LINK|META|PARAM|EMBED)$/i,s=/^(COLGROUP|DD|DT|LI|OPTIONS|P|TD|TFOOT|TH|THEAD|TR)$/i;function c(t){return t&&"startTag"===t.type&&(t.unary=r.test(t.tagName)||t.unary,t.html5Unary=!/\/>$/.test(t.text)),t}function u(t,e){var r=e.pop();t.prepend("</"+r.tagName+">")}}],r.c=o,r.p="",r(0);function r(t){if(o[t])return o[t].exports;var e=o[t]={exports:{},id:t,loaded:!1};return n[t].call(e.exports,e,e.exports,r),e.loaded=!0,e.exports}var n,o},t.exports=n()},function(t,e){"use strict";e.__esModule=!0;var n="function"==typeof Symbol&&"symbol"==typeof Symbol.iterator?function(t){return typeof t}:function(t){return t&&"function"==typeof Symbol&&t.constructor===Symbol&&t!==Symbol.prototype?"symbol":typeof t};function o(t){return null!=t}function i(t,e,r){var n=void 0,o=t&&t.length||0;for(n=0;n<o;n++)e.call(r,t[n],n)}function a(t,e,r){for(var n in t)t.hasOwnProperty(n)&&e.call(r,n,t[n])}function r(t,e){return!(!t||"startTag"!==t.type&&"atomicTag"!==t.type||!("tagName"in t))&&!!~t.tagName.toLowerCase().indexOf(e)}e.existy=o,e.isFunction=function(t){return"function"==typeof t},e.each=i,e.eachKey=a,e.defaults=function(r,t){return r=r||{},a(t,function(t,e){o(r[t])||(r[t]=e)}),r},e.toArray=function(r){try{return Array.prototype.slice.call(r)}catch(t){var e=function(){var e=[];return i(r,function(t){e.push(t)}),{v:e}}();if("object"===(void 0===e?"undefined":n(e)))return e.v}},e.last=function(t){return t[t.length-1]},e.isTag=r,e.isScript=function(t){return r(t,"script")},e.isStyle=function(t){return r(t,"style")}}],r.c=o,r.p="",r(0);function r(t){if(o[t])return o[t].exports;var e=o[t]={exports:{},id:t,loaded:!1};return n[t].call(e.exports,e,e.exports,r),e.loaded=!0,e.exports}var n,o},t.exports=n()},function(t,e,r){"use strict";Object.defineProperty(e,"__esModule",{value:!0}),e.newEnvironment=function(r){function e(){return r.top!==r&&!function(t){try{return t.top.location.toString(),!0}catch(t){return!1}}(r)}return{isMobileApp:function(t){return t&&"mobile-app"===t},isCrossDomain:e,isSafeFrame:function(){return!(!r.$sf||!r.$sf.ext)},isAmp:function(t){return"string"==typeof t&&""!==t&&e()},canLocatePrebid:function(){for(var t=!1,e=r;!t;){try{if(e.pbjs){t=!0;break}}catch(t){}if(e===window.top)break;e=e.parent}return t}}}}]);;

'use strict';

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
