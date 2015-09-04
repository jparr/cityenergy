# City Energy Project Building Map System

This repository contains the code for a highly configurable system to map buildings according to various kinds of energy efficiency data, to make critical data about urban sustainability and conservation visible and accessible to a wide array of potential audiences. 

The site was commissioned by the [City Energy Project](http://www.cityenergyproject.org/), a joint effort of the Natural Resources Defense Council (NRDC) and the Institute for Market Transformation (IMT). The City Energy Project is a national initiative to create healthier and more prosperous American cities by improving the energy efficiency of buildings. Working in partnership, the Project and cities will support innovative, practical solutions that cut energy waste, boost local economies, and reduce harmful pollution.

In close collaboration with partners at the City Energy Project, [Stamen Design](http://stamen.com) and the [Ministry of Velocity](http://www.ministryofvelocity.com/) designed and built the system in summer 2015. Stamen a leading innovator in data visualization, with a long history of direct collaborations with industry leaders, universities, museums, and humanitarian organizations. **The Ministry of Velocity is...**

## Dependencies

### Software
This project uses gulp for build scripts.
Other dependencies are contained in `package.json` and `bower.json`.

City data is hosted on CartoDB. Each city data table is specified in its respective JSON file contained in src/cities.

#### Development

  Start webserver
  ```
    $ npm start
  ```

  Start livereload
  ```
    $ npm watch
  ```

### Static Assets

Source files are in /src. The compiled files are in /dist.
```bash
  gulp
```
to compile, and copy all site files to the /dist folder

## How do I install it?

  * clone the repo
  * make sure you have [node](https://nodejs.org/) and [bower](http://bower.io/) installed
  * in the root of the repo, run ```sudo npm install```
  * in the root of the repo, run ```bower install```
  * in a separate terminal window run ```npm start```
  * point your browswer to http://localhost:8080/

## How do I deploy it to the world?

For the test site, we have an app live on Heroku where we push changes as a separate git remote. The `dist` directory also serves as a static site that can be hosted on any basic web server.

## City configuration

Before configuring a new city installation, the city and Stamen will have determined the hosting environment, forked this repository for a specific city installation, created a CartoDB account and loaded initial data there, and made any needed adjustments to header image and background color.

Once that's done, making changes to the overall application setup is quite simple and can be done by anyone with basic ability to use a text editor and maintain formatting.

Each city has an installation file in ```cityenergy/src/cities/```. Below is a simplified example for Philadelphia. Each instance of ellipses (...) means that more of those items can and likely will appear.

The first section sets the city name, URL, basemap tiles, default zoom location/level, source CartoDB account. These would all be optimized at initial setup for each city but are unlikely to need to be changed afterward:
```json
    "url_name": "philadelphia",
    "name": "Philadelphia",

    "tileSource": "http://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}.png",
    "center": [...],
    "zoom": 11,
    "cartoDbUser": "cityenergyproject",
    "property_id": "portfolio_manager_id",
    "property_name": "property_name",
    "building_type": "primary_property_type___epa_calculated",
```
The next sections are where most of the detail work will happen.

First, we can set the popup fields that appear when you click on a building on the map:

```json
    "popup_fields": [
      {"field": "property_name", "label": ""},
      {"field": "address", "label": "Address: "},
      {"field": "primary_property_type___epa_calculated", "label": "Building Type: "},
      {"field": "year_built", "label": "Year Built: "},
      {"field": "property_floor_area_buildings_and_parking_ft", "label": "Square Feet: "},
      {"field": "energy_star_score", "label": "Energy Star Score: "},
      {"field": "site_eui_kbtu_ft", "label":"Site Energy Use: "},
      {"field": "source_eui_kbtu_ft", "label":"Source Energy Use: "},
      {"field": "total_ghg_emissions_mtco2e", "label":"GHG Emissions: "}
    ],
  ```

  Next, we can set the filter fields, where each block here defines either a numeric filter field on the left or a category filter menu in the top bar:

  ```json
    "map_layers": [
        {
            "title": "Energy Star Score",
            "field_name": "energy_star_score",
            "display_type": "range",
            "section": "Energy Efficiency",
            "range_slice_count": 30,
            "color_range": ["#F77156","#DAC04D","#6BB0A5"],
            "description": "Measure of a building's energy performance relative to similar properties nationwide on a scale from 1-100. A score of 50 is the median. Buildings scoring 75 or higher are considered high performing."
        },
        {
            "title": "Energy Use per Sq Ft",
            "field_name": "site_eui_kbtu_ft",
            "display_type": "range",
            "section": "Energy Efficiency",
            "range_slice_count": 30,
            "filter_range": {"max" : 400},
            "color_range": ["#6BB0A5","#DAC04D","#F77156"],
            "description": "Site Energy divided by property size in square feet",
            "unit": "kbtu/ft&sup2;"
        },
        ...
    ],
```

The two items above are numeric fields with `display_type` of `range`. These create and set up the histogram filters along the left of the map.

Here's an example of the configuration for the category filters that can be added to the top bar:
```json
        {
            "title": "Building Type",
            "field_name": "primary_property_type___epa_calculated",
            "display_type": "category",
            "section": "Building Info",
            "categories_to_display": 9
        }
```

Finally, the one section of the configuration that will need ongoing maintenance. Here, when a new year of data has been loaded into CartoDB, we need to specify the years, table name, and default layer. New elements here will automatically appear in the year selector in the top right of the interface.

```json
    "years": {
        "2014": {
            "table_name": "philadelphia_propertiesreported_csv",
            "default_layer": "energy_star_score"
        },
        ...
    }
}
```
Once you edit the file, we recommend checking the formatting with a service such as http://jsonlint.com/ to confirm that all the commas and brackets are in their correct spots.
