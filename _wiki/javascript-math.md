---
layout: wiki
title: Javascript-math
categories: [Javascript, 面试]
description: 学习JS,及时记录下来
keywords: Javascript, 面试
---

## `Math`

### `accumulate`

```js

const accumulate = (...nums) => nums.reduce((acc, n) => [...acc, n + +acc.slice(-1)], []);
accumulate(1, 2, 3, 4); // [1, 3, 6, 10]
accumulate(...[1, 2, 3, 4]); // [1, 3, 6, 10]

```

### `and`

```js

and(true, true); // true
and(true, false); // false
and(false, false); // false

```

### `approximatelyEqual`

检查两个数字是否彼此近似相等。

```js

const approximatelyEqual = (v1, v2, epsilon = 0.001) => Math.abs(v1 - v2) < epsilon;
approximatelyEqual(Math.PI / 2.0, 1.5708); // true

```

### `average`

```js

const average = (...nums) => nums.reduce((acc, val) => acc + val, 0) / nums.length;
average(...[1, 2, 3]); // 2
average(1, 2, 3); // 2

```

### `averageBy`

```js

const averageBy = (arr, fn) => arr.map(typeof fn === 'function' ? fn : val => val[fn]).reduce((acc, val) => acc + val, 0) / arr.length;
averageBy([{ n: 4 }, { n: 2 }, { n: 8 }, { n: 6 }], o => o.n); // 5
averageBy([{ n: 4 }, { n: 2 }, { n: 8 }, { n: 6 }], 'n'); // 5

```

### `binomialCoefficient`

计算两个整数n和k的二项式系数。

```js

const binomialCoefficient = (n, k) => {
  if (Number.isNaN(n) || Number.isNaN(k)) return NaN;
  if (k < 0 || k > n) return 0;
  if (k === 0 || k === n) return 1;
  if (k === 1 || k === n - 1) return n;
  if (n - k < k) k = n - k;
  let res = n;
  for (let j = 2; j <= k; j++) res *= (n - j + 1) / j;
  return Math.round(res);
};
binomialCoefficient(8, 2); // 28

```

### `celsiusToFahrenheit`

将摄氏温度转换为华氏温度。

```js

const celsiusToFahrenheit = degrees => 1.8 * degrees + 32;
celsiusToFahrenheit(33); // 91.4

```

### `clampNumber`

```js

const clampNumber = (num, a, b) => Math.max(Math.min(num, Math.max(a, b)), Math.min(a, b));
clampNumber(2, 3, 5); // 3
clampNumber(1, -1, -5); // -1

```

### `degreesToRads`

将角度转换为弧度。

```js

const degreesToRads = deg => (deg * Math.PI) / 180.0;
degreesToRads(90.0); // ~1.5708

```

### `digitize`

将数字转换为数字数组。

```js

const digitize = n => String(n).split('').map(i => parseInt(i));
// or
const digitize = n => [...String(n)].map(i => parseInt(i));
// or
const digitize = n => [...`${n}`].map(i => parseInt(i));
digitize(123); // [1, 2, 3]

```

### `distance`

[`Math.hypot()` 函数返回所有参数的平方和的平方根](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Math/hypot)

返回两点之间的距离。

```js

const distance = (x0, y0, x1, y1) => Math.hypot(x1 - x0, y1 - y0);
distance(1, 1, 2, 3); // 2.23606797749979

```

### `elo`

```js

const elo = ([...ratings], kFactor = 32, selfRating) => {
    const [a, b] = ratings;
    const expectedScore = (self, opponent) => 1 / (1 + 10 ** ((opponent - self) / 400));
    const newRating = (rating, i) =>
        (selfRating || rating) + kFactor * (i - expectedScore(i ? a : b, i ? b : a));
    if (ratings.length === 2) return [newRating(a, 1), newRating(b, 0)];

    for (let i = 0, len = ratings.length; i < len; i++) {
        let j = i;
        while (j < len - 1) {
            j++;
            [ratings[i], ratings[j]] = elo([ratings[i], ratings[j]], kFactor);
        }
    }
    return ratings;
};
// Standard 1v1s
elo([1200, 1200]); // [1216, 1184]
elo([1200, 1200], 64); // [1232, 1168]
// 4 player FFA, all same rank
elo([1200, 1200, 1200, 1200]).map(Math.round); // [1246, 1215, 1185, 1154]
/*
For teams, each rating can adjusted based on own team's average rating vs.
average rating of opposing team, with the score being added to their
own individual rating by supplying it as the third argument.
*/

```

### `factorial`

阶乘

```js

const factorial = n =>
  n < 0
    ? (() => {
        throw new TypeError('Negative numbers are not allowed!');
      })()
    : n <= 1
    ? 1
    : n * factorial(n - 1);
factorial(6); // 720

```

### `fahrenheitToCelsius`

