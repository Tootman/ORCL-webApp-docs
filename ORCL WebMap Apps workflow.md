

# ORCL mapping WebApps workflow and overview




### Overview of App architecture 
![ORCLWEbApps overview - cloud](https://user-images.githubusercontent.com/6683043/73083670-7e7eb780-3ec3-11ea-93de-b103afaebd15.png)



There are three mapping webApps. Condition Surveying *Webapp, MapAdmin*, and *Client map*. The data sources, and their relationship to the webApps are shown in the diagram above.

**Condition Surveying WebApp** is a mobile first WebApp for assessing and for periodic checking  of Parks and OpenSpace Assets (and vegetation). All the data for the Condition Surveying app is stored in Firebase [Condition Surveying App documentation ('WebAppv3')](https:https://github.com/Tootman/WebAppv3/blob/master/README.md)

**MapAdmin** is a 'Desktop first' WebApp for creating GeoJson mapData from shp and importing mapData (and metadata) into Firebase. It can perform basic map queries such as  filtering map features for  Asset condition, or and description. It can Exporting Mapdata to csv. [Map Admin WebApp('WebmapDashboardv2') documentation](https://github.com/Tootman/WebMapDashboardv2/blob/master/README.md)

**Client Map** is an Interactive map displaying Assets and vegetation. Requires Log in. Click on a feature to see details, including any photo, and latest Condition survey update (also called 'related data') [Client Map documentation](https://github.com/Tootman/ClientMap/blob/master/README.md)

**Related Data** is the information collected by the ConditionSurveying WebApp (such as asset condition, with comments and photo). RelatedData is stored on in Firebase as  key value pair, using a key based upon the Asset's OJBECTID field value and it's geometry type (eg 1204Polygon)

Map Geospatial data is stored in a Firebase database in GeoJSON. If a dataset is very large (eg map data for an entire London Borough), then it is copied (split) into smaller 'child' maps. The conditionSurveying WebApp can then open a childMap, and write Related Data to it. A Firebase cloud function then copies that data to it's 'parent' map.

'Parent Map' MapData is imported into Mapbox as a 'Tileset'. The tileset is the datasource for the ClientMap MapData. The FIrebase Parent Map is the data source for the ClientMap's Related data. 

**Firebase** is a google cloud platform providing a no-sql database holding map data, and includes *firestore* , a cloud storage area serving image files (photos of the assets)

## Creating Maps 

#### Create Child Map shp files using QGIS ####

- Open QGIS
- Import polygon file to be used to create the split (clip) (eg 'wards' layer)
- Import the points. polyons and lines shp files to split up
- Copy layers to EPSG 4326 layers (
- Select vector>geoprocessing>clip with settings:
- - Input layer: points 4326 layer etc
- - clip layer: Polygon Layer to split by eg wards etc and select iterate over layer)
- For the 'clipped' box set name to geometry type ('points', lines' or 'polygons'), and select 'save to file' setting path to the clip
- Execute the 'Reorder-clipped-group-layers' python script (latest version in Trello), regenerating an ordered list of child map labels (eg ward names), in the order to correspond with the filename number ie point_0 is first in list. Modify the script to incorperate the list as values in the array variable named 'siteNames'

#### Upload a map or Childmap using MapAdmin

 - Open mapAdmin, login (login permission procedure is restricted to only DJS currently), and navigate to 'upload' tab. 
#### Creating a new project

- Open MapAdmin and load in a map

- Open MapAdmin Fill in project name eg Hounslow2018

 - Fill in project description
 - Fill in Related data reset date eg 1 jan 2019
 - Click 'create new project' button ( RelData hash and projectHash fields should  then automatically get populated)
#### Appending to existing project:

  - Paste in values for ProjectHash and relDataMapHash (find from inspecting Firebase database etc)
  - The project's condition rating system (and other related config), is fetched from the projects's /schema/formfields  - eg for Hounslow the grading system is [c1, c2, c3, c4, c5 & c6 etc]. Copy and paste the 

#### Uploading a new map:

  -  Open 'Import' tab and click 'Import  zip' button , select the zipped shp file for the childmap / map to be imported, and check that map loads as expected (tip: could use filename for map name eg name: Brentford, description 'Brentford ward',
  - Navigate back to Upload, and populate the map name and map description
  - Make sure RelDataMapHash and ProjectHash are both populated
  - Click upload map button
 - (You can check successful upload etc using WebApp - open project then open map)
- Repeat for each map in set.

### Creating a new Mapbox User profile

 - Each Mapbox user needs a MapboxUserProfile. Each user can access one 'Map' (the map the parent/master) map eg Whole of Hounslow Borough.
 - Currently, Mapbox does not use 'Projects' however it does use the 'User' FB node for setting up the Mapbox view. 

#### Instructions

 - Using an existing /User/ as a template, eg by exporting User node json from firebase, update the values to those of the new user and upload as follows:
 - *center* : desired center of the Map coords
 - *clickableLayers*: array of Mapbox layer names that can be clicked (including polygons and any optional layers eg veglayer)
 - etc

Push updated file to firebase/User/ (or create child node with a value of dummy, then import the file)

### Update Mapbox mapdata from updated Shp files:

(Updated 13/1/2020)

- Unzip provided archive.zip (shp files) (eg to 
D:\dan-s\Documents\qgis\hounslow_2018_survey\archive_2018_hounslow)
- created qgis project: (eg D:\dan-s\Documents\qgis\hounslow_2018_survey\hounslow_2018.qgs)
- Import / Drag in lines.shp, polyogns.shp and points.shp into QGIS
- Import /Drag in sites polygon (1 polygon per site ie site footprint or site bounds rectangle)
- import/drag in any optional ('selectable') layers (currently can only be a layer called 'vegetation')
- SaveAs above layers as geojson (or shp), as EPSG4326 (wsg84), giving unique filenames indicating project and geometry (and crs) eg hounslow2018-lines and hide original layers in QGIS, and add to map
- Note that For the SItes polygon / bounding box / site names - save as GeoJSON as will need to be imported as a dataset, as well as Tileset, and the crs member be need to be removed from the GeoJSON file before uploading to Mapbox
- Import new tilesets from the shp Asset (or update Tileset if appropriate), or if updating a map, REPLACE the Tileset
- (If creating a new map/style, download a style json, from another map to use as template if no pre-prepared style json is available) 
- To copy a style - eg when a style with substituted tilesets are needed then download the style and do a  Global search and replace tileset datasources etc with:
- - placeholderProjectName with projectName
- - placeholder points with newpointsTileset
- - placeholder lines with newLinesTileset
- - placeholder Polygons with newPolygonsPlaceholder
- Upload json style as new style

### Mapbox layers

(See Hounslow and Richmond as examples)
style: {projectname}layers eg hounslow-borough-parks

**Tilesets:** (This section needs updating  - does not currently make sense)
{projectname}points
{projectname}-lines (could be used for several line layers)
{projectname}-polygons

{projectName}-sites , from siteBoundingBoxes poly shp used for Site Names layer and Site bounding boxes site polygon

**Datasets:**

{projectName}-sites-hash from sitesboundingboxe  poly shp (see below section of SiteNames - as this is used for search Box site names)

### Site Names for Search Box
 - Ensure that an attribute called Site or Site_Name exists and is populated with the park names (or whatever is to be used in 'search for site' search box) (If a new field needs to be created then, in QGIS: make copied layer editable; Open Attribute Table, open Field Calculator, Output field name: 'Site_Name' ; Expression:"Park_Name" (or whatever the field is currently called), output field length:0
 -  Ensure one polygon per site name 
 -  Remove  crs data member from GeoJSON file
 - Import GeoJson file as dataset into Mapbox 
 - Use the resulting Id of the new dataset as the value for fb App/User/{projectId}/mapboxSitesDataSet

Client specific config: 
[link to specific config section](### Specific-client-map-config)







### Markers Layer

A markers layer, eg points of  unmapped Assets, Hazards, and notes, as collectecd by ConditionSurveyApp user, can become a mapbox style as follows:

1. Open the (Parent) map in MapAdmin
2. Export > export Markers 
3. Import the markers (a Geojson Feature collection) into Mapbox as TileSet and add layer to the map etc, (set as not-visable as would be a optional layer)

### Styling the map

The map is styled within Mapbox.

IF the style is based on existing style (ie copied as new style within Mapbox), layer datasources need to be updated.

Note that the sprite sheet has to be shared between all maps. However it can be modified, and exported and imported etc.

## Misc admin tasks

#### Renewing SSL certificate for ClientMap

- Needs to be done every three months

- log into db godaddy account via provided link - then to cpanel and DNS page
- (link: [godaddy link from David]

- (following guide [https://www.youtube.com/watch?v=ioQ_L2fjRcs&t=257s](https://www.youtube.com/watch?v=ioQ_L2fjRcs&t=257s)
- loginto sslforfree and generate ssl for:
- - *.orcl.co.uk orcl.co.uk
- Click verify manual domain (DNS) details
- update txt record values for _acme-challenge
- check propagation using [https://www.whatsmydns.net/#TXT/_acme-challenge.orcl.co.uk](https://www.whatsmydns.net/#TXT/_acme-challenge.orcl.co.uk)
- in sslforfree click on generate new certificate
- go to cpanel and renew(?) c
- certificate, pasting in new key values etc, then use on new site (for subdomains, if necessary)

## Create a new User - ?? is this section needed?
 - Only a logged in User can Write RelatedData to the WebApp, view WebApp images, and access the clientMap.
 - Create new Firebase User  by navigating to Firebase > Authentication>User>Add User (Email / password)
 - Set a  a new User node to /App/Users/ with UserUID as key and knickname (eg Dan_S) as value
 - Note that, if the User is also an EndUser / Client, using the Mapbox ClientMap then  Mapbox config is also under UserUID 

## Photo management
### Upload photos proc

 - Resize photos to thumbnail (eg 320x240), using {See trello attached .exe}, at say 70% quality, (14000 photos x 30kB)


 - Create fbStoragePhotosPath node in fb /Users/ , with path as value ie TowerHamlets/320x240/, if necessary
 - create photos folder in FB storage eg using FIrebase Dashboard

- open Powershell in working folder, holding the photos , and run:

gsutil -m cp -r *.jpg gs://fir-realtime-db-24299.appspot.com/{FbPhotosPath}
where  FbPhotosPath= eg TowerHamlets/320x240/

ie 
`gsutil -m cp *.jpg gs://fir-realtime-db-24299.appspot.com/TowerHamlets/320x240/`


 #### webApp
 - clicking on a Project button loads the project details having the button's ProjectId value
 -- THe project details contains the photosPath

#### EndUserMap
 - FbPhotosPath is in userProfile (from /User/)

## Firebase Cloud functions
 - Google cloud functions are used to copy RelatedData, (and Marker) data entries, from a childMap to it's Parent Map.  (The Child map is used by the WebApp, usually split into smaller datsets eg split into wards. The parentMap (eg a whole borough) is used by the EndUserMap 
 .firebaserc file contents:
    {
    "projects": {
    "default": "test-pwa-2434f"
    }
}
Cloud function github project * code:
[https://github.com/Tootman/webapp3cloudfunctions](https://github.com/Tootman/webapp3cloudfunctions)
### To run cloud functions:
- Run Windows powershell from within proj directory
Examplee script snippets:
to Deploy cloud function
```firebase deploy --only functions ```

Push data  to database database
 ```firebase database:push  /App/Users/test-area/messages -d  '{\"original\":\"make_upercase2!\"}'```

download (copy from) Firebase storage bucket to '/download/' local folder
 ```gsutil -m cp -r gs://fir-realtime-db-24299.appspot.com/hounslow/300x400 /downloads/```

set 
```firebase database:set  /App/Maps/-Limav7Ipfe8u_t40h9t/Markers  bedfont_markers.```

https://github.com/Tootman/webapp3cloudfunctions)

## Issue tracking and coding technical spec

See Github issues for tracking Issues, and wishlist for new features

See Trello for coding pattern and technical configs etc

## Troubleshooting

- WATCHOUT for non continuous array eg 1:,2: 3: 5 (leaving out element 4), in Firebase - this will result in one element with a a value of *empty* and break the code- 
~~~

~~~