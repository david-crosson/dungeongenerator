# Daggerfall Dungeon Generation
This is a 3D Dungeon Generator that I’ve been working on for a small roguelite game. The idea is heavily inspired by The Elder Scrolls II: Daggerfall from 1996, a game with twisting mazes. The game had a certain charm to it with the unapologetically enormous dungeons that you could get lost in for hours, yet at the same time seemingly simple approach to its layout, which seemed quite straight-forward and a fun challenge to attept at replicating.  

<a href="https://i.imgur.com/wwIm15J.mp4">![](https://raw.githubusercontent.com/david-crosson/dungeongenerator/main/turnaround.gif)</a>

So the gameplay that stuck out to me in Daggerfall was how the game separated rooms and corridors. Where rooms felt safe and familiar, corridors gave a sense of dread and unease. While in that game, dungeon segments were done by hand and then stitched together, I wanted to try my hand at doing something similar but with each piece completely randomised. 

Goals:  
• Explorable dungeon with corridors and rooms  
• No dead ends; every path should lead to a room  
• Allow dungeon to loop back into itself  
• Done in UE5, because I dig that and it's new and it's hot

So in order to do this, I felt that doing it with a 3D grid felt overkill. The idea is that, as long as every separate piece of the dungeon follows a grid size, there is no need to define the grid programmatically. Instead, each modular piece is given specific entry points where a connection can occur.  

## So how does this work
![](https://raw.githubusercontent.com/david-crosson/dungeongenerator/main/image3.png)  

So for starters, we need some 3D meshes that will make up our dungeon. These pieces are the ones that we will stitch together to make our layout. When a new tile is added to the dungeon, a random entry point from all of the previous tiles is selected from a list, and the new tile is placed to align with it so that it connects to the owning tile. Position and rotation of the new tile is set so that the two connection points coincide perfectly.

Then we check for any overlap, and if the new tile does not fit, we delete it and try again for X amount of times. If we cannot find a suitable match, the old entry point is removed from the list. If, however, we get no overlaps and the new tile fits in, it is go time. We can add the new tile to our list, and the old entry point is removed.  

![](https://raw.githubusercontent.com/david-crosson/dungeongenerator/main/image4.gif)  

Running this a couple of times gives us an endless maze of only corridors. Halfway there. Notice that since each mesh follows the imaginary grid, with entry points only allowed at specific points, we automatically get loops without having to code in the logic ourselves. Nice!

With this working, add dedicated rooms that serve as goals in the dungeon. They are marked specifically as unique and, if placed, cannot be removed during cleanup.

Once the dungeon has added a suitable amount of rooms, we begin the cleanup phase. This is so that we can remove those annoying dead-ends.  

![](https://raw.githubusercontent.com/david-crosson/dungeongenerator/main/image1.png)  

In this picture, none of the marked tiles lead to any point of interest and serve no purpose for the dungeon. To remove them, we check against each tile to see how many connections it has; if any tile ever only has one connection, it is removed. The exceptions are the main rooms and the starting room. The result is a dungeon with no dead ends, with the exception of the spontaneous loops.

There is always a chance that the dungeon generates loops far away from any point of interest, which is a dead-end in itself I suppose. There are multiple ways to fix this, maybe check how many steps you have to take to get to a point of interest. Not sure yet. Right now, I generally like what they offer, as loops allow the player to escape enemies pretty intuitively.

![](https://raw.githubusercontent.com/david-crosson/dungeongenerator/main/image2.png)  

With dead ends removed, we get a much more condensed and focused layout. All that remains is to go through all of the tile connections that have no connected room, and we close them off with walls. And this is the result. A bit of a maze currently, but some weight tuning on what geometry to spawn, we can get the type of layout we desire.

Furthermore, in order to add some variety, you can add different modules to the geometry and limit from what pool each room can connect to. This makes the dungeon a little less confusing, and this system can get further improved with a varied texture set that can get randomised as well.

## Conclusion
In exploring the dungeon, it is very easy to get lost, so it definitely needs some improvement in points of interest and just general clutter. But getting lost is part of the aesthetic I’m going for – a fine line if we don’t want the player to get frustrated. As it is though, the dungeon appeals to me with its simplicity. I’m looking forward to seeing how far one can take this kind of generation approach.
