# Sketch Script Fail

Version 3.8.3 (29802)

I’ve reviewed .sketch docs and tried many different solutions, but I couldn’t add a MSSliceLayer with the same frame as the selected layer. 

So here’s my failed attempt at creating a .sketch script. When I added MSSliceLayer to the current document it’s center point is the current document center point and not the selected layer.

Maybe, I’m missing something simple.

```javascript

var documentName = context.document.displayName();

var selectedLayers = context.selection;
var selectedCount = selectedLayers.count();

if (selectedCount == 0) {

  log('No layers are selected.');
  
} else {

  log('Selected layers:');
  for (var i = 0; i < selectedCount; i++) {
    var layer = selectedLayers[i];
    var sliceLayer = MSSliceLayer.alloc().init();

    var rect = MSRect.alloc().init();
    rect.x = layer.frame().x();
    rect.y = layer.frame().y();
    rect.width = layer.frame().width();
    rect.height = layer.frame().height();

    sliceLayer.frame = rect;

    context.document.addLayer(sliceLayer);
    
  }

};

```

###Solution
Thanks to [BohemianCoding](https://github.com/BohemianCoding/developer.sketchapp.com/issues/60):
 ```javascript

var documentName = context.document.displayName();

var selectedLayers = context.selection;
var selectedCount = selectedLayers.count();

if (selectedCount == 0) {

  log('No layers are selected.');
  
} else {

  log('Selected layers:');
  for (var i = 0; i < selectedCount; i++) {
    var layer = selectedLayers[i];

    var sliceLayer = MSSliceLayer.sliceLayerFromLayer(layer);
    context.document.addLayer(sliceLayer);
    
  }

};

```

As you can see this solution uses MSSliceLayer.sliceLayerFromLayer() which is not apart of the official docs, at least currently, but apart of MSSliceLayer’s unofficial [header](https://github.com/abynim/Sketch-Headers/blob/master/Headers/MSSliceLayer.h). 

###Extra
I've as noticed a few other things not working properly. Updating a MSSliceLayer.frame in order to add a 10px padding,
```javascript

MSSliceLayer.frame.scaleBy()

```
will update the frame.size, but not maintain its center point. So since I do have access to NSFoundation I can simply call [CGRectInset()](https://developer.apple.com/library/ios/documentation/GraphicsImaging/Reference/CGGeometry/index.html#//apple_ref/c/func/CGRectInset), which will returns a rectangle that is smaller or larger than the source rectangle, with the same center point, and update MSSliceLayer.rect.
```javascript

  var sliceLayer = MSSliceLayer.sliceLayerFromLayer(layer);
  sliceLayer.rect = CGRectInset(sliceLayer.frameForTransforms(), -10, -10)
  
```
