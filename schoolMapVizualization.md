School Ranking Vizualization
================
Emilia Pawlowska
2024-01-15

For the final assignment I chose topic “3. Analyzing number of schools”.
The aim of the project is to analyze the number and location of high
schools in Gdynia along with information about the year of their
establishment and their place in the “Pomorski Ranking Liceów
Ogólnokształcących 2023” prepared by the portal Perspektywy. We want to
create a leaflet map and show schools as a point with pop up menu where
the marker color corresponds to their rank in the ranking (gold, silver
or bronze).

The method of visualizing high schools in Gdynia on a map, as presented
in the project, can be very valuable for primary school students seeking
information about prospective schools.

Ranking site:
<https://2023.licea.perspektywy.pl/2023/rankings/ranking-pomorski>

Other information were collected from schools home pages.

<!--Installing libraries-->

# Creating data for analyzing high schools in Gdynia

- latitude and longitude of the school
- year of establishing
- place in the ranking
- rank (Quality mark in Perspektywy Ranking)

In case of the vector with ranks I decided to use numerical value where:
1 - gold, 2 -silver, 3- bronze.

``` r
schools_names <- c("III Liceum Ogólnokształcące im. Marynarki Wojennej RP", 
                   "VI LO im. Wacława Sierpińskiego", 
                   "X Liceum Ogólnokształcące", 
                   "Katolickie Liceum Ogólnokształcące im. Św. Jana Pawła II",
                   "II LO im. Adama Mickiewicza", 
                   "XIV LO z Oddziałami Dwujęzycznymi im Mikołaja Kopernika", 
                   "I LO im. Zasłużonych Ludzi Morza",
                   "IX LO im. Marszałka Józefa Piłsudskiego",
                   "XVII LO",
                   "Ogólnokształcące Liceum Jezuitów Z Oddziałami Dwujęzycznymi Im Św. Stanisława Kostki")

ranking_places <- c(1, 7, 12, 17 ,10, 23, 37, 35, 24, 36)

year_of_establishment  <- c(1950, 1972, 1953, 1992, 1931, 1993, 1948, 1991, 2019, 1937)

ranks <- c(1, 1, 1, 2, 1, 2, 3, 3, 2, 3)

longitudes <-c(18.544909252828344,
               18.540935474697612,
               18.53672274788308,
               18.509271985362744,
               18.519644278602872,
               18.466448898992436,
               18.51625368474876,
               18.55039783955798,
               18.428238980864435,
               18.52564368421164)

latitudes <-c(54.507172618186885,
             54.50304147439088,
             54.51109308175149,
             54.55285195157312,
             54.519311780444525,
             54.54236267453898,
             54.49857389963467,
             54.47838490037935,
             54.48848516237254,
             54.51566908702809)
```

### Creating a dataframe

Creating a dataframe with 6 columns: School, Latitude, Latitude, Year,
Place, Rank.

``` r
schools_df <- data.frame(School = schools_names,
                         Latitude = latitudes,
                         Longitude = longitudes,
                         Year = year_of_establishment,
                         Place = ranking_places,
                         Rank = ranks)
```

# Map of schools in Gdynia

## Creating custom icons and a map using Leaflet

Firstly, I found the coordinates of Gdynia for setView() so that the map
view would be set in the right place. I decided to use addAwesomeMarkers
to mark schools as points because it allows me to customize icons. For
customization, I created a getColor function, which assigns a color to
the marker depending on the school’s rank. I also added a legend for the
map’s readability. Thanks to the popup, after clicking on the marker
information about name, city, year, and place in the ranking is
displayed. The value for City is static because the project includes
only schools from Gdynia.

``` r
getColor <- function(schools_df) {
  sapply(schools_df$Rank, function(Rank) {
  if(Rank <= 1) {
    "orange"
  } else if(Rank == 2) {
    "lightgray"
  } else {
    "darkred"
  } })
}

gdynia_coordinates <- c(54.5, 18.43)


leaflet() %>%
  setView(lng = gdynia_coordinates[2], lat = gdynia_coordinates[1], zoom = 11) %>%
  addProviderTiles("OpenStreetMap.Mapnik") %>%
  addAwesomeMarkers(
    data = schools_df,
    lng = schools_df$Longitude,
    lat = schools_df$Latitude,
    icon=awesomeIcons(
        markerColor = getColor(schools_df)
    ),
    popup = paste0(
      "<b>School name: </b>", schools_df$School, "<br>",
      "<b>City: </b>", "Gdynia" ,"<br>",
      "<b>Year: </b>", schools_df$Year, "<br>",
      "<b>Ranking place: </b>", schools_df$Place,"<br>"
    )
  ) %>%
   addLegend(
    position = "bottomright",
    colors = c("orange", "lightgray", "darkred"),
    labels = c("Gold", "Silver", "Bronze"),
    title = "\"Perspektywy\" Ranking"
  )
```

