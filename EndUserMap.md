# End User Map (Client Map)

## Features

- View latest related data (collected condition survey data)
- View original data and photo
- Requires login to access map
- Optional layer overlay
- Common Asset types are reprented by symbols, and remaining asset types by black point/polygon and accompanying label
- Search by site name
- Responsive web design

## Architecture overview

- Map config data from firebase /user/ config
- Each Client (User) has own Mapbox style, dataset and Tilesets
- Map GeoData (map features) sourced from Mapbox tileset
- Map sites (searchable locations) map names sourced from box dataset (polygons), and map site features from same data  - but as tileset
- Tech stack: Javascript (es5) , webpack, Mapbox, Firebase, Bootstrap

## Requirements

- Asset name field has to be called 'Asset' (create attribute using QGIS if necessary)
- Requires connection to internet (no offline capability)
- Login credentials supplied by ORCL

## Patterns and functional Spec

- todo: (include: how layers are handled - onClick and curser, Login flow chart - signing in , obtaining AccessToken, downloading map, etc)

### Installation

- Install using node v11 (using nvm), but need to check if this is the case 

  `nvm install {repo}`

- If npm install produces ERR (probably will), try 

  ​	`npm install firebase --force`

- Can revert back to latest node v after install (is this right?)

### Bugs, issues and features wish list

See Github issues

#### Creating a new map and new user

See [Workflows](https://github.com/Tootman/ORCL-webApp-docs/blob/master/ORCL%20WebMap%20Apps%20workflow.md) document

#### Styling the map

See Workflow > Mapbox map styling



## Optional Layers

The list of available optional layers are set under Firebase /User/{UserID}/optionalLayers. Each optional Layer corresponds to a  mapbox layer, which is set to hidden in mapbox.

