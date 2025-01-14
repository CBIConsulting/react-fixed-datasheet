[![JavaScript Style Guide](https://cdn.rawgit.com/standard/standard/master/badge.svg)](https://github.com/standard/standard)
[![Build Status](https://travis-ci.org/nadbm/react-datasheet.svg?branch=master)](https://travis-ci.org/nadbm/react-datasheet)
[![Coverage Status](https://coveralls.io/repos/github/nadbm/react-datasheet/badge.svg)](https://coveralls.io/github/nadbm/react-datasheet)
[![Issue Count](https://codeclimate.com/github/nadbm/react-datasheet/badges/issue_count.svg)](https://codeclimate.com/github/nadbm/react-datasheet)
[![npm version](https://badge.fury.io/js/react-datasheet.svg)](https://badge.fury.io/js/react-datasheet)
# React-Datasheet
A simple react component to create a spreadsheet. Adding new feature, fixed header and columns on scroll.

Demo here: https://nadbm.github.io/react-datasheet/

Examples are located in https://github.com/nadbm/react-datasheet/tree/master/docs/src/examples

Current features:
* Select cells, copy-paste cells
* Navigation using keyboard keys
* Deletion using keyboard keys
* Callbacks for onChange, valueRenderer(visible data)
* dataRenderer(underlying data in the input, takes the value by default)
* Optional fixed header on scroll

Todo:
* Create test for the new features.
* Fix resizing bugs.
* Fixed columns

## Installation

Install from npm:
```bash
$ npm install react-datasheet --save
```
Import in your project:

```javascript
import ReactDataSheet from 'react-datasheet';
// Be sure to include styles at some point, probably during your bootstrapping
import 'react-datasheet/lib/react-datasheet.css';
```

## Usage

React-Datasheet generates a table with the cells. Double-clicking or typing edits the value and if changed, initiates an onChange callback.

The data provided should be an array of rows, and each row should include the cells.

### Basic Usage
```jsx
class App extends React.Component {
  constructor (props) {
    super(props)
    this.state = {
      grid: [
        [{value:  1}, {value:  3}],
        [{value:  2}, {value:  4}]
      ]
    }
  }
  render () {
    return (
      <ReactDataSheet
        data={this.state.grid}
        valueRenderer={(cell) => cell.value}
        onChange={(cell, rowI, colJ, value) =>
          this.setState({
            grid: this.state.grid.map((col) =>
              col.map((rowCell) =>
                (rowCell == cell) ? ({value: value}) : rowCell
              )
            )
          })
        }
      />
    )
  }
}
```

### Cells with underlying data

There are two values that each cell shows. The first is via ```valueRenderer``` and the second is via ```dataRenderer```. When a cell is in *edit mode*, it will show the value returned from ```dataRenderer```. It needs to return a string as this value is set in an input field.
Each of these callbacks are passed the cell value as well as the cell's coordinates in the spreadsheet. This allows you to apply formatting logic at rendering time, such as *all cells in the third column should be formatted as dates*.

```jsx
const grid = [
   [{value:  5, expr: '1 + 4'}, {value:  6, expr: '6'}, {value: new Date('2008-04-10')}],
   [{value:  5, expr: '1 + 4'}, {value:  5, expr: '1 + 4'}, {value: new Date('2004-05-28')}]
]
const onChange = (cell, i, j, newValue) => console.log("New expression :" + newValue)
<ReactDataSheet
  data={grid}
  valueRenderer={(cell, i, j) => j == 2 ? cell.value.toDateString() : cell.value}
  dataRenderer={(cell, i, j) => j == 2 ? cell.value.toISOString() : cell.expr}
  onChange={}
/>
```

### Cells with underlying component

```jsx
const grid = [
   [{
    value:  5,
      component: (
        <button onClick={() => console.log("clicked")}>
          Rendered
        </button>
      )
    }]
]
<ReactDataSheet
  data={grid}
  valueRenderer={(cell) => cell.value}
/>
```
This renders a single cell with the value 5. Once in edit mode, the button will appear.


### Cells with extra attributes

```jsx
const grid = [
  [{value:  1, hint: 'Valid'}, {value:  3, hint: 'Not valid'}],
  [{value:  2}, {value:  4}]
]
<ReactDataSheet
  data={grid}
  valueRenderer={(cell) => cell.value}
  attributesRenderer={(cell) => {'data-hint': cell.hint || {}}
  ...
/>
```
This render 2 rows, each one with two cells, the cells in the first row will have an attribute data-hint and the other 2 will not.

## Options

Option | Type | Description
:--- | :---: | :---
data | Array | Array of rows and each row should contain the cell objects to display
valueRenderer | func | Method to render the value of the cell `function(cell, i, j)`. This is visible by default
dataRenderer | func | Method to render the underlying value of the cell `function(cell, i, j)`. This data is visible once in edit mode.
overflow | 'wrap'\|'nowrap'\|'clip' | Grid default for how to render overflow text in cells
onChange | func | onChange handler: `function(cell, i, j, newValue) {}`
onPaste | func | onPaste handler: `function(array) {}` If set, the function will be called with an array of rows. Each row has an array of objects containing the cell and raw pasted value. If the pasted value cannot be matched with a cell, the cell value will be undefined
onContextMenu | func | Context menu handler : `function(event, cell, i, j)`
parsePaste | func | `function (string) {}` If set, the function will be called with the raw clipboard data. It should return an array of arrays of strings. This is useful for when the clipboard may have data with irregular field or line delimiters. If not set, rows will be split with line breaks and cells with tabs.
attributesRenderer | func | Method to add attributes to the cell `function(cell, i, j)`. The function should return an object where the keys are the attribute names and the values are their values. Example bellow.

## Cell Options

The cell object is what gets passed back to the onChange callback. They can contain the following options as well

Option | Type | Default |  Description
:--- | :--- | :--- | :--
readOnly | Bool | false | Cell will never go in edit mode
key | String | undefined | By default, each cell is given the key of col number and row number. This would override that key
className | String | undefined | Additional class names for cells.
component | ReactElement | undefined | Insert a react element or JSX to this field. This will render on edit mode
forceComponent | bool | false | Renders what's in component at all times, even when not in edit mode
disableEvents | bool | false | Makes cell unselectable and read only
colSpan | number | 1 | The colSpan of the cell's td element
rowSpan | number | 1 | The rowSpan of the cell's td element
width | number or String | undefined | Sets the cell's td width using a style attribute. Number is interpreted as pixels, strings are used as-is. Note: This will only work if the table does not have a set width.
overflow | 'wrap'\|'nowrap'\| 'clip' | undefined | How to render overflow text. Overrides grid-level `overflow` option.
