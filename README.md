castor-theme-sbadmin
======================

A Castor theme to visualize a synthesis on a corpus using pies and histograms, based on [SB Admin v2.0](http://startbootstrap.com/templates/sb-admin-2/).

Installation
------------

```bash
$ npm install castor-cli -g
$ git clone https://github.com/castorjs/castor-theme-sbadmin.git
```

Usage
-----

```bash
$ castor --theme ./castor-theme-sbadmin/ /path/to/data/repository
```

If you don't have a data repository, but already loaded data in mongodb, you
can use:

```bash
$ castor --theme ./castor-theme-sbadmin/ $PWD/data
```

Before that, you have to configure your mongo connection, by creating a
`./data.json` file containing something like:

```json
{
  "port": 3000,
  "collectionName" : "insu"
}
```

Or, if you prefer (assuming you use `insu` collection):

```bash
$ castor --theme ./castor-theme-sbadmin/ --collectionName insu
```

Configuration
-------------

To make charts appear on the dashboard, you have to configure them.

The configuration is done in the JSON file of
[castor](https://github.com/castorjs/castor-core) (e.g.`data.json`), 
it's a file with the same name as the data directory 
(besides that directory), appended with `.json`.

The whole dashboard configuration is done inside the `dashboard` key
of the JSON configuration file.

Each chart has to be described in the `dashboard.charts` key.
Each chart has a name (its key in the JSON).

Below is an example with an histogram (which key is `perYear`), and a pie
chart (which key is `perTheme`). There are two types of charts:
[`histogram`](#histogram) and [`pie`](#pie).

```json
{
  "theme": "/path/to/castor-theme-sbadmin",
  "customFields": {
    "Themes" : {
      "path" : "content.json.DiscESI",
      "separator" : ";"
    }
  },
  "dashboard" : {
    "charts": {
        "perYear": {
            "field": "content.json.Py",
            "type": "histogram"
        },
        "perTheme": {
            "field": "fields.Themes",
            "type": "pie"
        }
    }
  }
}
```

## Charts

### Chart types

#### histogram

Used to represent evolution of the number of documents along the time (so,
this field is often a publication year, or anything indicating a point in
time).

Possible configuration: [`size`](#size).

#### pie

Used to fill the pie chart quarters.

There are some configuration possible: [`size`](#size) of the pie, and 
position of the [`legend`](#legend).

#### horizontalbars

Used to display the number of documents associated to a field value (for 
example, for keywords: how many documents match a keyword?).

Possible configuration: [`size`](#size).

### Preferences

#### size

To specify the size of the pie, add the `size` key to your chart.
Then, you can follow the [C3's example](http://c3js.org/samples/options_size.html) to fill it.

Ex:

```javascript
"perTheme": {
  "field": "fields.Themes",
  "type": "pie",
  "size": {
    "height": 400
  }
}
```

You can add a `columns` property too, knowing that the display has a "width" of 12
columns (Twitter bootstrap).

Here is  an example where the pie should take half of the page's width:

```javascript
"perTheme": {
  "field": "fields.Themes",
  "type": "pie",
  "size": {
    "height": 400,
    "columns": 6
  }
}
```

If you need to separate two charts, you can add an offset before a chart, using
`offset` property. It is a number which represent the "width" of `offset` 
columns.

Below is an example where the horizontal bars should take 5 columns, with a
preceding offset of 1 column.

```javascript
"horizontalThemes": {
    "field": "fields.Themes",
    "type": "horizontalbars",
    "title": "Thèmes",
    "size": {
        "height": 420,
        "columns": 5,
        "offset": 1
    }
}
```

#### legend

To specify where you want the legend to be, add the `legend` key to your chart.
Then, you follow the [C3's example](http://c3js.org/samples/legend_position.html) to fill it.

There are currently only two positions: `bottom` and `right`.

Ex:

```javascript
"perTheme": {
  "field": "fields.Themes",
  "type": "pie",
  "legend": {
    "position": "bottom"
  }
}
```


### Field configuration

#### Simple configuration

To indicate which field is used by a chart, you have to specify it inside the can use the JSON configuration field.

These are used to point inside the mongodb document, using the dot notation.

Often, they are placed in the `content` field, or in `fields`.

Ex:

```javascript
"dashboard" : {
  "charts": {
      "perYear": {
          "field": "content.json.Py",
          "type": "histogram"
      },
      "perTheme": {
          "field": "content.json.DiscESI",
          "type": "pie"
      }
  }
}
```


### Multivalued fields

Maybe your fields are *multivalued*, for example, if you load `csv` files.

For example, in a `Keywords` columns, you have such values:

```
Dashboard; Nodejs; Github
Web; Dashboard; Statistics
```

The direct way, is to point to `content.json.keywords`, but that will
distinguish the `Dashboard` from the first row to the one from the second row.
Moreover, they will be bound to other keywords on the same row.

The solution is to add a *custom field* in the JSON configuration file:

```javascript
"customFields" : {
  "Keywords" :  {
    "content.json.Keywords",
    "separator" : ";"
  }
},
```

Then, you have to add 

```javascript
"dashboard" : {
  "charts": {
      "perYear": {
          "field": "content.json.Py",
          "type": "histogram"
      },
      "perTheme": {
          "field": "fields.Keywords",
          "type": "pie"
      }
  }
}
```

Here is an example with a normal field `Py` (Publication year, which
is unique in each row), and a multivalued one, `Keywords` (several 
keywords):

```javascript
"customFields" : {
  "Keywords" :  {
    "content.json.Keywords",
    "separator" : ";"
  }
},
"dashboard" : {
  "charts": {
      "perYear": {
          "field": "content.json.Py",
          "type": "histogram"
      },
      "perTheme": {
          "field": "fields.Keywords",
          "type": "pie"
      }
  }
}
```

## Documents table

In `/chart.html` pages, you can see a chart, and a table with documents. This table display the fields you chose to put in the `customFields` key.

Here is an example, displaying `Year`, `Title`, `Authors`, and `Keywords`:

```javascript
"customFields" : {
  "year"   : {
    "path" : "content.json.Py",
    "label": "Publication Year",
    "public": true
  },
  "title"  : {
    "path" : "content.json.Ti",
    "label": "Title",
    "public": true
  },
  "authors": {
    "path" : "content.json.Af",
    "label": "Authors",
    "public": true
  },
  "keywords" : {
    "path" : "content.json.DiscESI",
    "label": "Keywords",
    "public": true
  }
}
```

All *custom fields* which `public` key is set to `true` will be
present in the table.

By default, `public` key value is `false`.