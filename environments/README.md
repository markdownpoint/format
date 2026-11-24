# The environment maps

A World with no color lights a slide's 3D objects with the map its `environment` names, `studio` unless it names another. The map's light is multiplied by the World's `intensity`. A World's `intensity` is 0 unless it is set. A World with neither a color nor an intensity gives no light.

Each map is an equirectangular HDR picture in the Radiance format, 1024 by 512, named after the map: `studio.hdr`, `photo-studio.hdr`, `loft.hdr`, `sky.hdr`, and `sunset.hdr`. It contains linear light from every direction around the slide. Its top is up. Its middle column faces the slide's right (+x), and the column a quarter of the way from the left edge faces into the slide.

`studio.hdr` is MarkdownPoint's own studio light. MarkdownPoint makes this light from three.js's `RoomEnvironment` and prefilters it with a blur of 0.04 radians. The file contains the sharpest level of that prefiltered light. A mirror reflects this level. The file's header names the three.js version it was made from.

The other four maps are made from Poly Haven's CC0 maps: Studio Small 09, Brown Photostudio 02, Kloofendal 48d Partly Cloudy (Pure Sky), and Venice Sunset. Each is scaled so that its mean light over the sphere is the studio's. So a World of one intensity gives about as much light with any map. No color channel is above 65000. So each map fits in a half-float texture. Only the sun in `sky.hdr` is dimmed by this. Its map's scale makes up the light it loses. MarkdownPoint uses these four files as they are. It doesn't blur them.

In Blender, use the file as the World's Environment Texture, and set the Background node's Strength to the World's `intensity`. Convert the scene's axes as Blender's glTF importer does. Y up becomes Z up. Blender also points the middle of an equirectangular texture toward +X. The texture needs no rotation.