将华氏温度转换为摄氏温度。

```js

const fahrenheitToCelsius = degrees => ((degrees - 32) * 5) / 9;
fahrenheitToCelsius(32); // 0

```

### `fibonacci`

```js

const fibonacci = n =>
  Array.from({ length: n }).reduce(
        (acc, val, i) => acc.concat(i > 1 ? acc[i - 1] + acc[i - 2] : i),
        []
  );
fibonacci(6); // [0, 1, 1, 2, 3, 5] 

```

### `gcd`

计算两个或多个数字/数组之间的最大公约数。

```js

const gcd = (...arr) => {
  const _gcd = (x, y) => (!y ? x : gcd(y, x % y));
  return [...arr].reduce((a, b) => _gcd(a, b));
};
gcd(8, 36); // 4
gcd(...[12, 8, 32]); // 4

```

### `geometricProgression`

```js

const geometricProgression = (end, start = 1, step = 2) =>
    Array.from({ length: Math.floor(Math.log(end / start) / Math.log(step)) + 1 }).map(
        (v, i) => start * step ** i
    );
geometricProgression(256); // [1, 2, 4, 8, 16, 32, 64, 128, 256]
geometricProgression(256, 3); // [3, 6, 12, 24, 48, 96, 192]
geometricProgression(256, 1, 4); // [1, 4, 16, 64, 256]

```

### `hammingDistance`

汉明距离度量了通过替换字符的方式将字符串x变成y所需要的最小的替换次数。

```js

// 举例说明以下字符串间的汉明距离为：
// "karolin" and "kathrin" is 3.
// "karolin" and "kerstin" is 3.
// 1011101 and 1001001 is 2.
// 2173896 and 2233796 is 3.

```

```js

const hammingDistance = (num1, num2) => ((num1 ^ num2).toString(2).match(/1/g) || '').length;
hammingDistance(2, 3); // 1

```

### `inRange`

```js

// end未赋值则从0开始
const inRange = (n, start, end = null) => {
  if (end && start > end) [end, start] = [start, end];
  return end == null ? n >= 0 && n < start : n >= start && n < end;
};
inRange(3, 2, 5); // true
inRange(3, 4); // true
inRange(2, 3, 5); // false
inRange(3, 2); // false

```

### `isDivisible`

检查第一个数字参数是否可被第二个参数整除。

```js

const isDivisible = (dividend, divisor) => dividend % divisor === 0;
isDivisible(6, 3); // true

```

### `isEven`

```js

const isEven = num => num % 2 === 0;
isEven(3); // false

```

### `isNegativeZero`

检查给定值是否等于负零（-0）。

```js

const isNegativeZero = val => val === 0 && 1 / val === -Infinity;
isNegativeZero(-0); // true
isNegativeZero(0); // false

```

### `isOdd`

```js

const isOdd = num => num % 2 === 1;
isOdd(3); // true

```

### `isPowerOfTwo`

如果给定数字为2的幂，则返回true，否则返回false。

```js

const isPowerOfTwo = n => !!n && (n & (n - 1)) == 0;
isPowerOfTwo(0); // false
isPowerOfTwo(1); // true
isPowerOfTwo(8); // true

```

### `isPrime`

检查提供的整数是否为质数。

```js

const isPrime = num => {
    const boundary = Math.floor(Math.sqrt(num));
    for (var i = 2; i <= boundary; i++) if (num % i === 0) return false;
    return num >= 2;
};
isPrime(11); // true

```

### `lcm`

返回两个或多个数字的最小公倍数。

```js

const lcm = (...arr) => {
    const gcd = (x, y) => (!y ? x : gcd(y, x % y));
    const _lcm = (x, y) => (x * y) / gcd(x, y);
    return [...arr].reduce((a, b) => _lcm(a, b));
};
lcm(12, 7); // 84
lcm(...[1, 3, 4, 5]); // 60

```

### `luhnCheck`

Luhn算法

```js

const luhnCheck = num => {
    let arr = (num + '')
        .split('')
        .reverse()
        .map(x => parseInt(x));
    let lastDigit = arr.splice(0, 1)[0];
    let sum = arr.reduce((acc, val, i) => (i % 2 !== 0 ? acc + val : acc + ((val * 2) % 9) || 9), 0);
    sum += lastDigit;
    return sum % 10 === 0;
};
luhnCheck('4485275742308327'); // true
luhnCheck(6011329933655299); //  false
luhnCheck(123456789); // false

```

### `mapNumRange`

```js

const mapNumRange = (num, inMin, inMax, outMin, outMax) =>
  ((num - inMin) * (outMax - outMin)) / (inMax - inMin) + outMin;
mapNumRange(5, 0, 10, 0, 100); // 50

```

### `maxBy`

