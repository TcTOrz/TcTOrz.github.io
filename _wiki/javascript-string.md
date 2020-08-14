---
layout: wiki
title: Javascript-string
categories: [Javascript, 面试]
description: 学习JS,及时记录下来
keywords: Javascript, 面试
---

## `String`

### `byteSize`

返回字符串的长度（以字节为单位）。

```js

const byteSize = str => new Blob([str]).size;
byteSize('😀'); // 4
byteSize('Hello World'); // 11

```

### `capitalize`

第一个字母大写。

```js

const capitalize = ([first, ...rest], lowerRest = false) =>
  first.toUpperCase() + (lowerRest ? rest.join('').toLowerCase() : rest.join(''));
capitalize('fooBar'); // 'FooBar'
capitalize('fooBar', true); // 'Foobar'

```

### `capitalizeEveryWord`

将字符串中每个单词的首字母大写。

```js

const capitalizeEveryWord = str => str.replace(/\b[a-z]/g, char => char.toUpperCase());
capitalizeEveryWord('hello world!'); // 'Hello World!'

```

### `compactWhitespace`

返回压缩了空格的字符串。

```js

const compactWhitespace = str => str.replace(/\s{2,}/g, ' ');
compactWhitespace('Lorem    Ipsum'); // 'Lorem Ipsum'
compactWhitespace('Lorem \n Ipsum'); // 'Lorem Ipsum'

```

### `containsWhitespace`

如果给定的字符串包含任何空格字符，则返回true，否则返回false。

```js

const containsWhitespace = str => /\s/.test(str);
containsWhitespace('lorem'); // false
containsWhitespace('lorem ipsum'); // true

```

### `CSVToArray`

```js

const CSVToArray = (data, delimiter = ',', omitFirstRow = false) =>
    data
        .slice(omitFirstRow ? data.indexOf('\n') + 1 : 0)
        .split('\n')
        .map(v => v.split(delimiter));
CSVToArray('a,b\nc,d'); // [['a','b'],['c','d']];
CSVToArray('a;b\nc;d', ';'); // [['a','b'],['c','d']];
CSVToArray('col1,col2\na,b\nc,d', ',', true); // [['a','b'],['c','d']];

```

### `CSVToJSON`

```js

const CSVToJSON = (data, delimiter = ',') => {
    const titles = data.slice(0, data.indexOf('\n')).split(delimiter);
    return data
        .slice(data.indexOf('\n') + 1)
        .split('\n')
        .map(v => {
            const values = v.split(delimiter);
            return titles.reduce((obj, title, index) => ((obj[title] = values[index]), obj), {});
        });
    };
CSVToJSON('col1,col2\na,b\nc,d'); // [{'col1': 'a', 'col2': 'b'}, {'col1': 'c', 'col2': 'd'}];
CSVToJSON('col1;col2\na;b\nc;d', ';'); // [{'col1': 'a', 'col2': 'b'}, {'col1': 'c', 'col2': 'd'}];

```

### `decapitalize`

```js

const decapitalize = ([first, ...rest], upperRest = false) =>
    first.toLowerCase() + (upperRest ? rest.join('').toUpperCase() : rest.join(''));
decapitalize('FooBar'); // 'fooBar'
decapitalize('FooBar', true); // 'fOOBAR'

```

### `escapeHTML`

转义Html字符串

[String.prototype.replace()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/replace)

```js

// example
function replacer(match, p1, p2, p3, offset, string) {
  // p1 is nondigits, p2 digits, and p3 non-alphanumerics
  return [p1, p2, p3].join(' - ');
}
var newString = 'abc12345#$*%'.replace(/([^\d]*)(\d*)([^\w]*)/, replacer);
console.log(newString);  // abc - 12345 - #$*%

```

```js

const escapeHTML = str =>
    str.replace(
        /[&<>'"]/g,
        tag =>
        ({
            '&': '&amp;',
            '<': '&lt;',
            '>': '&gt;',
            "'": '&#39;',
            '"': '&quot;'
        }[tag] || tag)
    );
escapeHTML('<a href="#">Me & you</a>'); // '&lt;a href=&quot;#&quot;&gt;Me &amp; you&lt;/a&gt;'

```

