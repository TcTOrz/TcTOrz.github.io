---
layout: wiki
title: Javascript-node
categories: [Javascript, 面试]
description: 学习JS,及时记录下来
keywords: Javascript, 面试
---

## `Node`

> Note: 需要在***Node***环境下运行。

### `atob`

Decodes a string of data which has been encoded using base-64 encoding.

```js

const atob = str => Buffer.from(str, 'base64').toString('binary');
atob('Zm9vYmFy'); // 'foobar'

```

### `btoa`

```js

const btoa = str => Buffer.from(str, 'binary').toString('base64');
btoa('foobar'); // 'Zm9vYmFy'

```

### `colorize`

在文本中添加特殊字符以在控制台中以彩色打印（与console.log（）组合）。

```js

const colorize = (...args) => ({
    black: `\x1b[30m${args.join(' ')}`,
    red: `\x1b[31m${args.join(' ')}`,
    green: `\x1b[32m${args.join(' ')}`,
    yellow: `\x1b[33m${args.join(' ')}`,
    blue: `\x1b[34m${args.join(' ')}`,
    magenta: `\x1b[35m${args.join(' ')}`,
    cyan: `\x1b[36m${args.join(' ')}`,
    white: `\x1b[37m${args.join(' ')}`,
    bgBlack: `\x1b[40m${args.join(' ')}\x1b[0m`,
    bgRed: `\x1b[41m${args.join(' ')}\x1b[0m`,
    bgGreen: `\x1b[42m${args.join(' ')}\x1b[0m`,
    bgYellow: `\x1b[43m${args.join(' ')}\x1b[0m`,
    bgBlue: `\x1b[44m${args.join(' ')}\x1b[0m`,
    bgMagenta: `\x1b[45m${args.join(' ')}\x1b[0m`,
    bgCyan: `\x1b[46m${args.join(' ')}\x1b[0m`,
    bgWhite: `\x1b[47m${args.join(' ')}\x1b[0m`
});
console.log(colorize('foo').red); // 'foo' (red letters)
console.log(colorize('foo', 'bar').bgBlue); // 'foo bar' (blue background)
console.log(colorize(colorize('foo').yellow, colorize('foo').green).bgWhite); // 'foo bar' (first word in yellow letters, second word in green letters, white background for both)

```

### `createDirIfNotExists`

创建目录（如果不存在）。

```js

const fs = require('fs');
const createDirIfNotExists = dir => (!fs.existsSync(dir) ? fs.mkdirSync(dir) : undefined);
createDirIfNotExists('test'); // creates the directory 'test', if it doesn't exist

```

### Debugging Node.js using Chrome Developer Tools

Node.js can be debugged using Chrome Developer Tools since v6.3.0. Here's a quick guide on how to do this:

1. Download and install Node.js v6.3.0 or newer, if you don't already have it installed on your machine.
2. Run node with the --inspect-brk flag (e.g. node --inspect-brk index.js).
3. Open about:inspect in a new tab in Chrome. You should see something like the screenshot below.
4. Click Open dedicated DevTools for Node to open a new window connected to your Node.js instance.
5. Use the Developer Tools to debug your Node.js application!

### `hasFlags`

检查当前进程的参数是否包含指定的标志。

```js

const hasFlags = (...flags) =>
  flags.every(flag => process.argv.includes(/^-{1,2}/.test(flag) ? flag : '--' + flag));

// node testnode.js -s --test --cool=true --special
// node myScript.js -s --test --cool=true
hasFlags('-s'); // true
hasFlags('--test', 'cool=true', '-s'); // true
hasFlags('special'); // false

```

### `hashNode`

```js

const crypto = require('crypto');
const hashNode = val =>
    new Promise(resolve =>
        setTimeout(
        () =>
            resolve(
            crypto
                .createHash('sha256')
                .update(val)
                .digest('hex')
            ),
        0
        )
    );
hashNode(JSON.stringify({ a: 'a', b: [1, 2, 3, 4], foo: { c: 'bar' } })).then(console.log); // '04aa106279f5977f59f9067fa9712afc4aedc6f5862a8defc34552d8c7206393'

```

### `isDuplexStream`

检查给定的参数是否为双工（可读和可写）流。

```js

const isDuplexStream = val =>
    val !== null &&
    typeof val === 'object' &&
    typeof val.pipe === 'function' &&
    typeof val._read === 'function' &&
    typeof val._readableState === 'object' &&
    typeof val._write === 'function' &&
    typeof val._writableState === 'object';
const Stream = require('stream');
isDuplexStream(new Stream.Duplex()); // true

```

### `isReadableStream`

检查给定的参数是否为可读流。

```js

const isReadableStream = val =>
  val !== null &&
  typeof val === 'object' &&
  typeof val.pipe === 'function' &&
  typeof val._read === 'function' &&
  typeof val._readableState === 'object';
const fs = require('fs');
isReadableStream(fs.createReadStream('test.txt')); // true

```

### `isStream`

检查给定参数是否为流。

```js

const isStream = val => val !== null && typeof val === 'object' && typeof val.pipe === 'function';
const fs = require('fs');
isStream(fs.createReadStream('test.txt')); // true

```

### `isTravisCI`

检查当前环境是否为Travis CI。

```js

const isTravisCI = () => 'TRAVIS' in process.env && 'CI' in process.env;
isTravisCI(); // true (if code is running on Travis CI)

```

### `isWritableStream`

检查给定的参数是否为可写流。

```js

const isWritableStream = val =>
    val !== null &&
    typeof val === 'object' &&
    typeof val.pipe === 'function' &&
    typeof val._write === 'function' &&
    typeof val._writableState === 'object';
const fs = require('fs');
isWritableStream(fs.createWriteStream('test.txt')); // true

```

### `JSONToFile`

将JSON对象写入文件。

```js

const fs = require('fs');
const JSONToFile = (obj, filename) =>
    fs.writeFileSync(`${filename}.json`, JSON.stringify(obj, null, 2));
JSONToFile({ test: 'is passed' }, 'testJsonFile'); // writes the object to 'testJsonFile.json'

```

### `readFileLines`

返回指定文件中的行数组。

```js

const fs = require('fs');
const readFileLines = filename =>
    fs
        .readFileSync(filename)
        .toString('UTF8')
        .split('\n');
/*
contents of test.txt :
  line1
  line2
  line3
  ___________________________
*/
let arr = readFileLines('test.txt');
console.log(arr); // ['line1', 'line2', 'line3']

```

### `requireUncached`

从缓存中删除模块后加载模块（如果存在）。

```js

const requireUncached = module => {
  delete require.cache[require.resolve(module)];
  return require(module);
};
const fs = requireUncached('fs'); // 'fs' will be loaded fresh every time

```

### `untildify`

将波浪号路径转换为绝对路径。

```js

const untildify = str => str.replace(/^~($|\/|\\)/, `${require('os').homedir()}$1`);
untildify('~/node'); // '/Users/aUser/node'

```

### `UUIDGeneratorNode`

在Node.JS中生成一个UUID。

```js

const crypto = require('crypto');
const UUIDGeneratorNode = () =>
  ([1e7] + -1e3 + -4e3 + -8e3 + -1e11).replace(/[018]/g, c =>
    (c ^ (crypto.randomBytes(1)[0] & (15 >> (c / 4)))).toString(16)
  );
UUIDGeneratorNode(); // '79c7c136-60ee-40a2-beb2-856f1feabefc'

```