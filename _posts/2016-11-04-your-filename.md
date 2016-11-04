---
published: true
layout: post
date: '2016-11-04 07:15:14 -0400'
categories: data
---
I used optical character recognition to convert a pdf into text, named entity recognition algorithm to parse the text for places, Google Maps' geocoding API to pull latitude / longitude coordinates and Carto to map everything. All of code is available [here](http://nbviewer.jupyter.org/github/willgeary/janejacobs/blob/master/Notebook.ipynb).

A quick overview of my steps...

### Find the pdf

![fig](https://raw.githubusercontent.com/willgeary/janejacobs/master/Images/Cover.png)

![address to lat lon](http://i.imgur.com/nm55glO.gif)

# Geocoding with Google Sheets Tutorial

You can geocode addresses to lat lon coordinates and vice versa using google sheets and a custom googlescript. This can be useful when you are collaborating on datasets and want a quick way to generate coordinates for a map. This tutorial will walk you through the steps necessary for doing something like this:

![address to lat lon](http://i.imgur.com/nm55glO.gif)

# 1. Get the geocoding macro google script source code

We will use the source code found [here](https://github.com/nuket/google-sheets-geocoding-macro/blob/master/Code.gs). Copy the entire chunk of google script code below. 

```{eval = FALSE}

function getGeocodingRegion() {
  return PropertiesService.getDocumentProperties().getProperty('GEOCODING_REGION') || 'us';
}

/*
function setGeocodingRegion(region) {
  PropertiesService.getDocumentProperties().setProperty('GEOCODING_REGION', region);
  updateMenu();
}
function promptForGeocodingRegion() {
  var ui = SpreadsheetApp.getUi();
  var result = ui.prompt(
    'Set the Geocoding Country Code (currently: ' + getGeocodingRegion() + ')',
    'Enter the 2-letter country code (ccTLD) that you would like ' +
    'the Google geocoder to search first for results. ' +
    'For example: Use \'uk\' for the United Kingdom, \'us\' for the United States, etc. ' +
    'For more country codes, see: https://en.wikipedia.org/wiki/Country_code_top-level_domain',
    ui.ButtonSet.OK_CANCEL
  );
  // Process the user's response.
  if (result.getSelectedButton() == ui.Button.OK) {
    setGeocodingRegion(result.getResponseText());
  }
}
*/

function addressToPosition() {
  var sheet = SpreadsheetApp.getActiveSheet();
  var cells = sheet.getActiveRange();
  
  // Must have selected 3 columns (Address, Lat, Lng).
  // Must have selected at least 1 row.

  if (cells.getNumColumns() != 3) {
    Logger.log("Must select at least 3 columns: Address, Lat, Lng columns.");
    return;
  }
  
  var addressColumn = 1;
  var addressRow;
  
  var latColumn = addressColumn + 1;
  var lngColumn = addressColumn + 2;
  
  var geocoder = Maps.newGeocoder().setRegion(getGeocodingRegion());
  var location;
  
  for (addressRow = 1; addressRow <= cells.getNumRows(); ++addressRow) {
    var address = cells.getCell(addressRow, addressColumn).getValue();
    
    // Geocode the address and plug the lat, lng pair into the 
    // 2nd and 3rd elements of the current range row.
    location = geocoder.geocode(address);
   
    // Only change cells if geocoder seems to have gotten a 
    // valid response.
    if (location.status == 'OK') {
      lat = location["results"][0]["geometry"]["location"]["lat"];
      lng = location["results"][0]["geometry"]["location"]["lng"];
      
      cells.getCell(addressRow, latColumn).setValue(lat);
      cells.getCell(addressRow, lngColumn).setValue(lng);
    }
  }
};

function positionToAddress() {
  var sheet = SpreadsheetApp.getActiveSheet();
  var cells = sheet.getActiveRange();
  
  // Must have selected 3 columns (Address, Lat, Lng).
  // Must have selected at least 1 row.

  if (cells.getNumColumns() != 3) {
    Logger.log("Must select at least 3 columns: Address, Lat, Lng columns.");
    return;
  }

  var addressColumn = 1;
  var addressRow;
  
  var latColumn = addressColumn + 1;
  var lngColumn = addressColumn + 2;
  
  var geocoder = Maps.newGeocoder().setRegion(getGeocodingRegion());
  var location;
  
  for (addressRow = 1; addressRow <= cells.getNumRows(); ++addressRow) {
    var lat = cells.getCell(addressRow, latColumn).getValue();
    var lng = cells.getCell(addressRow, lngColumn).getValue();
    
    // Geocode the lat, lng pair to an address.
    location = geocoder.reverseGeocode(lat, lng);
   
    // Only change cells if geocoder seems to have gotten a 
    // valid response.
    Logger.log(location.status);
    if (location.status == 'OK') {
      var address = location["results"][0]["formatted_address"];

      cells.getCell(addressRow, addressColumn).setValue(address);
    }
  }  
};

function generateMenu() {
  // var setGeocodingRegionMenuItem = 'Set Geocoding Region (Currently: ' + getGeocodingRegion() + ')';
  
  // {
  //   name: setGeocodingRegionMenuItem,
  //   functionName: "promptForGeocodingRegion"
  // },
  
  var entries = [{
    name: "Geocode Selected Cells (Address to   Lat, Long)",
    functionName: "addressToPosition"
  },
  {
    name: "Geocode Selected Cells (Address from Lat, Long)",
    functionName: "positionToAddress"
  }];
  
  return entries;
}

function updateMenu() {
  SpreadsheetApp.getActiveSpreadsheet().updateMenu('Geocode', generateMenu())
}

/**
 * Adds a custom menu to the active spreadsheet, containing a single menu item
 * for invoking the readRows() function specified above.
 * The onOpen() function, when defined, is automatically invoked whenever the
 * spreadsheet is opened.
 *
 * For more information on using the Spreadsheet API, see
 * https://developers.google.com/apps-script/service_spreadsheet
 */
function onOpen() {
  SpreadsheetApp.getActiveSpreadsheet().addMenu('Geocode', generateMenu());
  // SpreadsheetApp.getActiveSpreadsheet().addMenu('Region',  generateRegionMenu());
  // SpreadsheetApp.getUi()
  //   .createMenu();
};
```

# 2. Open Tools -> Script Editor in new google sheet

![](https://i.imgur.com/nkQisew.png)

# 3. Paste the above code into the script
Paste the entire section of code into the google script editor, replacing the starter code that was originally there. Click the save button to save this script in your google sheet.
![](https://i.imgur.com/WTRXTE0.png)

# 4. Refresh your google sheet. You may need to close out and re-open

# 5. A new tab called 'Geocode' should appear in your menu
It it doesn't appear, try closing out of the google sheet and reopening it.
![](https://i.imgur.com/AGO85q2.png)

# Now you can do magic

# 6. Select column of addresses and two columns to the right of them
This tells the Geocode macro where your addresses exist and where the lat/lon coordiates should be output.
![](https://i.imgur.com/VtHTXUL.png)

# 7. Navigate to Geocode -> Geocode Selected Cells (Address to Lat, Long)

![](https://i.imgur.com/8kps7bU.png)

# Magic

![](http://i.imgur.com/fbVjAFt.gif)

# Note: You can also reverse geocode

![](http://i.imgur.com/vjFYOjv.gif)

Thanks for reading! I hope this is helpful.

Will Geary