### `escapeRegExp`

转义要在正则表达式中使用的字符串。

```js

const escapeRegExp = str => str.replace(/[.*+?^${}()|[\]\\]/g, '\\$&');
escapeRegExp('(test)'); // \\(test\\)

```

### `expandTabs`

将制表符转换为空格，其中每个制表符对应于计数空格。

```js

const expandTabs = (str, count) => str.replace(/\t/g, ' '.repeat(count));
expandTabs('\t\tlorem', 3); // '      lorem'    

```

### `extendHex`

将3位颜色代码扩展为6位颜色代码。

```js

const extendHex = shortHex =>
    '#' +
    shortHex
        .slice(shortHex.startsWith('#') ? 1 : 0)
        .split('')
        .map(x => x + x)
        .join('');
extendHex('#03f'); // '#0033ff'
extendHex('05a'); // '#0055aa'

```

### `formatNumber`

使用本地数字格式顺序返回数字。

```js

const formatNumber = num => num.toLocaleString();
formatNumber(123456); // '123,456' in `en-US`
formatNumber(15675436903); // '15.675.436.903' in `de-DE`

```

### `fromCamelCase`

从驼峰式转换字符串。

```js

const fromCamelCase = (str, separator = '_') =>
    str
        .replace(/([a-z\d])([A-Z])/g, '$1' + separator + '$2')
        .replace(/([A-Z]+)([A-Z][a-z\d]+)/g, '$1' + separator + '$2')
        .toLowerCase();
fromCamelCase('someDatabaseFieldName', ' '); // 'some database field name'
fromCamelCase('someLabelThatNeedsToBeCamelized', '-'); // 'some-label-that-needs-to-be-camelized'
fromCamelCase('someJavascriptProperty', '_'); // 'some_javascript_property'

```

### `hexToRGB`

```js

const hexToRGB = hex => {
    let alpha = false,
        h = hex.slice(hex.startsWith('#') ? 1 : 0);
    if (h.length === 3) h = [...h].map(x => x + x).join('');
    else if (h.length === 8) alpha = true;
    h = parseInt(h, 16);
    return (
        'rgb' +
        (alpha ? 'a' : '') +
        '(' +
        (h >>> (alpha ? 24 : 16)) +
        ', ' +
        ((h & (alpha ? 0x00ff0000 : 0x00ff00)) >>> (alpha ? 16 : 8)) +
        ', ' +
        ((h & (alpha ? 0x0000ff00 : 0x0000ff)) >>> (alpha ? 8 : 0)) +
        (alpha ? `, ${h & 0x000000ff}` : '') +
        ')'
    );
};
// parseInt('27ae60ff', 16) === 665739519
// parseInt('27ae60ff', 16)>>>24 === 39
hexToRGB('#27ae60ff'); // 'rgba(39, 174, 96, 255)'
hexToRGB('27ae60'); // 'rgb(39, 174, 96)'
hexToRGB('#fff'); // 'rgb(255, 255, 255)'

```

### `indentString`

缩进首行。

```js

const indentString = (str, count, indent = ' ') => str.replace(/^/gm, indent.repeat(count));
indentString('Lorem\nIpsum', 2); // '  Lorem\n  Ipsum'
indentString('Lorem\nIpsum', 2, '_'); // '__Lorem\n__Ipsum'

```

### `isAbsoluteURL`

如果给定的字符串是绝对URL，则返回true，否则返回false。

```js

const isAbsoluteURL = str => /^[a-z][a-z0-9+.-]*:/.test(str);
isAbsoluteURL('https://google.com'); // true
isAbsoluteURL('ftp://www.myserver.net'); // true
isAbsoluteURL('/foo/bar'); // false

```

### `isAnagram`

检查一个字符串是否是另一个字符串的字谜（不区分大小写，忽略空格，标点符号和特殊字符）。

