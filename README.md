# Software 3D Rendering Pushed to it's Limits
Okay, so I know software rendering is horrible, but it's fun to program. All the issues are fun to deal with, and I generally know what I'm doing.
## "But why?" I hear you asking
So, I was designing a 3D game, and I thought, "Okay, let's try Software 3D Rendering." That was about a year ago, and I've made several prototype software 3D rasterizers, and learned that it's called rasterization. Here's some of the features I plan to add (3D side of things only):
1. Texturing (perspective correct, none of that affine crap)
2. Depth-buffering
3. Multi-threading (split the triangles between multiple threads, although memory is an issue, especially caching)
4. Shaders (screen-space only, however)
5. Lighting (based on geometry, might have to make the equations belong to chunks)
6. Transparency (actually correct transparency)
7. Mipmapping
Some things I will **not** add to the 3D portion of the engine:
1. Anti-aliasing (remember, this has to run CPU-side, no hardware acceleration)
2. Reflections (at least not realtime, I might add cubemaps)
3. Anything using `sqrt()`, `sin()`, or `cos()` functions too much.
4. Raytracing
Oh yeah, did I mention this is being developed solely in Java? No native libraries. Well, maybe. I might use some native libraries but as of right now, **100% Java**
## What game?
Yeah. It's mainly an engine I can use for anything 3D, but the whole thing that started it was a furry game. As for that, that's not what this repo is for.
## If you're so hyped about the 3D part of it, why are you calling it a game engine?
It has a lot of the lower-level classes built-in to it (that I programmed from scratch) that can be used to make your own game. It's like Unity but I don't want your money.
# Theoretical Performance
Time for me to address the elephant in the room. How the hell am I going to get a software 3D rasterizer to run 30+ (hell even 60+) FPS? I'll tell you. I have no clue. This project is more of a journey than an actual AAA game.
## Most Important Factors
The most important things that will cause slow-downs are listed here.
1. Resolution. I'm no dummy, I've lived with low-end graphics cards all my life. No matter your graphics quality (except maybe lighting quality), your resolution will really determine if you're actually playing a game or watching a slideshow. The game has two resolution variables: `3D Resolution` and `2D Resolution`. The 3D rasterization is written onto an image. The image has the resolution of the `3D Resolution`. This image is then scaled up to the 2D resolution, and then UI elements are drawn. Performance goes down by a power of 2 each resolution you go (assuming correct aspect ration).
2. Triangle count. Since I'm using the scanline method of drawing (remember, I don't want AA and I can figure out perspective coorect texture mapping, and even if I wasn't using this method of drawing triangles, this would still be an issue), each time we want to draw a triangle, we need to figure out a few variables beforehand. So, while drawing a triangle that takes up the whole screen is a big task, drawing a bunch of smaller triangles that take up the whole screen when combined is an even bigger task. There are two main methods I'm using to tackle this. Number one, backface culling, and number two, BSP. Number one is simple enough, if the triangle is not facing us (through some simple math involving normal vectors), why even draw it? Number two needs a bit more explaining. BSP, or **B**inary **S**pace **P**artitioning, is a method of storing a 3D space. It involves visleafs and PVS (**p**otentially **v**isible **s**et), and it's basically just a whole scheme to reduce overdraw. If there's no way we can see this room from where we currently are, then don't draw it. Doom, Quake, Quake 2, GoldSrc, Source Engine all use this method. When storing all of the world triangles, we store all of the ones in the same room next to eachother in the array, so we can just specify that "we want to draw room 2," which is just triangles ranged from *x* to *y*.
