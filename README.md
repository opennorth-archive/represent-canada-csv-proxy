# Represent API: CSV Proxy

[Represent](https://represent.opennorth.ca/) is the open database of Canadian elected officials and electoral districts. It provides a [REST API](https://represent.opennorth.ca/api/) to boundary, representative, and postcode resources.

This repository proxies Google Sheets with elected officials' contact information and reformats the data for import into Represent.

**If this repository requires maintenance, it should be merged into [scrapers-ca](https://github.com/opencivicdata/scrapers-ca/) instead.**

## Getting Started

```
bundle
bundle exec rackup
```

### API

The only API endpoint is `/:id/:gid/:boundary_set`. For a Google Sheets URL of:

    https://docs.google.com/a/opennorth.ca/spreadsheets/d/7mOZ3yDOsmKJfg3s15S6GFEy6YAtFy461blJEemE81ML/edit#gid=059742683

* `:id` is the key `7mOZ3yDOsmKJfg3s15S6GFEy6YAtFy461blJEemE81ML`
* `:gid` is the parameter `059742683`
* `:boundary_set` is the slug of a boundary set in [Represent](http://represent.opennorth.ca/boundary-sets/?limit=0)

If `:boundary_set` is `census-subdivisions` or `census-subdivisions-and-divisions`, then an optional `sgc` query string parameter can be set to a two-digit [Standard geographic classification (SGC) code](http://www12.statcan.gc.ca/census-recensement/2011/ref/dict/table-tableau/table-tableau-8-eng.cfm), to limit the search for a matching Census subdivision or Census division to one province or territory.

### Data manipulation

The proxy:

* Maps between CSV headers and JSON fields
* Formats addresses, genders, and phone numbers
* Preserves unrecognized CSV headers

The proxy attempts to map the District ID or District Name to a boundary in Represent, in the following order:

1. If a District ID is four digits, the proxy maps it to a Census division boundary.
1. If a District ID is seven digits, the proxy maps it to a Census subdivision boundary.
1. If `:boundary_set` is `census-subdivisions` or `census-subdivisions-and-divisions`, the proxy attempts to map the District Name to a unique boundary within the boundary set, and raises an error if it can't.
1. If `:boundary_set` is anything else, the proxy slugifies the District Name and maps it to a boundary within the boundary set.

The proxy performs no validation that the boundary exists, except in the third case as a side effect.

Copyright (c) 2015 Open North Inc., released under the MIT license
