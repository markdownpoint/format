# The studio environment

`studio.hdr` is the light on a slide's 3D objects when their World has no color. It is multiplied by the World's `intensity`. A World's `intensity` is 0 unless it is set, so a World with neither a color nor an intensity gives no light.

The file is an equirectangular HDR picture in the Radiance format. It contains linear light from every direction around the slide. Its top is up. Its middle column faces the slide's right (+x), and the column a quarter of the way from the left edge faces into the slide.

MarkdownPoint makes this light from three.js's `RoomEnvironment` and prefilters it with a blur of 0.04 radians. The file contains the sharpest level of that prefiltered light. A mirror reflects this level. The file's header names the three.js version it was made from.

In Blender, use the file as the World's Environment Texture, and set the Background node's Strength to the World's `intensity`. Convert the scene's axes as Blender's glTF importer does, so Y up becomes Z up. Blender also points the middle of an equirectangular texture toward +X, so the texture needs no rotation.
