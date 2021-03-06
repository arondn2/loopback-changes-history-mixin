loopback-changes-history-mixin
===============

[![npm version](https://badge.fury.io/js/loopback-changes-history-mixin.svg)](https://badge.fury.io/js/loopback-changes-history-mixin) [![Build Status](https://travis-ci.org/arondn2/loopback-changes-history-mixin.svg?branch=master)](https://travis-ci.org/arondn2/loopback-changes-history-mixin)
[![Coverage Status](https://coveralls.io/repos/github/arondn2/loopback-changes-history-mixin/badge.svg?branch=master)](https://coveralls.io/github/arondn2/loopback-changes-history-mixin?branch=master)

Loopback mixin to generate a new model to save history changes by record of a model.

## Installation

`npm install loopback-changes-history-mixin --save`

## Usage

Add the mixins property to your `server/model-config.json`:

```json
{
  "_meta": {
    "sources": [
      "loopback/common/models",
      "loopback/server/models",
      "../common/models",
      "./models"
    ],
    "mixins": [
      "loopback/common/mixins",
      "../node_modules/loopback-changes-history-mixin",
      "../common/mixins"
    ]
  }
}
```

Add mixin params in model definition. Example:
```
{
  "name": "Person",
  "properties": {
    "name": "string",
    "email": "string",
    "status": "string",
    "description": "string"
  },
  "mixins": {
    "ChangesHistory": true
  }
}
```

In the above definition it will define the following:
- A model called `Person_history` with properties indicated in `fields`: `name`, `email`, `status` and `description`.
- Relation `Person` has many `history` (model `Person_history`, foreign key `_recordId`).
- Relation `Person_history` belongs `record` (model `Person`, foreign key `_recordId`).
- Properties `_version` and `_hash` as `string` in models `Person` and `Person_history`.
- Properties `_action` as `string` and `_update` as `date` in model `Person_history`.

Every time a change is made in a record of `Person`, it will be saved in `Person_history` a record with new values
and the following fields:
- `_version = <version code>`
- `_hash    = <hash code>`
- `_action  = <'create' or 'update' or 'delete'>`
- `_update  = <date of change>`

## Options

The mixin supports the following parameters:

 Name                 | Type                    | Default                | Optional | Description
----------------------|-------------------------|------------------------|----------|------------
 `fields`             | `array` or `string` `*` | `*`                    | No       | Array with the fields to version
 `modelName`          | `string`                | `${ModelName}_history` | No       | Name to history model
 `relationName`       | `string`                | `history`              | No       | Model has many history model relation name
 `relationParentName` | `string`                | `_record`              | No       | History model belongs to model relation name
 `relationForeignKey` | `string`                | `_recordId`            | No       | Foreign key for relations
 `versionFieldName`   | `string`                | `_version`             | No       | Field name to version code
 `versionFieldLen`    | `number`                | 5                      | No       | Length to `versionField`
 `hashFieldName`      | `string` or `false`     | `_hash`                | Yes      | Field name to hash code
 `hashFieldLen`       | `number`                | 10                     | No       | Length to `hashField`
 `actionFieldName`    | `string` or `false`     | `_action`              | Yes      | Field name to action name
 `updatedFieldName`   | `string` or `false`     | `_update`              | Yes      | Field name to update date

Notes:
- `hashFieldName` allow create a history change only if any property in fields was alter. If setup `hashFieldName: false` then a history change will be creates with update method called.
- If `hashFieldName` is `false` then `hashFieldLen` is. ignoored.

## Loopback methods

Generates create records
  - `Model.create`
  - `Model.updateOrCreate` (AKA `Model.upsert`)
  - `Model.findOrCreate`
  - `Model.replaceOrCreate`
  - `Model.upsertWithWhere` (view `Model.upsertWithWhere` section below)

Generates update records
  - `Model.updateOrCreate` (AKA `Model.upsert`)
  - `Model.replaceOrCreate`
  - `Model.upsertWithWhere` (view `Model.upsertWithWhere` section below)
  - `Model.replaceById`
  - `Model.prototype.save`
  - `Model.prototype.updateAttribute`
  - `Model.prototype.updateAttributes`
  - `Model.prototype.replaceAtributes`

Generates destroy records
  - `Model.prototype.delete` (AKA `Model.prototype.destroy`)
  - `Model.deleteById` (AKA `Model.destroyById`)

Unsupported methods
  - `Model.updateAll`
  - `Model.deleteAll` (AKA `Model.destroyAll`)

### Model.upsertWithWhere
To create history change with method `upsertWithWhere` option `instanceByWhere: true` must be passed:
```js
const where = { /* ... */};
const data = { /* ... */ };
Person.upsertWithWhere(where, data, { instanceByWhere: true });
```

## Troubles

If you have any kind of trouble with it, just let me now by raising an issue on the GitHub issue tracker here:

https://github.com/arondn2/loopback-changes-history-mixin/issues

Also, you can report the orthographic errors in the READMEs files or comments. Sorry for that, English is not my main language.

## Tests

`npm test` or `npm run cover`
