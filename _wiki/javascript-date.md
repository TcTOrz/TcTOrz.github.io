---
layout: wiki
title: Javascript-date
categories: [Javascript, 面试]
description: 学习JS,及时记录下来
keywords: Javascript, 面试
---

## `Date`

### `dayOfYear`

[Date MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date)

```js

const dayOfYear = date => Math.floor((date - new Date(date.getFullYear(), 0, 0)) / 1000 / 60 / 60 / 24);
dayOfYear(new Date()); // 225

```

### `formatDuration`

```js

const formatDuration = ms => {
    if (ms < 0) ms = -ms;
    const time = {
        day: Math.floor(ms / 86400000),
        hour: Math.floor(ms / 3600000) % 24,
        minute: Math.floor(ms / 60000) % 60,
        second: Math.floor(ms / 1000) % 60,
        millisecond: Math.floor(ms) % 1000
    };
    return Object.entries(time)
        .filter(val => val[1] !== 0)
        .map(([key, val]) => `${val} ${key}${val !== 1 ? 's' : ''}`)
        .join(', ');
};
formatDuration(1001); // '1 second, 1 millisecond'
formatDuration(34325055574); // '397 days, 6 hours, 44 minutes, 15 seconds, 574 milliseconds'

```

### `getColonTimeFromDate`

从Date对象返回形式为HH：MM：SS的字符串。

```js

const getColonTimeFromDate = date => date.toTimeString().slice(0, 8);
getColonTimeFromDate(new Date()); // "08:38:00"

```

### `getDaysDiffBetweenDates`

返回两个日期之间的差（以天为单位）。

```js

const getDaysDiffBetweenDates = (dateInitial, dateFinal) =>
    (dateFinal - dateInitial) / (1000 * 3600 * 24);
getDaysDiffBetweenDates(new Date('2015-03-13'), new Date('2020-08-12')); // 1979

```

### `getMeridiemSuffixOfInteger`

将整数转换为带后缀的字符串，并根据其值添加am或pm。

```js

const getMeridiemSuffixOfInteger = num =>
    num === 0 || num === 24
        ? 12 + 'am'
        : num === 12
        ? 12 + 'pm'
        : num < 12
        ? (num % 12) + 'am'
        : (num % 12) + 'pm';
getMeridiemSuffixOfInteger(0); // "12am"
getMeridiemSuffixOfInteger(11); // "11am"
getMeridiemSuffixOfInteger(13); // "1pm"
getMeridiemSuffixOfInteger(25); // "1pm"

```

### `getMonthsDiffBetweenDates`

返回两个日期之间的差（以月为单位）。

```js

const getMonthsDiffBetweenDates = (dateInitial, dateFinal) =>
    Math.max(
        (dateFinal.getFullYear() - dateInitial.getFullYear()) * 12 +
        dateFinal.getMonth() -
        dateInitial.getMonth(),
        0
    );
getMonthsDiffBetweenDates(new Date('2015-03-13'), new Date('2020-08-12')); // 65

```

### `How can I implement a sleep function in JavaScript?`

##### `setTimeout`

```js

const printNums = () => {
    console.log(1);
    setTimeout(() => console.log(2), 500);
    console.log(3);
};

printNums(); // Logs: 1, 3, 2 (2 logs after 500ms)

```

##### `Synchronous`

```js

const sleepSync = (ms) => {
    const end = new Date().getTime() + ms;
    while (new Date().getTime() < end) { /* do nothing */ }
}

const printNums = () => {
    console.log(1);
    sleepSync(500);
    console.log(2);
    console.log(3);
};

printNums(); // Logs: 1, 2, 3 (2 and 3 log after 500ms)

```

##### `Asynchronous`

```js

const sleep = (ms) =>
    new Promise(resolve => setTimeout(resolve, ms));

const printNums = async() => {
    console.log(1);
    await sleep(500);
    console.log(2);
    console.log(3);
};

printNums(); // Logs: 1, 2, 3 (2 and 3 log after 500ms)

```

### `isAfterDate`

检查日期是否在另一个日期之后。

```js

const isAfterDate = (dateA, dateB) => dateA > dateB;
isAfterDate(new Date(2010, 10, 21), new Date(2010, 10, 20)); // true

```

### `isBeforeDate`

检查日期是否在另一个日期之前。

```js

const isBeforeDate = (dateA, dateB) => dateA < dateB;
isBeforeDate(new Date(2010, 10, 20), new Date(2010, 10, 21)); // true

```

### `isLeapYear`

闰年返回true，否则false

```js

const isLeapYear = year => new Date(year, 1, 29).getMonth() === 1;
isLeapYear(2019); // false
isLeapYear(2020); // true

```

### `isSameDate`

检查一个日期是否与另一个日期相同。

```js

const isSameDate = (dateA, dateB) => dateA.toISOString() === dateB.toISOString();
isSameDate(new Date(2010, 10, 20), new Date(2010, 10, 20)); // true

```

### `isWeekday`

```js

const isWeekday = (t = new Date()) => {
  return t.getDay() % 6 !== 0;
};
isWeekday(); // true (if current date is 2019-07-19)

```

### `isWeekend`

```js

const isWeekend = (t = new Date()) => {
  return t.getDay() % 6 === 0;
};
isWeekend();

```

### `maxDate`

```js

const maxDate = dates => new Date(Math.max(...dates));
const array = [
  new Date(2017, 4, 13),
  new Date(2018, 2, 12),
  new Date(2016, 0, 10),
  new Date(2016, 0, 9)
];
maxDate(array); // 2018-03-11T22:00:00.000Z

```

### `tomorrow`

```js

const tomorrow = () => {
    let t = new Date();
    t.setDate(t.getDate() + 1);
    return t.toISOString().split('T')[0];
};
tomorrow();

```