```js

const maxBy = (arr, fn) => Math.max(...arr.map(typeof fn === 'function' ? fn : val => val[fn]));
maxBy([{ n: 4 }, { n: 2 }, { n: 8 }, { n: 6 }], o => o.n); // 8
maxBy([{ n: 4 }, { n: 2 }, { n: 8 }, { n: 6 }], 'n'); // 8

```

### `median`

返回数字数组的中位数。

```js

const median = arr => {
  const mid = Math.floor(arr.length / 2),
    nums = [...arr].sort((a, b) => a - b);
  return arr.length % 2 !== 0 ? nums[mid] : (nums[mid - 1] + nums[mid]) / 2;
};
median([5, 6, 50, 1, -5]); // 5

```

### `midpoint`

计算两对（x，y）点之间的中点。

```js

const midpoint = ([x1, y1], [x2, y2]) => [(x1 + x2) / 2, (y1 + y2) / 2];
midpoint([2, 2], [4, 4]); // [3, 3]
midpoint([4, 4], [6, 6]); // [5, 5]
midpoint([1, 3], [2, 4]); // [1.5, 3.5]

```

### `minBy`

```js

const minBy = (arr, fn) => Math.min(...arr.map(typeof fn === 'function' ? fn : val => val[fn]));
minBy([{ n: 4 }, { n: 2 }, { n: 8 }, { n: 6 }], o => o.n); // 2
minBy([{ n: 4 }, { n: 2 }, { n: 8 }, { n: 6 }], 'n'); // 2

```

### `not`

```js

const not = a => !a;
not(true); // false
not(false); // true

```

### `or`

```js

const or = (a, b) => a || b;
or(true, true); // true
or(true, false); // true
or(false, false); // false

```

### `percentile`

使用百分比公式来计算给定数组中有多少个数字小于或等于给定值。

```js

const percentile = (arr, val) =>
    (100 * arr.reduce((acc, v) => acc + (v < val ? 1 : 0) + (v === val ? 0.5 : 0), 0)) / arr.length;
percentile([1, 2, 3, 4, 5, 6, 7, 8, 9, 10], 6); // 55

```

### `powerset`

返回给定数字数组的幂集。

```js

const powerset = arr => arr.reduce((a, v) => a.concat(a.map(r => [v].concat(r))), [[]]);
powerset([1, 2]); // [[], [1], [2], [2, 1]]

```

### `primes`

质数

```js

const primes = num => {
    let arr = Array.from({ length: num - 1 }).map((x, i) => i + 2),
        sqroot = Math.floor(Math.sqrt(num)),
        numsTillSqroot = Array.from({ length: sqroot - 1 }).map((x, i) => i + 2);
    numsTillSqroot.forEach(x => (arr = arr.filter(y => y % x !== 0 || y === x)));
    return arr;
};
primes(10); // [2,3,5,7]

```

### `radsToDegrees`

将角度从弧度转换为度。

```js

const radsToDegrees = rad => (rad * 180.0) / Math.PI;
radsToDegrees(Math.PI / 2); // 90

```

### `randomHexColorCode`

生成随机的十六进制颜色代码。

```js

const randomHexColorCode = () => {
  let n = (Math.random() * 0xfffff * 1000000).toString(16);
  return '#' + n.slice(0, 6);
};
randomHexColorCode(); // "#e34155"

```

### `randomIntArrayInRange`

```js

const randomIntArrayInRange = (min, max, n = 1) =>
  Array.from({ length: n }, () => Math.floor(Math.random() * (max - min + 1)) + min);
randomIntArrayInRange(12, 35, 10); // [ 34, 14, 27, 17, 30, 27, 20, 26, 21, 14 ]

```

### `randomIntegerInRange`

```js

const randomIntegerInRange = (min, max) => Math.floor(Math.random() * (max - min + 1)) + min;
randomIntegerInRange(0, 5); // 2

```

### `randomNumberInRange`

```js

const randomNumberInRange = (min, max) => Math.random() * (max - min) + min;
randomNumberInRange(2, 10); // 6.0211363285087005

```

### `round`

```js

// Number(`${Math.round(1.005e2)}e-2`)
const round = (n, decimals = 0) => Number(`${Math.round(`${n}e${decimals}`)}e-${decimals}`);
round(1.005, 2); // 1.01

```

### `sdbm`

将输入字符串哈希为整数。

```js

const sdbm = str => {
    let arr = str.split('');
    return arr.reduce(
        (hashCode, currentVal) =>
        (hashCode = currentVal.charCodeAt(0) + (hashCode << 6) + (hashCode << 16) - hashCode),
        0
    );
}
sdbm('name'); // -3521204949

```

### `standardDeviation`

返回数字数组的标准差。

