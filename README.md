# Map List Pro - Register Track fix

## Introduction

This is a rough outline to as to how to tweak map-list-pro to enable users to
register track loations

## Files to Edit

* /var/www/html/wp-content/plugins/MapListPro/js/maplistfront.js

Lines ~512:
```javascript
    // Listen for user click on map to close any open infowindows
    google.maps.event.addListener(self.map, "click", function () {
        self.infowindow.close();
    });
```

Were basically going to use this even listener to handle our click event on the
map. When we receive an event we will call the google maps api to add a marker
and instruct it to remove any markers on right click.

Change the above lines to this:
```javascript
    var EDITABLE = 9999     // Constant used to detect that we want to edit this map.
    var marker;             // A global used to detect existence of old markers.
    var latbox = $("#lat"); // The location box to populate (using contact 7)
    var lonbox = $("#lon");

    // Listen for user click on map to close any open infowindows
    // and then add our own marker.
    google.maps.event.addListener(self.map, "click", function (event) {
        self.infowindow.close();
        //ddaviso
        if (mapObject.options.locationsperpage == EDITABLE) {
            console.log(event.latLng);
            placeMarker(event.latLng);
            latbox.val(event.latLng.k);
            lonbox.val(event.latLng.B);
        }
    });
    
    // Listen for user click on map to remove any markers
    if (mapObject.options.locationsperpage == EDITABLE) {
        google.maps.event.addListener(self.map, "rightclick", function () {
            removeMarker();
            latbox.val("");
            lonbox.val("");
        });
    }
   
    function placeMarker(location) {
        if ( marker ) {
            marker.setPosition(location);
        } else {
            marker = new google.maps.Marker({
                position: location,
                map: self.map
            });
        }
    }
        
    function removeMarker(location) {
        if ( marker ) {
            marker.setMap(null);
            marker = undefined;
        } 
    }
```

## Enabling Edit Mode

This is simply done by adding `locationsperpage="9999"` to our `maplist`
shortcode under `Pages`. Example below:

```
[maplist locationstoshow="116,117" showdirections="false" hidecategoriesonitems="true" hideviewdetailbuttons="true" expandsingleresult="false" streetview="false" showdirections="false" simplesearch="false" country="Location:" hidefilter="true" hidesort="true" viewstyle="maponly" locationsperpage="9999"]
```
