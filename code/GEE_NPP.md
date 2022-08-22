## NPP DATA
```
var roi = table;
var year_list=ee.List.sequence(2000,2019);
year_list=year_list.map(function(num){
  var time=ee.Date.fromYMD(num, 1, 1)
  var year_image=ee.ImageCollection("MODIS/006/MOD17A3HGF")
                  .filterDate(time,ee.Date(time).advance(1,'year'))
                  .max();
  var year_npp=year_image.select('Npp');
  return  year_npp.addBands(ee.Image.constant(num).toFloat());                 
}
  )
var img_collection=ee.ImageCollection.fromImages(year_list);
var linearFit = img_collection.select(['constant', 'Npp'])
  .reduce(ee.Reducer.linearFit());
print(year_list)
print(img_collection)
var trendVis = {
  min: -100,
  max: 100,
  palette: [
"fff5f0","fee0d2","fcbba1","fc9272","fb6a4a","ef3b2c","cb181d","a50f15","67000d",
"f7fcf5","e5f5e0","c7e9c0","a1d99b","74c476","41ab5d","238b45","006d2c","00441b"
  ],
};
//Download the required call interface
var batch = require('users/fitoprincipe/geetools:batch')
batch.Download.ImageCollection.toDrive(img_collection,"result", {
scale: 500,//resoluation 500m
region: roi,                //study area
maxPixels:34e10,            //The value here is set larger to prevent overflow
type:"int16" });
Map.centerObject(roi);
Map.addLayer(linearFit.select('scale').clip(roi),trendVis);
```