# ConditionSurvey WebApp

---

### Description

**ConditionSurvey WebApp** is a browser-based Mapping Web App designed for GIS Geo-spatial data collection - (in particular, asset condition surveys), using smartphone, in the field.

### Features overview

- Collect and submit related data (Asset condition update data)
- Offline capability using ServiceWorker (PWA)
- MapTile caching for offline basemap
- Highlighting of not-yet surveyed features on map
- cloud storage of data, falling back to localStorage / cached data when Off-line
- Live update on all devices when a user adds Marker, or adds or modifies RelatedData record
- Annotate Map using Markers

### device requirements & notes

- GPS enabled smartphone running modern (supported) browser
- Rugged / waterproof casing recommended
- Capacitive stylus recommeded (eg when cold weather and wearing gloves)
- Recommend using 'mid-range' or 'high-end' smartphone since the App can be CPU intensive for larger datasets
- Use GPS location sparingly - it drains the device battery at a greater rate.
- Take a power block as backup  - as battery will typically not last a full day

### Bugs and Issue

All bugs issues, and feature requests are documented in github Issues 

### Dev notes

- Issue of how to handle multiple point features at exact same location - suggested solution - re-process map to force coincident features to be separated geographically - eg signs on post, position above each other to be spaced out say 10cm apart (maybe surrounding the mapped point, or linearly, say due north)
- enable html webpack-plugin to recompile index.html, disable the plugin plugin to allow Jest unit testing

##### Example user stories

- A a GIS surveyor working in-the-field I want to:
  - inspect features (assets) and submit a form recording it's condition, along with a timestamp, my userID, a Photo of the feature (optional), and any notes /comments (optional)
  - Using just my smartphone
  - Show my location relative to the map (using GPS)

### User requirement

- Must ensure no submitted data is 'lost'
- Should be usable even if 4G network connectivity is not good in some locations within the site.

### Functional specification (pseudocode)

    onSubmitRelatedData:
        copy_relatedDataRecord_to_localStorage
        if user is logged in then attempt to send RelatedDataRecord to cloud
            then set corresponding feature symbology to completed
    
    onLoadApp:
        display_mapData_held_in_local_storage_ifExists
        try to send RelatedDataRecords in local storage to cloud
    
    onRelatedDataRecord_Sucessfully_Sent_to_cloud:
        delete_local_RelatedDataRecord
    
    on_click_a_different_feature:
        set previous_feature_style back to original_style
        set current feature style to highlightStyle
        display_popup
    
    on_setup_features_layer:
        clear layer
        populaterelatedData
        forEachFeature:
            set style completed or not completed depending on corresponding relatedDataRecord
    
      on_populate_relatedData:
            forEach relatedDataRecord
                set relatedDataItem with keyname OJBECTID to most recent record
    
      on_feature_popup_click:
            Make a reference to the current feature available to the whole app
            open the Flyout window reading in data for the current feature

### WebApp Behaviour:

- Can only open a new map from the Cloud
- Automatically Stores a copy of the current map locally
- Opening the App loads in the locally stored map (if it exists)
- The map data is immutable.
- Related Data can be added (it cannot be removed or edited)

### Technology stack

- Leaflet - Mapping library API
- fontello - small set of svg icons)
- leafletKnn - measureing spatial distances between features or coordinates
- WebPack - producing minified es5 js bundle with some of the CSS embedded,
- Firebase database for Cloud storage of all data
- LocalStorage backup when offline ServiceWorker (using BoxWorker) for PWA capability,
- LocalForage + tilesdb for Offline Background slippy map Tiles

---

## User Documentation 

##Getting Started

#### Login

Only logged in users can submit Related data and view Asset photos. Login Credentials are supplied by ORCL

#### Opening a project and map

A project as a collection of maps for a client

Choose a map to open

Large datasets may cause the App to crash or freeze or slow-down on lower-spec phones - so where possible, use a 'childmap' (a map split into geographic areas eg a London Borough (parent map) split into  Wards(childmaps)

#### Map data caching and offline usage

Once a new map is retrived from the cloud, it is saved into the App () stored in the local device browser's 'localStorage'). If the App is reloaded is loads the map from Localstorage. If none exists then it retrieves it from the cloud.

RelatedData and Markers are retrieved via 'Firebase listeners'. The listener event is triggered when loaded ,and when a new RelatedData record is created elsewhere (by another user accessing the same map). 

#### Markers

Markers are point features created by the ConditionSurvey App user, intended for the following uses:

**Unmapped Asset** marker is used to indicate the presence of a map feature/Asset not currently on the map (eg a new Asset). Usually includes a photo

***Hazard*** marker indicate the presence and and location of a hazard eg  tree roots trip hazard. Usually includes a photo

***Comment*** marker geospatial general comment

Markers are loaded into the map via Firebase firebase listeners. The listener function is executed each time the App is loaded, and each time a marker is created (by the user or by another user using another device, working on the same map)

Markers are stored on Firebase under /{mapKey}/. 