```js

const standardDeviation = (arr, usePopulation = false) => {
    const mean = arr.reduce((acc, val) => acc + val, 0) / arr.length;
    return Math.sqrt(
        arr.reduce((acc, val) => acc.concat((val - mean) ** 2), []).reduce((acc, val) => acc + val, 0) /
        (arr.length - (usePopulation ? 0 : 1))
    );
};
standardDeviation([10, 2, 38, 23, 38, 23, 21]); // 13.284434142114991 (sample)
standardDeviation([10, 2, 38, 23, 38, 23, 21], true); // 12.29899614287479 (population)

```

### `sum`

```js

const sum = (...arr) => [...arr].reduce((acc, val) => acc + val, 0);
sum(1, 2, 3, 4); // 10
sum(...[1, 2, 3, 4]); // 10

```

### `sumBy`

```js

const sumBy = (arr, fn) =>
    arr.map(typeof fn === 'function' ? fn : val => val[fn]).reduce((acc, val) => acc + val, 0);
sumBy([{ n: 4 }, { n: 2 }, { n: 8 }, { n: 6 }], o => o.n); // 20
sumBy([{ n: 4 }, { n: 2 }, { n: 8 }, { n: 6 }], 'n'); // 20

```

### `sumPower`

返回从开始到结束（包括两端）的所有数字的幂的和。

```js

const sumPower = (end, power = 2, start = 1) =>
  Array(end + 1 - start)
    .fill(0)
    .map((x, i) => (i + start) ** power)
    .reduce((a, b) => a + b, 0);
sumPower(10); // 385
sumPower(10, 3); // 3025
sumPower(10, 3, 5); // 2925

```

### `toCurrency`

取一个数字并返回指定的货币格式。

[`Intl.NumberFormat`是对语言敏感的格式化数字类的构造器类](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Intl/NumberFormat)

```js

const toCurrency = (n, curr, LanguageFormat = undefined) =>
    Intl.NumberFormat(LanguageFormat, { style: 'currency', currency: curr }).format(n);
toCurrency(123456.789, 'EUR'); // €123,456.79  | currency: Euro | currencyLangFormat: Local
toCurrency(123456.789, 'USD', 'en-us'); // $123,456.79  | currency: US Dollar | currencyLangFormat: English (United States)
toCurrency(123456.789, 'USD', 'fa'); // ۱۲۳٬۴۵۶٫۷۹ ؜$ | currency: US Dollar | currencyLangFormat: Farsi
toCurrency(322342436423.2435, 'JPY'); // ¥322,342,436,423 | currency: Japanese Yen | currencyLangFormat: Local
toCurrency(322342436423.2435, 'JPY', 'fi'); // 322 342 436 423 ¥ | currency: Japanese Yen | currencyLangFormat: Finnish

```

### `toDecimalMark`

将数字转换为小数点格式的字符串。

```js

const toDecimalMark = num => num.toLocaleString('en-US');
toDecimalMark(12305030388.9087); // "12,305,030,388.909"

```

### `toOrdinalSuffix`

在数字后添加序数后缀。

```js

const toOrdinalSuffix = num => {
    const int = parseInt(num),
        digits = [int % 10, int % 100],
        ordinals = ['st', 'nd', 'rd', 'th'],
        oPattern = [1, 2, 3, 4],
        tPattern = [11, 12, 13, 14, 15, 16, 17, 18, 19];
    return oPattern.includes(digits[0]) && !tPattern.includes(digits[1])
        ? int + ordinals[digits[0] - 1]
        : int + ordinals[3];
};
toOrdinalSuffix('123'); // "123rd"

```

### `toSafeInteger`

[Number.MIN_SAFE_INTEGER](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number/MIN_SAFE_INTEGER)

```js

const toSafeInteger = num =>
    Math.round(Math.max(Math.min(num, Number.MAX_SAFE_INTEGER), Number.MIN_SAFE_INTEGER));
toSafeInteger('3.2'); // 3
toSafeInteger(Infinity); // 9007199254740991

```

### `validateNumber`

```js

const validateNumber = n => !isNaN(parseFloat(n)) && isFinite(n) && Number(n) == n;
validateNumber('10'); // true

```

### `vectorAngle`

返回两个向量之间的角度（θ）。

```js

const vectorAngle = (x, y) => {
    let mX = Math.sqrt(x.reduce((acc, n) => acc + Math.pow(n, 2), 0));
    let mY = Math.sqrt(y.reduce((acc, n) => acc + Math.pow(n, 2), 0));
    return Math.acos(x.reduce((acc, n, i) => acc + n * y[i], 0) / (mX * mY));
};
vectorAngle([3, 4], [4, 3]); // 0.283794109208328

```

### `vectorDistance`

返回两个向量之间的距离。

```js

const vectorDistance = (x, y) =>
  Math.sqrt(x.reduce((acc, val, i) => acc + Math.pow(val - y[i], 2), 0));
vectorDistance([10, 0, 5], [20, 0, 10]); // 11.180339887498949

```