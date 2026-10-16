# The studio environment

`studio.hdr` is the light a World with no color gives a slide's 3D objects. The World's `strength` multiplies it.

The file is an equirectangular HDR picture in the Radiance format. It holds linear light from every direction around the slide. Its top is up. Its middle column faces the slide's right (+x). The column a quarter of the way from the left edge faces into the slide.

MarkdownPoint makes this light from three.js's `RoomEnvironment`. The app prefilters it with a blur of 0.04 radians. The file records the sharpest level of that prefiltered light. A mirror reflects that level. The file's header names the three.js version it was made from.

In Blender, use the file as the World's Environment Texture. Set the Background node's Strength to the World's `strength`. Convert the scene's axes as Blender's glTF importer does. Y up becomes Z up. Blender also faces the middle of an equirectangular texture toward +X. So the texture needs no rotation.