<div class="leaflet html-widget html-fill-item" id="htmlwidget-7abff8b974099c106095" style="width:672px;height:480px;"></div>
<script type="application/json" data-for="htmlwidget-7abff8b974099c106095">{"x":{"options":{"crs":{"crsClass":"L.CRS.EPSG3857","code":null,"proj4def":null,"projectedBounds":null,"options":{}}},"setView":[[54.5,18.43],11,[]],"calls":[{"method":"addProviderTiles","args":["OpenStreetMap.Mapnik",null,null,{"errorTileUrl":"","noWrap":false,"detectRetina":false}]},{"method":"addAwesomeMarkers","args":[[54.50717261818689,54.50304147439088,54.51109308175149,54.55285195157312,54.51931178044453,54.54236267453898,54.49857389963467,54.47838490037935,54.48848516237254,54.51566908702809],[18.54490925282834,18.54093547469761,18.53672274788308,18.50927198536274,18.51964427860287,18.46644889899244,18.51625368474876,18.55039783955798,18.42823898086444,18.52564368421164],{"icon":"home","markerColor":["orange","orange","orange","lightgray","orange","lightgray","darkred","darkred","lightgray","darkred"],"iconColor":"white","spin":false,"squareMarker":false,"iconRotate":0,"font":"monospace","prefix":"glyphicon"},null,null,{"interactive":true,"draggable":false,"keyboard":true,"title":"","alt":"","zIndexOffset":0,"opacity":1,"riseOnHover":false,"riseOffset":250},["<b>School name: <\/b>III Liceum Ogólnokształcące im. Marynarki Wojennej RP<br><b>City: <\/b>Gdynia<br><b>Year: <\/b>1950<br><b>Ranking place: <\/b>1<br>","<b>School name: <\/b>VI LO im. Wacława Sierpińskiego<br><b>City: <\/b>Gdynia<br><b>Year: <\/b>1972<br><b>Ranking place: <\/b>7<br>","<b>School name: <\/b>X Liceum Ogólnokształcące<br><b>City: <\/b>Gdynia<br><b>Year: <\/b>1953<br><b>Ranking place: <\/b>12<br>","<b>School name: <\/b>Katolickie Liceum Ogólnokształcące im. Św. Jana Pawła II<br><b>City: <\/b>Gdynia<br><b>Year: <\/b>1992<br><b>Ranking place: <\/b>17<br>","<b>School name: <\/b>II LO im. Adama Mickiewicza<br><b>City: <\/b>Gdynia<br><b>Year: <\/b>1931<br><b>Ranking place: <\/b>10<br>","<b>School name: <\/b>XIV LO z Oddziałami Dwujęzycznymi im Mikołaja Kopernika<br><b>City: <\/b>Gdynia<br><b>Year: <\/b>1993<br><b>Ranking place: <\/b>23<br>","<b>School name: <\/b>I LO im. Zasłużonych Ludzi Morza<br><b>City: <\/b>Gdynia<br><b>Year: <\/b>1948<br><b>Ranking place: <\/b>37<br>","<b>School name: <\/b>IX LO im. Marszałka Józefa Piłsudskiego<br><b>City: <\/b>Gdynia<br><b>Year: <\/b>1991<br><b>Ranking place: <\/b>35<br>","<b>School name: <\/b>XVII LO<br><b>City: <\/b>Gdynia<br><b>Year: <\/b>2019<br><b>Ranking place: <\/b>24<br>","<b>School name: <\/b>Ogólnokształcące Liceum Jezuitów Z Oddziałami Dwujęzycznymi Im Św. Stanisława Kostki<br><b>City: <\/b>Gdynia<br><b>Year: <\/b>1937<br><b>Ranking place: <\/b>36<br>"],null,null,null,null,{"interactive":false,"permanent":false,"direction":"auto","opacity":1,"offset":[0,0],"textsize":"10px","textOnly":false,"className":"","sticky":true},null]},{"method":"addLegend","args":[{"colors":["orange","lightgray","darkred"],"labels":["Gold","Silver","Bronze"],"na_color":null,"na_label":"NA","opacity":0.5,"position":"bottomright","type":"unknown","title":"\"Perspektywy\" Ranking","extra":null,"layerId":null,"className":"info legend","group":null}]}],"limits":{"lat":[54.47838490037935,54.55285195157312],"lng":[18.42823898086444,18.55039783955798]}},"evals":[],"jsHooks":[]}</script>
