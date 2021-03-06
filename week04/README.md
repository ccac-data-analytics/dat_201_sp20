# Week 4

## Agenda
0. [Announcements](#announcements)
1. [Talking about homework/readings](#readings)
2. [QGIS Practice](#qgis)
    * [Adding layers and counting points](#qgis1)
    * [Joining flat files with vector files](#qgis2)
3. [Homework/Readings](#homework)

## <span id="announcements">Announcements</span>

My department meeting is now happening on the day I was going to go to the mapping thing in Oakland (2/27). I'm really sorry. I'll email the people about maybe coming in, though.

We should also talk about our schedule.

## <span id="readings">Talking about homework</span>

This was possibly a very low-homework week. A 10-minute video&mdash;what did we think?&mdash;and getting QGIS up and running. Before we can go any further, let's make sure: does everyone have a functioning copy of QGIS? 

## <span id="qgis">QGIS Practice</span>
    
Note: we're doing all of this in class, together. I wrote it out in case that's easier for you, or you want to remind yourself how to do some of this later.

You're going to want to open GitHub on the browser _on the machine where you're running QGIS_. (Are you running it on the VM? Open it in Firefox on the VM.)

You might find this [really nice cheat sheet](https://north-road.com/qgis-3-0-shortcuts/) helpful.
    
### <span id="qgis1">Adding Layers and Counting Points - Together</span>

The question: The Forestry Division of the Department of Public Works has money to plant more trees. What neighborhoods would you tell them to prioritize?

1. Get these data sets, and save them onto your machine in a folder you've created to store all of your QGIS work (in Windows, how about making a QGIS folder under Documents?):
    * [Fixed up PGH neighborhoods shapefiles](../files/shapefiles/fixed_neighborhoods.zip) - right-click, save-as, and then unzip/extract the folder
    * [City of Pittsburgh Trees](https://data.wprdc.org/dataset/city-trees) (CSV)
1. Open QGIS, and start a new project (Project -> New; then do yourself a favor, and choose Project -> Save, and save it in the folder you've made for QGIS files).
1. (Optional, but I like it.) In the "Browser" area, find XYZ Tiles, drop it down, and add the OpenStreetMap layer to your map if you want by double-clicking or dragging onto your map.
1. Add the Neighborhood Shapefiles Layer (Layer -> Add Layer -> Add Vector Layer, and you'll want "fixed_neighborhoods.shp"; close the dialog box after the layer shows up in the Layers box)
1. Right-click on the "fixed_neighborhoods" layer, and choose "Zoom to Layer"
1. Have a look at the Attribute Table for the fixed_neighborhoods layer. (Right-click, or there's a button in the toolbar at the top of the screen. It looks a bit like a calendar. If you like shortcuts, it's F6.) Admire how much data we have, already.
1. Now, we have a LOT of data about trees in our CSV file. We don't want or need all of it. So it's time for a quick digression into data cleaning in Excel:
    * First off, give your file a better name (open the file, Save As something else &mdash; that way you have the original file, in case you need it, too). I use "pgh_trees." Don't put spaces into the filename.
    * Columns to keep: id, address, street, common_name, scientific_name, height, width, condition, neighborhood, tract, latitude, longitude - you can keep any others you think you might want to use later, but these are the ones I keep. (Keep note of how many columns are deleted/which are kept)
    * Now we're going to filter our data and remove the following types of trees: "(Blanks)", "Stump," "Vacant Site *", "Non-sufficient space." (Keep note of how many rows are deleted) 
    * Save again, and you can probably close the CSV for now.
1. Add the trees as a layer. (Layer -> Add Layer -> Add Delimited Text Layer; choose your csv file, and make sure the file format, geometry definition (point coordinates, x: longitude, y: latitude, Geometry CRS: WGS 84), and layer settings look good)
1. Check out the attribute table for pgh_trees (it'll take a second to load).
1. Take a second and visit Vector -> Analysis Tools -> Basic Statistics for Fields. We can run stats in our GIS software! Choose "height" as your field. After you've soaked up the statistical goodness*, close the dialog box. * Is it good? How do we feel about this?
1. Style the layers how you like. (At the top of the Layers box, you choose the "Open the Layer Styling panel" tooltip. "Single symbol" and "simple marker" are good choices, and you can just choose a color from the drop-down for now.)
1. Save your project.
1. Now, we're going to count our trees, to see how they are dispersed between neighborhoods:
    * We'll use a tool called "Count Points in Polygon," which is under Vector -> Analysis Tools
    * It should correctly identify your polygon layer (fixed_neighborhoods) and your points layer (mine is called "pgh_trees"). To make your life better, you'll probably want to give a more useful field name; I use "tree_count" and specify a savefile (ellipsis beside the "Count" field -> Save To File).
    * It will take a minute to run. That's fine.
    * And then it creates a new layer ("Count"), on top of all of your others. Examine the attribute table.
    * Rename the layer "tree_count." (Right-click on the layer, and Rename is an option.)
1. Number of trees is nice and all, but the neighborhoods aren't all equal sizes. I'd argue that density of trees is a more useful metric. Conveniently, we can add columns to our attribute table!
    * Open the attribute table for the tree_count layer.
    * Open the Field Calculator (the icon looks a bit like an abacus).
    * You want to create a new field. Call it "density_tr" (or whatever you like), and set the field type to Decimal Number (real).
    * In the center column, you want to open the Fields and Values option, and double-click tree_count to add it to the Expression box. Now hit the division symbol ("/"), and then double-click on "sqmiles" in the center column. Your expression should read "tree_count" / "sqmiles"
    * After you click OK, you should see the new column in your table. Go ahead and sort by that column (click on the title until it's sorted smallest to largest). ... Yeah.
    Before we fix this, go ahead and click the pencil icon to exit Edit Mode. It will ask if you want to save. You do.
1. Now we're going to fix this one neighborhood that doesn't have a square mileage. There are a few steps to do this:
    * It looks like there's no square mileage reported for this neighborhood. Honestly, it's missing a bunch of fields, but that's the one we need.
    * Highlight this row (and only this row), and then open the Field Calculator again.
    * Choose "Update existing field," this time, and below that choose "area." (We're going to fill the "area" column with square meters and then use that value to calculate square miles.)
    * Now we're going to use the Search box above the center column, and enter the word "area." Under Geometry, you should have "$area" as an option. Double-click that. Click "OK." Make sure your area column is in line with other areas.
    * Reopen the Field Calculator. This time we will update the sqmiles field.
    * Grab the area field from the Fields and Values drop-down, and multiply ("*") it by **3.86102e-7**. (Sorry, I haven't found a nicer way to do this.)
    * Did the density_tr field populate? If not, how would you populate it? Do it.
    * Click the pencil icon to exit Edit Mode.
    * Remember to deselect the row (there's an icon in the toolbar to Deselect All).
1. We want to be able to mouse over our map and see what neighborhood we're looking at, right? So. Right-click on the tree_count layer, and choose Properties.
Under Display, you can write HTML to show nicely-formatted pop-ups. To keep this simple, I recommend
      [% "hood" %] <br>
      [% "density_tr" %] trees/mi^2
(If the pop-ups don't show, you may have to turn them on under View -> Show Map Tips.)
1. OK, let's do a graduated color scheme for tree density. Open the Layer Styling panel again.
Switch from Single Symbol to Graduated, and set the column to "density_tr"
1. Choose a color ramp you like (I go with Greens for this map).
1. Choose a Mode (we're going to look at Equal Interval and at Quantile), and then click "Classify."

So. Which neighborhoods would you pick? What other factors might we consider?

### <span id="qgis2">Joining Flat Files with Vector Layers</span>

Looking at (one aspect of) the digital divide in Pennsylvania.

1. Get these data sets, and save them into your designated folder for QGIS files; extract both folders:
    * PA Census Tracts from [TIGER/Line Shapefiles](https://www.census.gov/geo/maps-data/data/tiger-line.html) - 2018, Web Interface, Census Tracts, Pennsylvania
    * [American FactFinder](https://factfinder.census.gov/) - Internet Access (under Advanced Search, Topics -> Housing -> Physical Characteristic), limit Geographies to Census Tracts in Pennsylvania (all) (B28002)
1. Start a new project (and then save it in your designated folder for QGIS files).
1. Add the Census Tracts shapefile as a layer.
1. Have a look at its attribute table.
1. Clean up the Internet Access (ACS_17_574_B28002_with_ann) file in Excel, to remove data we don't want. (Open it, Save As something meaningful, and then start making changes.)
    * It's vital that we keep the GEO.id2 field, but let's change it to GEO_id2.
    * Let's say we're looking at the estimated number of households without internet access, column HD01_VD13 (second to last column); we should keep that. We'll also want the estimated total number of households (HD01_VD01). We should keep those two columns, plus GEO_id2. We should also give them more useful names: total_households and no_internet_households.
    * We should also remove that explanatory row (row 2).
1. Now, we can add it as a delimited text layer. Unlike the tree data file, this one has no geometry. After it's added, change its name to something easier to work with, like "census_data"
1. To add the information from this layer to the attribute table of our census tracts, we need to perform what's called a "Join." Right-click on the census tracts layer, and choose "Properties," and then choose "Joins" on the left-hand menu.
1. Hit the plus sign.
1. Since we're in the Properties of the tracts layer, the join layer will be the census_data layer. We're combining these tables based on the census tract ID, so GEO_id2 will be our join field, and GEOID will be our target field.
1. Click OK, and go look at the attribute table now. (Aren't you glad we changed the layer name on our census data? That said, we could have changed the column label prefix in the layer join dialog box, too, but it's useful to have it match.)
1. Now we're going to add a column to hold the percentage of households without internet. (I called mine "no_net_per.") It's done just like the density of trees in the previous exercise, only we take the number of households without internet and divide by the total number of households in each tract. (Don't forget to make it a decimal type column, not a whole number type.)
1. After we add that column, sort it. Nulls aren't great, right? I propose we highlight those rows and use the Field Calculator to set them to zero.
1. Now we're going to make a graduated color map, just like we did in the first example.
1. We're also going to set the display values on mouse-over.

What can you tell me about the digital divide in PA? How about in Allegheny County?

## <span id="homework">Homework/Readings</span>

Work through [Joining Flat Files with Vector Layers](#qgis2) on your own. Bring a map and a data-backed assertion about the digital divide in PA or Allegheny County to class next week.

 ~~Look through the [PASDA](http://www.pasda.psu.edu/) data sets, and make a map and a data-based assertion that's interesting to you. If none of the data sets speak to you, please look at how many [oil and gas](http://www.pasda.psu.edu/uci/DataSummary.aspx?dataset=283) or [coal](http://www.pasda.psu.edu/uci/DataSummary.aspx?dataset=271) operations are being run in the [Chesapeake Bay watershed](http://www.pasda.psu.edu/uci/DataSummary.aspx?dataset=59) (or Allegheny County or any other subset of the state of interest to you). There are also [oil and gas water pollution control facilities](http://www.pasda.psu.edu/uci/DataSummary.aspx?dataset=284), if you'd like to look at those. Don't forget to cite your data sources, and keep a log of the changes you make, so that another data analyst could follow your steps.~~

~~You have enough tools to get started on a project using Census data, as well. Explore American Factfinder (or any other source of data we've covered in 102 or 201), and think about what kind of map you'd like to make for your mapping project. We're going to explore areas of interest at the beginning of next class, and I'm hoping groups will magically coalesce ... but failing that, we'll work together to figure something out.~~

### Readings (you've got two weeks for this one)

* [What's in a map?](https://www.gislounge.com/whats-in-a-map/)
* [Design principles for cartography](https://www.esri.com/arcgis-blog/products/product/mapping/design-principles-for-cartography/)