```js

const isAnagram = (str1, str2) => {
    const normalize = str =>
        str
        .toLowerCase()
        .replace(/[^a-z0-9]/gi, '')
        .split('')
        .sort()
        .join('');
    return normalize(str1) === normalize(str2);
};
isAnagram('iceman', 'cinema'); // true

```

### `isLowerCase`

检查字符串是否为小写。

```js

const isLowerCase = str => str === str.toLowerCase();
isLowerCase('abc'); // true
isLowerCase('a3@$'); // true
isLowerCase('Ab4'); // false

```

### `isUpperCase`

Checks if a string is upper case.

```js

const isUpperCase = str => str === str.toUpperCase();
isUpperCase('ABC'); // true
isUpperCase('A3@$'); // true
isUpperCase('aB4'); // false

```

### `mapString`

创建一个新字符串，并对调用字符串中的每个字符调用提供的函数。

```js

const mapString = (str, fn) =>
    str
        .split('')
        .map((c, i) => fn(c, i, str))
        .join('');
mapString('lorem ipsum', c => c.toUpperCase()); // 'LOREM IPSUM'

```

### `mask`

用指定的掩码字符替换字符。

[String.prototype.padStart()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/padStart)

```js

const mask = (cc, num = 4, mask = '*') => `${cc}`.slice(-num).padStart(`${cc}`.length, mask);
mask(1234567890); // '******7890'
mask(1234567890, 3); // '*******890'
mask(1234567890, -4, '$'); // '$$$$567890'

```

### `pad`

如果字符串短于指定的长度，就在字符串的两边填充字符串。

```js

const pad = (str, length, char = ' ') =>
    str.padStart((str.length + length) / 2, char).padEnd(length, char);
pad('cat', 8); // '  cat   '
pad(String(42), 6, '0'); // '004200'
pad('foobar', 3); // 'foobar'

```

### `palindrome`

如果给定的字符串是回文，则返回true，否则返回false。

```js

const palindrome = str => {
    const s = str.toLowerCase().replace(/[\W_]/g, '');
    return s === [...s].reverse().join('');
};
palindrome('taco cat'); // true

```

### `pluralize`

根据输入数字返回单词的单数或复数形式。 如果第一个参数是一个对象，它将通过返回一个函数来使用闭包，该函数可以自动复数不简单以s结尾的单词（如果提供的字典包含该单词）。

```js

const pluralize = (val, word, plural = word + 's') => {
    const _pluralize = (num, word, plural = word + 's') =>
        [1, -1].includes(Number(num)) ? word : plural;
    if (typeof val === 'object') return (num, word) => _pluralize(num, word, val[word]);
    return _pluralize(val, word, plural);
};
pluralize(0, 'apple'); // 'apples'
pluralize(1, 'apple'); // 'apple'
pluralize(2, 'apple'); // 'apples'
pluralize(2, 'person', 'people'); // 'people'
const PLURALS = {
    person: 'people',
    radius: 'radii'
};
const autoPluralize = pluralize(PLURALS);
autoPluralize(2, 'person'); // 'people'

```

### `prettyBytes`

将以字节为单位的数字转换为人类可读的字符串。

```js

const prettyBytes = (num, precision = 3, addSpace = true) => {
    const UNITS = ['B', 'KB', 'MB', 'GB', 'TB', 'PB', 'EB', 'ZB', 'YB'];
    if (Math.abs(num) < 1) return num + (addSpace ? ' ' : '') + UNITS[0];
    const exponent = Math.min(Math.floor(Math.log10(num < 0 ? -num : num) / 3), UNITS.length - 1);
    const n = Number(((num < 0 ? -num : num) / 1000 ** exponent).toPrecision(precision));
    return (num < 0 ? '-' : '') + n + (addSpace ? ' ' : '') + UNITS[exponent];
};
prettyBytes(1000); // "1 KB"
prettyBytes(-27145424323.5821, 5); // "-27.145 GB"
prettyBytes(123456789, 3, false); // "123MB"

```

