# Node XLSX

[![npm version](https://img.shields.io/npm/v/node-xlsx.svg?style=flat)](https://www.npmjs.com/package/node-xlsx)
[![license](https://img.shields.io/badge/license-Apache--2.0-green.svg)](https://tldrlegal.com/license/apache-license-2.0-(apache-2.0))
![status](https://img.shields.io/badge/status-maintained-brightgreen.svg)
[![npm downloads](https://img.shields.io/npm/dm/node-xlsx.svg)](https://www.npmjs.com/package/node-xlsx)<br />
[![build status](http://img.shields.io/travis/mgcrea/node-xlsx/master.svg?style=flat)](http://travis-ci.org/mgcrea/node-xlsx)
[![dependencies status](https://img.shields.io/david/mgcrea/node-xlsx.svg?style=flat)](https://david-dm.org/mgcrea/node-xlsx)
[![devDependencies status](https://img.shields.io/david/dev/mgcrea/node-xlsx.svg?style=flat)](https://david-dm.org/mgcrea/node-xlsx#info=devDependencies)
[![Codacy Badge](https://api.codacy.com/project/badge/Grade/5bbea5e7b2084c2586e5599cda6aefc8)](https://www.codacy.com/app/mgcrea/node-xlsx?utm_source=github.com&amp;utm_medium=referral&amp;utm_content=mgcrea/node-xlsx&amp;utm_campaign=Badge_Grade)
[![Codacy Badge](https://api.codacy.com/project/badge/Coverage/5bbea5e7b2084c2586e5599cda6aefc8)](https://www.codacy.com/app/mgcrea/node-xlsx?utm_source=github.com&utm_medium=referral&utm_content=mgcrea/node-xlsx&utm_campaign=Badge_Coverage)


Excel file parser/builder that relies on [js-xlsx](https://github.com/SheetJS/js-xlsx).


## Usage
### Installation
```npm install node-xlsx --save```
### Examples

1. Parsing a xlsx from file/buffer, outputs an array of worksheets

```js
import xlsx from 'node-xlsx';
// Or var xlsx = require('node-xlsx').default;

// Parse a buffer
const workSheetsFromBuffer = xlsx.parse(fs.readFileSync(`${__dirname}/myFile.xlsx`));
// Parse a file
const workSheetsFromFile = xlsx.parse(`${__dirname}/myFile.xlsx`);
```

2. Building a xlsx

```js
import xlsx from 'node-xlsx';
// Or var xlsx = require('node-xlsx').default;

const data = [[1, 2, 3], [true, false, null, 'sheetjs'], ['foo', 'bar', new Date('2014-02-19T14:30Z'), '0.3'], ['baz', null, 'qux']];
var buffer = xlsx.build([{name: "mySheetName", data: data}]); // Returns a buffer
```

3. Cell styling example

```
data.push([{
    v: 'List URL',
    s: {
        fill: {
            fgColor: {
                rgb: "FFFFAA00"
            }
        },
        font: {
            name: 'Calibri',
            sz: 14,
            bold: true
        }
    },
    l: {
        Target: targetLink,
        Tooltip: targetLink ? (`List URL ${targetLink.substr(0, 20)}...`) : null
    }
}]);

```

  * Custom column width
```js
import xlsx from 'node-xlsx';
// Or var xlsx = require('node-xlsx').default;

const data = [[1, 2, 3], [true, false, null, 'sheetjs'], ['foo', 'bar', new Date('2014-02-19T14:30Z'), '0.3'], ['baz', null, 'qux']]
const options = {'!cols': [{ wch: 6 }, { wch: 7 }, { wch: 10 }, { wch: 20 } ]};
options.defaultCellStyle: {
    font: {
        name: 'Calibri',
        sz: 14,
        bold: true
    }
}

var buffer = xlsx.build([{name: "mySheetName", data: data}], options); // Returns a buffer
```

   * Cell Styles

Cell styles are specified by a style object that roughly parallels the OpenXML structure.  The style object has five
top-level attributes: `fill`, `font`, `numFmt`, `alignment`, and `border`.


| Style Attribute | Sub Attributes | Values |
| --------------- | -------------- | -------|
| fill            | patternType    |  `"solid"` or `"none"` |
|                 | fgColor        |  `COLOR_SPEC` |
|                 | bgColor        |  `COLOR_SPEC` |
| font            | name           |  `"Calibri"` // default |
|                 | sz             |  `"11"` // font size in points |
|                 | color          |  `COLOR_SPEC` |
|                 | bold           |  `true || false` |
|                 | underline      |  `true || false` |
|                 | italic         |  `true || false` |
|                 | strike         |  `true || false` |
|                 | outline        |  `true || false` |
|                 | shadow         |  `true || false` |
|                 | vertAlign      |  `true || false` |
| numFmt          |                |  `"0"`  // integer index to built in formats, see StyleBuilder.SSF property  |
|                 |                |  `"0.00%"` // string matching a built-in format, see StyleBuilder.SSF  |
|                 |                |  `"0.0%"`  // string specifying a custom format  |
|                 |                |  `"0.00%;\\(0.00%\\);\\-;@"` // string specifying a custom format, escaping special characters  |
|                 |                |  `"m/dd/yy"` // string a date format using Excel's format notation  |
| alignment       | vertical       | `"bottom"||"center"||"top"`  |
|                 | horizontal     | `"bottom"||"center"||"top"`  |
|                 | wrapText       |  `true || false` v
|                 | readingOrder   |  `2` // for right-to-left  |
|                 | textRotation   | Number from `0` to `180` or `255` (default is `0`)  |
|                 |                |  `90` is rotated up 90 degrees  |
|                 |                |  `45` is rotated up 45 degrees |
|                 |                | `135` is rotated down 45 degrees | 
|                 |                | `180` is rotated down 180 degrees |
|                 |                | `255` is special,  aligned vertically |
| border          | top            | `{ style: BORDER_STYLE, color: COLOR_SPEC }` |
|                 | bottom         | `{ style: BORDER_STYLE, color: COLOR_SPEC }` |
|                 | left           | `{ style: BORDER_STYLE, color: COLOR_SPEC }` |
|                 | right          | `{ style: BORDER_STYLE, color: COLOR_SPEC }` |
|                 | diagonal       | `{ style: BORDER_STYLE, color: COLOR_SPEC }` |
|                 | diagonalUp     | `true||false` |
|                 | diagonalDown   | `true||false` |


**COLOR_SPEC**: Colors for `fill`, `font`, and `border` are specified as objects, either:
* `{ auto: 1}` specifying automatic values
* `{ rgb: "FFFFAA00" }` specifying a hex ARGB value
* `{ theme: "1", tint: "-0.25"}` specifying an integer index to a theme color and a tint value (default 0)
* `{ indexed: 64}` default value for `fill.bgColor`

**BORDER_STYLE**: Border style is a string value which may take on one of the following values:
 * `thin`
 * `medium`
 * `thick`
 * `dotted`
 * `hair`
 * `dashed`
 * `mediumDashed`
 * `dashDot`
 * `mediumDashDot`
 * `dashDotDot`
 * `mediumDashDotDot`
 * `slantDashDot`


  * Spanning multiple rows `A1:A4` in every sheets
```js
import xlsx from 'node-xlsx';
// Or var xlsx = require('node-xlsx').default;

const data = [[1, 2, 3], [true, false, null, 'sheetjs'], ['foo', 'bar', new Date('2014-02-19T14:30Z'), '0.3'], ['baz', null, 'qux']];
const range = {s: {c: 0, r:0 }, e: {c:0, r:3}}; // A1:A4
const options = {'!merges': [ range ]};

var buffer = xlsx.build([{name: "mySheetName", data: data}], options); // Returns a buffer
```

  * Spanning multiple rows `A1:A4` in second sheet only
```js
import xlsx from 'node-xlsx';
// Or var xlsx = require('node-xlsx').default;

const dataSheet1 = [[1, 2, 3], [true, false, null, 'sheetjs'], ['foo', 'bar', new Date('2014-02-19T14:30Z'), '0.3'], ['baz', null, 'qux']];
const dataSheet2 = [[4, 5, 6], [7, 8, 9, 10], [11, 12, 13, 14], ['baz', null, 'qux']];
const range = {s: {c: 0, r:0 }, e: {c:0, r:3}}; // A1:A4
const sheetOptions = {'!merges': [ range ]};

var buffer = xlsx.build([{name: "myFirstSheet", data: dataSheet1}, {name: "mySecondSheet", data: dataSheet2, options: sheetOptions}]); // Returns a buffer
```
_Beware that if you try to merge several times the same cell, your xlsx file will be seen as corrupted._


  * Using Primitive Object Notation
Data values can also be specified in a non-abstracted representation.

Examples:
```js
const rowAverage = [[{t:'n', z:10, f:'=AVERAGE(2:2)'}], [1,2,3];
var buffer = xlsx.build([{name: "Average Formula", data: rowAverage}]);
```

Refer to [xlsx](https://sheetjs.gitbooks.io) documentation for valid structure and values:
- Cell Object: https://sheetjs.gitbooks.io/docs/#cell-object
- Data Types: https://sheetjs.gitbooks.io/docs/#data-types
- Format: https://sheetjs.gitbooks.io/docs/#number-formats



### Troubleshooting

This library requires at lease nodeJS v4. For legacy versions, you can use this workaround before using the lib.

```
npm i --save object-assign
Object.prototype.assign = require('object-assign');
```


### Contributing

Please submit all pull requests the against master branch. If your unit test contains javascript patches or features, you should include relevant unit tests. Thanks!


### Available scripts

| **Script** | **Description** |
|----------|-------|
| start | Alias of test:watch |
| test | Run mocha unit tests |
| test:watch | Run and watch mocha unit tests |
| lint | Run eslint static tests |
| compile | Compile the library |
| compile:watch | Compile and watch the library |


## Authors

**Olivier Louvignes**

+ http://olouv.com
+ http://github.com/mgcrea


## Copyright and license

[Apache License 2.0](https://spdx.org/licenses/Apache-2.0.html)

```
Copyright (C) 2012-2014  Olivier Louvignes

   Licensed under the Apache License, Version 2.0 (the "License");
   you may not use this file except in compliance with the License.
   You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

   Unless required by applicable law or agreed to in writing, software
   distributed under the License is distributed on an "AS IS" BASIS,
   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
   See the License for the specific language governing permissions and
   limitations under the License.

Except where noted, this license applies to any and all software programs and associated documentation files created by the Original Author and distributed with the Software:

Inspired by SheetJS gist examples, Copyright (c) SheetJS.
```
