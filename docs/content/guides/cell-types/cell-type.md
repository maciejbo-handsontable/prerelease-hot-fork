---
title: Cell type
metaTitle: Cell type - JavaScript Data Grid | Handsontable
description: Use Handsontable's built-in cell types such as autocomplete, date, time, and more, for consistent UI across cell renderer, editor, and validator.
permalink: /cell-type
canonicalUrl: /cell-type
react:
  metaTitle: Cell type - React Data Grid | Handsontable
searchCategory: Guides
---

# Cell type

[[toc]]

## Overview
The cell type functionality enables you to customize the appearance and validate the cell(s) when applied.

## Usage

There are three functions associated with every table cell: [`renderer`](@/api/options.md#renderer), [`editor`](@/api/options.md#editor), and optionally [`validator`](@/api/options.md#validator). These functions are mostly used all together as they are strongly connected.

Example scenario - To store a date in a cell, you would:
* Use a [`renderer`](@/api/options.md#renderer) to display the date using appropriate formatting `dd/mm/yyyy`, `yyyy-mm-dd`, etc.
* Use an [`editor`](@/api/options.md#editor) that displays a calendar instead of the default text input, allowing the user to easily pick the right date.
* Use a [`validator`](@/api/options.md#validator) to check if the value entered by a user is valid.

Cell type is represented by a string i.e. `"text"`, `"numeric"`, `"date"`. Each string is internally mapped to functions associated with this type e.g., `"numeric"` type is associated with the following functions:

* `Handsontable.renderers.NumericRenderer`
* `Handsontable.editors.TextEditor`
* `Handsontable.validators.NumericValidator`


When Handsontable encounters a cell with the [`type`](@/api/options.md#type) option defined, it checks which cell functions this type refers to and uses them. For example, when setting the column type to `'password'`:

::: only-for javascript
```js
columns: [{
  type: 'password'
}]
```
:::

::: only-for react
```jsx
columns={[{
  type: 'password'
}]}
```
:::

the functions [`editor`](@/api/options.md#editor), [`renderer`](@/api/options.md#renderer), and [`copyable`](@/api/options.md#copyable) are automatically set as follows:

::: only-for javascript
```js
columns: [{
  editor: Handsontable.editors.PasswordEditor
  renderer: Handsontable.renderers.PasswordRenderer,
  copyable: false,
}]
```
:::

::: only-for react
```jsx
columns={[{
  editor: Handsontable.editors.PasswordEditor
  renderer: Handsontable.renderers.PasswordRenderer,
  copyable: false,
}]}
```
:::

## Available cell types

Handsontable comes with nine types:

* ["autocomplete"](@/guides/cell-types/autocomplete-cell-type.md) or `Handsontable.cellTypes.autocomplete`
* ["checkbox"](@/guides/cell-types/checkbox-cell-type.md) or `Handsontable.cellTypes.checkbox`
* ["date"](@/guides/cell-types/date-cell-type.md) or `Handsontable.cellTypes.date`
* ["dropdown"](@/guides/cell-types/dropdown-cell-type.md) or `Handsontable.cellTypes.dropdown`
* ["handsontable"](@/guides/cell-types/handsontable-cell-type.md) or `Handsontable.cellTypes.handsontable`
* ["numeric"](@/guides/cell-types/numeric-cell-type.md) or `Handsontable.cellTypes.numeric`
* ["password"](@/guides/cell-types/password-cell-type.md) or `Handsontable.cellTypes.password`
* ["select"](@/guides/cell-types/select-cell-type.md) or `Handsontable.cellTypes.select`
* ["time"](@/guides/cell-types/time-cell-type.md) or `Handsontable.cellTypes.time`
* "text" or `Handsontable.cellTypes.text`

The `text` cell type is the default type.

## Anatomy of a cell type

A cell type is a predefined set of cell properties. Cell type defines which [`renderer`](@/api/options.md#renderer), [`editor`](@/api/options.md#editor) or [`validator`](@/api/options.md#validator) should be used for a cell. They can also define any different cell property that will be assumed for each matching cell:

```js
Handsontable.cellTypes.registerCellType('custom', {
  renderer: Handsontable.renderers.TextRenderer,
  className: 'my-cell',
  readOnly: true,
  myCustomProperty: 'foo'
});
```

When used in Handsontable settings:

::: only-for javascript
```js
columns: [{
  type: 'custom'
}]
```
:::

::: only-for react
```jsx
columns={[{
  type: 'custom'
}]}
```
:::

Is an equivalent to defining them all:

::: only-for javascript
```js
columns: [{
  editor: false,
  renderer: Handsontable.renderers.TextRenderer,
  className: 'my-cell',
  readOnly: true,
  myCustomProperty: 'foo'
}]
```
:::

::: only-for react
```jsx
columns={[{
  editor: false,
  renderer: Handsontable.renderers.TextRenderer,
  className: 'my-cell',
  readOnly: true,
  myCustomProperty: 'foo',
}]}
```
:::

## Registering custom cell type

When you create a custom cell type, best practice is to assign it as an alias that will refer to this particular type definition.

This gives users a convenient way of defining which cell type should be used for describing cell properties. The user doesn't need to know which part of the code is responsible for rendering, validating, or editing the cell value. They do not even need to know that there are any functions at all. You can change the cell behaviour associated with an alias without changing the code that defines a cell's properties.

To register your own alias use `Handsontable.cellTypes.registerCellType()` function. It takes two arguments:

* `cellTypeName` - a string representing the cell type object
* [`type`](@/api/options.md#type) - an object with keys [`editor`](@/api/options.md#editor), [`renderer`](@/api/options.md#renderer), and [`validator`](@/api/options.md#validator) that will be represented by `cellTypeName`

If you'd like to register `copyablePasswordType` under alias `copyable-password`, you need to call:

```js
Handsontable.cellTypes.registerCellType('copyable-password', {
  editor: copyablePasswordEditor,
  renderer: copyablePasswordRenderer,
});
```

Choose aliases wisely. If you register your cell type under name that is already registered, the target function will be overwritten:

```js
Handsontable.cellTypes.registerCellType('password', {
  editor: copyablePasswordEditor,
  renderer: copyablePasswordRenderer,
});

// Now 'password' alias points to the newly created
// object, not Handsontable.cellTypes.password
```

Unless you intentionally want to overwrite an existing alias, try to choose a unique name. Best practice is to prefix your aliases with a custom name to minimize the possibility of name collisions. This is especially important if you want to publish your cell type as you never know what aliases have been registered by a user who uses your cell type.

```js
Handsontable.cellTypes.registerCellType('copyable-password', {
  editor: copyablePasswordEditor,
  renderer: copyablePasswordRenderer,
});
```
Someone might already registered such alias. It would be better use a unique prefix:

```js
Handsontable.cellTypes.registerCellType('my.copyable-password', {
  editor: copyablePasswordEditor,
  renderer: copyablePasswordRenderer,
});
```

To sum up, a well-prepared cell type object should look like this:

```js
class MyEditor extends Handsontable.editors.TextEditor {}

function customRenderer(instance, td, row, column, prop, value, cellProperties) {
  // ...renderer logic
}

function customValidator(query, callback) {
  // ...validator logic
  callback(/* Pass `true` or `false` */);
}

// Register an alias
Handsontable.cellTypes.registerCellType('my.custom', {
  editor: MyEditor,
  renderer: customRenderer,
  validator: customValidator,
  // You can add additional options to the cell type
  // based on Handsontable settings
  className: 'my-cell',
  allowInvalid: true,
  // Or you can add custom properties which
  // will be accessible in `cellProperties`
  myCustomCellState: 'complete',
});
```

## Using an alias

The next step is to use the registered aliases to enable users to easily refer to them without the need to know what the actual cell type object is. Here's an example of how you would use your cell definition:

::: only-for javascript
```js
const hot = new Handsontable(container, {
  columns: [{
    type: 'my.custom'
  }]
});
```
:::

::: only-for react
```jsx
<HotTable
  columns={[{
    type: 'my.custom'
  }]}
/>
```
:::

## Precedence

It is possible to define the [`type`](@/api/options.md#type) option together with options such as [`renderer`](@/api/options.md#renderer), [`editor`](@/api/options.md#editor) or [`validator`](@/api/options.md#validator). For example:

::: only-for javascript
```js
const hot = new Handsontable(container, {
  columns: [{
    type: 'numeric',
    // validator function defined elsewhere
    validator: customValidator
  }]
});
```
:::

::: only-for react
```jsx
<HotTable
  columns={[{
    type: 'numeric',
    // validator function defined elsewhere
    validator: customValidator
  }]}
/>
```
:::

We defined the[`type`](@/api/options.md#type) for all cells in a column to be `numeric`. We also defined a validator function directly. In Handsontable, cell functions that are defined directly always take precedence over functions associated with cell type, so the above configuration is equivalent to:

::: only-for javascript
```js
const hot = new Handsontable(container, {
  columns: [{
    renderer: Handsontable.renderers.TextRenderer,
    editor: Handsontable.editors.TextEditor,
    validator: customValidator
  }]
});
```
:::

::: only-for react
```jsx
<HotTable
  columns={[{
    renderer: Handsontable.renderers.TextRenderer,
    editor: Handsontable.editors.TextEditor,
    validator: customValidator
  }]}
/>
```
:::

There is one more way you can define the configuration using types:

::: only-for javascript
```js
const hot = new Handsontable(container, {
  // validator function defined elsewhere
  validator: customValidator,
  columns: [{
    type: 'my.custom'
  }]
});
```
:::

::: only-for react
```jsx
<HotTable
  validator={customValidator}
  columns={[{
    type: 'my.custom'
  }]}
/>
```
:::

Using [cascade configuration](@/guides/getting-started/configuration-options.md#cascading-configuration) we define a table with two columns, with [`validator`](@/api/options.md#validator) set to `customValidator` function. The s[`type`](@/api/options.md#type) of the first column is set to `password`. The `Password` cell type does not define a validator function:

```js
{
  renderer: Handsontable.renderers.PasswordRenderer,
  editor: Handsontable.editors.PasswordEditor,
  validator: undefined
}
```

Because `type: 'password'` is a more specific configuration for the cells in the first column than the `validator: customValidator`, cell functions associated with the `password` type take precedence over the functions defined on the higher level of configuration. Therefore, the equivalent configuration is:

::: only-for javascript
```js
function customValidator(query, callback) {
  // ...validator logic
  callback(/* Pass `true` or `false` */);
}

const hot = new Handsontable(container, {
  columns: [{
    renderer: Handsontable.renderers.PasswordRenderer,
    editor: Handsontable.editors.PasswordEditor,
    validator: undefined
  }, {
    renderer: Handsontable.renderers.TextRenderer,
    editor: Handsontable.editors.TextEditor,
    validator: customValidator
  }]
});
```
:::

::: only-for react
```jsx
function customValidator(query, callback) {
  // ...validator logic
  callback(/* Pass `true` or `false` */);
}

<HotTable
  columns={[{
    renderer: Handsontable.renderers.PasswordRenderer,
    editor: Handsontable.editors.PasswordEditor,
    validator: undefined
  }, {
    renderer: Handsontable.renderers.TextRenderer,
    editor: Handsontable.editors.TextEditor,
    validator: customValidator
  },
  ]}
/>
```
:::

## Built-in cell types example

The example below shows some of the built-in cell types, i.e. combinations of cell renderers and editors available in Handsontable. The example also shows the declaration of custom cell renderers, namely `yellowRenderer` and `greenRenderer`.

::: only-for javascript
::: example #example1
```js
const container = document.querySelector('#example1');
const colors = ['yellow', 'red', 'orange', 'green', 'blue', 'gray', 'black', 'white'];

const yellowRenderer = function(instance, td, row, col, prop, value, cellProperties) {
  Handsontable.renderers.TextRenderer.apply(this, arguments);
  td.style.backgroundColor = 'yellow';
};

const greenRenderer = function(instance, td, row, col, prop, value, cellProperties) {
  Handsontable.renderers.TextRenderer.apply(this, arguments);

  td.style.backgroundColor = 'green';
};

const hot = new Handsontable(container, {
  data: [
    { id: 1, name: 'Ted', isActive: true, color: 'orange', date: '2015-01-01' },
    { id: 2, name: 'John', isActive: false, color: 'black', date: null },
    { id: 3, name: 'Al', isActive: true, color: 'red', date: null },
    { id: 4, name: 'Ben', isActive: false, color: 'blue', date: null },
  ],
  colHeaders: true,
  licenseKey: 'non-commercial-and-evaluation',
  columns: [
    { data: 'id', type: 'text' },
    // 'text' is default, you don't actually need to declare it
    { data: 'name', renderer: yellowRenderer },
    // use default 'text' cell type but overwrite its renderer with yellowRenderer
    { data: 'isActive', type: 'checkbox' },
    { data: 'date', type: 'date', dateFormat: 'YYYY-MM-DD' },
    { data: 'color', type: 'autocomplete', source: colors }
  ],
  cell: [
    { row: 1, col: 0, renderer: greenRenderer }
  ],
  cells(row, col, prop) {
    if (row === 0 && col === 0) {
      this.renderer = greenRenderer;
    }
  }
});
```
:::
:::

::: only-for react
::: example #example1 :react
```jsx
import ReactDOM from 'react-dom';
import { HotTable } from '@handsontable/react';
import { registerAllModules } from 'handsontable/registry';
import 'handsontable/dist/handsontable.full.min.css';

// register Handsontable's modules
registerAllModules();

const ExampleComponent = () => {
  const colors = ['yellow', 'red', 'orange', 'green', 'blue', 'gray', 'black', 'white'];
  const yellowRenderer = function(instance, td, row, col, prop, value, cellProperties) {
    Handsontable.renderers.TextRenderer.apply(this, arguments);
    td.style.backgroundColor = 'yellow';
  };
  const greenRenderer = function(instance, td, row, col, prop, value, cellProperties) {
    Handsontable.renderers.TextRenderer.apply(this, arguments);

    td.style.backgroundColor = 'green';
  };
  const cells = function(instance, td, row, col, prop, value, cellProperties) {
    if (row === 0 && col === 0) {
      this.renderer = greenRenderer;
    }
  };

  return (
    <HotTable
      data={[
        { id: 1, name: 'Ted', isActive: true, color: 'orange', date: '2015-01-01' },
        { id: 2, name: 'John', isActive: false, color: 'black', date: null },
        { id: 3, name: 'Al', isActive: true, color: 'red', date: null },
        { id: 4, name: 'Ben', isActive: false, color: 'blue', date: null },
      ]}
      colHeaders={true}
      licenseKey="non-commercial-and-evaluation"
      columns={[
        { data: 'id', type: 'text' },
        // 'text' is default, you don't actually need to declare it
        { data: 'name', renderer: yellowRenderer },
        // use default 'text' cell type but overwrite its renderer with yellowRenderer
        { data: 'isActive', type: 'checkbox' },
        { data: 'date', type: 'date', dateFormat: 'YYYY-MM-DD' },
        { data: 'color', type: 'autocomplete', source: colors }
      ]}
      cell={[
        { row: 1, col: 0, renderer: greenRenderer }
      ]}
      cells={cells}
    />
  );
};

ReactDOM.render(<ExampleComponent />, document.getElementById('example1'));
```
:::
:::


## Empty cells

It's worth to mention that values such as `''` (empty string), `null` and `undefined` are considered empty values. You can use the `Handsontable.helper.isEmpty` method to check whether a value is considered empty. Cells with empty values are displayed in a similar way for most of the data types (see below).

:::tip
Please keep in mind that opening a cell with `undefined` and `null` values results in **overwriting** the original value with an empty string. Moreover, copying and pasting that values will result in pasting the empty string.
:::

::: only-for javascript
::: example #example2
```js
const container = document.querySelector('#example2');

const hot = new Handsontable(container, {
  licenseKey: 'non-commercial-and-evaluation',
  data: [
    ['empty string', '', '', '', '', ''],
    ['null', null, null, null, null, null],
    ['undefined', undefined, undefined, undefined, undefined, undefined],
    ['non-empty value', 'non-empty text', 13000, true, 'orange', 'password'],
  ],
  columnSorting: {
    sortEmptyCells: true
  },
  columns: [
    {
      columnSorting: {
        indicator: false,
        headerAction: false,
        compareFunctionFactory: function compareFunctionFactory() {
          return function comparator() {
            return 0; // Don't sort the first visual column.
          };
        }
      },
      readOnly: true,
    },
    {},
    {
      type: 'numeric',
      numericFormat: {
        pattern: '$0,0.00',
        culture: 'en-US' // this is the default culture, set up for USD
      },
    },
    { type: 'checkbox' },
    { type: 'dropdown', source: ['yellow', 'red', 'orange'] },
    { type: 'password' },
  ],
  preventOverflow: 'horizontal',
  colHeaders: ['value<br>underneath', 'type:text', 'type:numeric', 'type:checkbox', 'type:dropdown', 'type:password'],
});
```
:::
:::

::: only-for react
::: example #example2 :react
```jsx
import ReactDOM from 'react-dom';
import { HotTable } from '@handsontable/react';
import { registerAllModules } from 'handsontable/registry';
import 'handsontable/dist/handsontable.full.min.css';

// register Handsontable's modules
registerAllModules();

const ExampleComponent = () => {
  return (
    <HotTable
      licenseKey="non-commercial-and-evaluation"
      data={[
        ['empty string', '', '', '', '', ''],
        ['null', null, null, null, null, null],
        ['undefined', undefined, undefined, undefined, undefined, undefined],
        ['non-empty value', 'non-empty text', 13000, true, 'orange', 'password'],
      ]}
      columnSorting={{
        sortEmptyCells: true
      }}
      columns={[
        {
          columnSorting: {
            indicator: false,
            headerAction: false,
            compareFunctionFactory: function compareFunctionFactory() {
              return function comparator() {
                return 0; // Don't sort the first visual column.
              };
            }
          },
          readOnly: true,
        },
        {},
        {
          type: 'numeric',
          numericFormat: {
            pattern: '$0,0.00',
            culture: 'en-US'
          },
        },
        { type: 'checkbox' },
        { type: 'dropdown', source: ['yellow', 'red', 'orange'] },
        { type: 'password' },
      ]}
      preventOverflow="horizontal"
      colHeaders={['value<br>underneath', 'type:text', 'type:numeric', 'type:checkbox', 'type:dropdown', 'type:password']}
    />
  );
};

ReactDOM.render(<ExampleComponent />, document.getElementById('example2'));
```
:::
:::


Empty cells may be treated differently in different contexts, for example, the [ColumnSorting](@/api/columnSorting.md) plugin has `sortEmptyCells` option which is responsible for establishing whether empty cells should be sorted like non-empty cells.

## Related articles

### Related guides

- [Cell function](@/guides/cell-functions/cell-function.md)
- [Cell editor](@/guides/cell-functions/cell-editor.md)
- [Cell renderer](@/guides/cell-functions/cell-renderer.md)
- [Cell validator](@/guides/cell-functions/cell-validator.md)

### Related API reference

- Configuration options:
  - [`editor`](@/api/options.md#editor)
  - [`renderer`](@/api/options.md#renderer)
  - [`type`](@/api/options.md#type)
  - [`validator`](@/api/options.md#validator)
- Core methods:
  - [`getCellEditor()`](@/api/core.md#getcelleditor)
  - [`getCellMeta()`](@/api/core.md#getcellmeta)
  - [`getCellMetaAtRow()`](@/api/core.md#getcellmetaatrow)
  - [`getCellsMeta()`](@/api/core.md#getcellsmeta)
  - [`getCellRenderer()`](@/api/core.md#getcellrenderer)
  - [`getCellValidator()`](@/api/core.md#getcellvalidator)
  - [`getDataType()`](@/api/core.md#getdatatype)
  - [`setCellMeta()`](@/api/core.md#setcellmeta)
  - [`setCellMetaObject()`](@/api/core.md#setcellmetaobject)
  - [`removeCellMeta()`](@/api/core.md#removecellmeta)
- Hooks:
  - [`afterBeginEditing`](@/api/hooks.md#afterbeginediting)
  - [`afterGetCellMeta`](@/api/hooks.md#aftergetcellmeta)
  - [`afterGetColumnHeaderRenderers`](@/api/hooks.md#aftergetcolumnheaderrenderers)
  - [`afterGetRowHeaderRenderers`](@/api/hooks.md#aftergetrowheaderrenderers)
  - [`afterSetCellMeta`](@/api/hooks.md#aftersetcellmeta)
  - [`afterValidate`](@/api/hooks.md#aftervalidate)
  - [`afterRenderer`](@/api/hooks.md#afterrenderer)
  - [`beforeGetCellMeta`](@/api/hooks.md#beforegetcellmeta)
  - [`beforeRenderer`](@/api/hooks.md#beforerenderer)
  - [`beforeSetCellMeta`](@/api/hooks.md#beforesetcellmeta)
  - [`beforeValidate`](@/api/hooks.md#beforevalidate)