### `Regular Expressions Cheat Sheet`

[正则表达式](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Regular_Expressions#special-negated-character-set)

### `removeNonASCII`

删除不可打印的ASCII字符。

```js

const removeNonASCII = str => str.replace(/[^\x20-\x7E]/g, '');
removeNonASCII('äÄçÇéÉêlorem-ipsumöÖÐþúÚ'); // 'lorem-ipsum'

```

### `reverseString`

反转字符串。

```js

const reverseString = str => [...str].reverse().join('');
reverseString('foobar'); // 'raboof'

```

### `RGBToHex`

将RGB分量的值转换为颜色代码。

```js

const RGBToHex = (r, g, b) => ((r << 16) + (g << 8) + b).toString(16).padStart(6, '0');
RGBToHex(255, 165, 1); // 'ffa501'

```

### `sortCharactersInString`

按字母顺序对字符串中的字符进行排序。

```js

const sortCharactersInString = str => [...str].sort((a, b) => a.localeCompare(b)).join('');
sortCharactersInString('cabbage'); // 'aabbceg'

```

### `splitLines`

将多行字符串拆分为行数组。

```js

const splitLines = str => str.split(/\r?\n/);
splitLines('This\nis a\nmultiline\nstring.\n'); // ['This', 'is a', 'multiline', 'string.' , '']

```

### `stringPermutations`

生成字符串的所有排列（包含重复项）。

警告：此功能的执行时间随着每个字符的增加而呈指数增长。 超过8到10个字符的任何字符都会导致您的浏览器在尝试解决所有不同的组合时挂起。

```js

const stringPermutations = str => {
    if (str.length <= 2) return str.length === 2 ? [str, str[1] + str[0]] : [str];
    return str
        .split('')
        .reduce(
        (acc, letter, i) =>
            acc.concat(stringPermutations(str.slice(0, i) + str.slice(i + 1)).map(val => letter + val)),
        []
        );
};
stringPermutations('abc'); // ['abc','acb','bac','bca','cab','cba']

```

### `stripHTMLTags`

从字符串中删除HTML / XML标签。

```js

const stripHTMLTags = str => str.replace(/<[^>]*>/g, '');
stripHTMLTags('<p><em>lorem</em> <strong>ipsum</strong></p>'); // 'lorem ipsum'

```

### `toCamelCase`

将字符串转换为驼峰式。

```js

const toCamelCase = str => {
    let s =
        str &&
        str
        .match(/[A-Z]{2,}(?=[A-Z][a-z]+[0-9]*|\b)|[A-Z]?[a-z]+[0-9]*|[A-Z]|[0-9]+/g)
        .map(x => x.slice(0, 1).toUpperCase() + x.slice(1).toLowerCase())
        .join('');
    return s.slice(0, 1).toLowerCase() + s.slice(1);
};
toCamelCase('some_database_field_name'); // 'someDatabaseFieldName'
toCamelCase('Some label that needs to be camelized'); // 'someLabelThatNeedsToBeCamelized'
toCamelCase('some-javascript-property'); // 'someJavascriptProperty'
toCamelCase('some-mixed_string with spaces_underscores-and-hyphens'); // 'someMixedStringWithSpacesUnderscoresAndHyphens'

```

### `toKebabCase`

将字符串转换为kebab case。(带短横线式)

```js

const toKebabCase = str =>
    str &&
    str
        .match(/[A-Z]{2,}(?=[A-Z][a-z]+[0-9]*|\b)|[A-Z]?[a-z]+[0-9]*|[A-Z]|[0-9]+/g)
        .map(x => x.toLowerCase())
        .join('-');
toKebabCase('camelCase'); // 'camel-case'
toKebabCase('some text'); // 'some-text'
toKebabCase('some-mixed_string With spaces_underscores-and-hyphens'); // 'some-mixed-string-with-spaces-underscores-and-hyphens'
toKebabCase('AllThe-small Things'); // "all-the-small-things"
toKebabCase('IAmListeningToFMWhileLoadingDifferentURLOnMyBrowserAndAlsoEditingSomeXMLAndHTML'); // "i-am-listening-to-fm-while-loading-different-url-on-my-browser-and-also-editing-xml-and-html"

```

### `toSnakeCase`

```js

const toSnakeCase = str =>
    str &&
    str
        .match(/[A-Z]{2,}(?=[A-Z][a-z]+[0-9]*|\b)|[A-Z]?[a-z]+[0-9]*|[A-Z]|[0-9]+/g)
        .map(x => x.toLowerCase())
        .join('_');
toSnakeCase('camelCase'); // 'camel_case'
toSnakeCase('some text'); // 'some_text'
toSnakeCase('some-mixed_string With spaces_underscores-and-hyphens'); // 'some_mixed_string_with_spaces_underscores_and_hyphens'
toSnakeCase('AllThe-small Things'); // "all_the_small_things"
toSnakeCase('IAmListeningToFMWhileLoadingDifferentURLOnMyBrowserAndAlsoEditingSomeXMLAndHTML'); // "i_am_listening_to_fm_while_loading_different_url_on_my_browser_and_also_editing_some_xml_and_html"

```

### `toTitleCase`

```js

const toTitleCase = str =>
    str
        .match(/[A-Z]{2,}(?=[A-Z][a-z]+[0-9]*|\b)|[A-Z]?[a-z]+[0-9]*|[A-Z]|[0-9]+/g)
        .map(x => x.charAt(0).toUpperCase() + x.slice(1))
        .join(' ');
toTitleCase('some_database_field_name'); // 'Some Database Field Name'
toTitleCase('Some label that needs to be title-cased'); // 'Some Label That Needs To Be Title Cased'
toTitleCase('some-package-name'); // 'Some Package Name'
toTitleCase('some-mixed_string with spaces_underscores-and-hyphens'); // 'Some Mixed String With Spaces Underscores And Hyphens'

```

### `truncateString`

将字符串截断为指定长度。

```js

const truncateString = (str, num) =>
    str.length > num ? str.slice(0, num > 3 ? num - 3 : num) + '...' : str;
truncateString('boomerang', 7); // 'boom...'

```

### `unescapeHTML`

转义HTML字符。

```js

const unescapeHTML = str =>
    str.replace(
        /&amp;|&lt;|&gt;|&#39;|&quot;/g,
        tag =>
        ({
            '&amp;': '&',
            '&lt;': '<',
            '&gt;': '>',
            '&#39;': "'",
            '&quot;': '"'
        }[tag] || tag)
    );
unescapeHTML('&lt;a href=&quot;#&quot;&gt;Me &amp; you&lt;/a&gt;'); // '<a href="#">Me & you</a>'

```

### `URLJoin`

将所有给定的URL段连接在一起，然后标准化结果URL。

```js

const URLJoin = (...args) =>
    args
        .join('/')
        .replace(/[\/]+/g, '/')
        .replace(/^(.+):\//, '$1://')
        .replace(/^file:/, 'file:/')
        .replace(/\/(\?|&|#[^!])/g, '$1')
        .replace(/\?/g, '&')
        .replace('&', '?');
URLJoin('http://www.google.com', 'a', '/b/cd', '?foo=123', '?bar=foo'); // 'http://www.google.com/a/b/cd?foo=123&bar=foo'

```

### `words`

将给定的字符串转换为单词数组。

```js

const words = (str, pattern = /[^a-zA-Z-]+/) => str.split(pattern).filter(Boolean);
words('I love javaScript!!'); // ["I", "love", "javaScript"]
words('python, javaScript & coffee'); // ["python", "javaScript", "coffee"]

```

### `yesNo`

如果字符串是y / yes，则返回true；如果字符串是n / no，则返回false。

```js

const yesNo = (val, def = false) =>
    /^(y|yes)$/i.test(val) ? true : /^(n|no)$/i.test(val) ? false : def;
yesNo('Y'); // true
yesNo('yes'); // true
yesNo('No'); // false
yesNo('Foo', true); // true

```