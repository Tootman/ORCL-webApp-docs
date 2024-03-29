# MapAdmin

## version: 0.9.01 - not yet ready - not released

## Component relationship



![component relationships](https://user-images.githubusercontent.com/6683043/73085624-e08cec00-3ec6-11ea-8ddd-26c884d4c90d.png)

## Description and overview

Provides a Browser based dashboard with MapView, for managing ORCL mapdata for Local authority owned parks recreation areas and conservation area, intended for use by surveyor GIS managers or staff

## Features

- Import a zipped set of shapefiles as a map
- filterable and sortable tabular view of features with their RelatedData
- highlight filtered set of features on a map
- Inspect explore Map data in a tree view of nodes
- Export mapdata CSV or as ShapeFile set
- Upload Dataset to the cloud database, as a new map
- User Not logged in can access one set of webpages (Read only), user logged in can access additionally MapAdmin page (read and write to database)
- set map bounds (FlyTo) to a selected feature on the table

## Bugs / issues

- large dataset can freeze UI while busy - how to interrupt
- how to report 'busy' status to user while canvas is busy when redrawing large datasets on Leaflet map
- Visual style and layout - replace MapAdmin tabs with menu
- Add company branding /logo /visual style
- Address 'bundle much too large' message
- Shp 'condition' and height properties case sensitivity issue
- Refactor removeNaNPropertyValues

## features todo / wishlist

See Github issues

#### Notes

- Connect API to access User permissions on page components
- Leaflet object shared throughout Application

## Functional specification

#### onMapLoad

    Retrieve Map data from Firebase Database into MapAdmin State
    forEach record in RelatedData
        Inject most recent RelatedData into MapData feature matching ObjectID
    forEachFeature
        if hasProperty 'highlightOnMap' and is True then use HighlightedStyle
        else use unhighlightedstyle

#### onSelectAll_clicked

    for EachRow in FilteredSetOfRows
      // Row Index corresponds directly to index of feature in Map GeoJson Data
      set highlightOnMap property of MapDataFeature element RowIndex to TRUE

#### onSelectNone_clicked

    forEachFeature in map
        set highlightOnMap property to FALSE

#### onTableRowClick:

    toggle the value of mapAdmin.state.geojson.features(tableRow.index).properties.highlight_on_map
    // then the change of state causes react to refresh style prop callback forEachFeature

## How it it built - technology stack

- IMPORTANT - npm install only seems to work using node v11 (Use NVM)
- Leaflet Mapping API
- ReactJS v 16.3 (with Webpack via Create-react-App)
- Firebase database for Cloud storage
- Bootstrap 4
- 

---

## Installation

- **IMPORATANT: install requires node v11** (use nvm to select node ver) - come back to this issue
- npm install from github repo
- need to install firebase manually 'install firebase --force' (Come back to this issue)



## Example User Stories

- As a GIS user / client end-user I want to be able to respond to a customer enquiry, (from my office computer), asking me where abouts in a park they can find their memorial bench (or memorial tree), by intuitively searching/inspecting an interactive WebMap

- As a GIS user / client end-user I want to easily find the information on who owns (or manages) a specific feature that I have identified on the WebMap (eg a specific boundary wall.

- (As a 'friend of park' (or as member of the public?) I want to identify the location of my memorial bench (or memorial tree), from an easily-usable WebMap, from my phone, tablet or computer

#### Proposed map design behavour for 'who owns/manages what' map

Start with map containing all features of the park Iie basic Leaflet - Click feature on map showing all features over OpenMap baselayer to view popup infoBox containing details inc who Owns/manages, (partly solved in WebApp, however would need to search table)

#### For map for searching for a specific memorial etc

    Start with empty map (with minimal detail eg basemap with park bounds?) with search bar - generate a dropdownlist of fuzzy results from search, and display (and zoom to), selected (clicked) result, on map.  - maybe use google mymap?

#### Combination of above two maps