# sketchScriptFail

Here’s my failed attempt at creating a .sketch script. The script was supports to create a MSSliceLayer with the same frame as the selected layer. However, when I add MSSliceLayer to the current document it’s center point is the current document center point and not the selected layer.

I’ve reviewed .sketch docs and tried many different solutions, but I couldn’t add a MSSliceLayer with the right frame correctly. 

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
