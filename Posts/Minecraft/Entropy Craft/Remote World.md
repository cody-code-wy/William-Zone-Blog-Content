[FOK-SETUP]: # ()

# World.isRemote

Sometimes a simple bug is actually very difficult to find because the cause of the bug actually makes so many thigs work unexpectedly. That is exactly what happened to me when I misinterpreted what `World.isRemote` does in minecraft Forge.

### The Bug

I figured that if the world in remote that the code in executing on the remote side, however the reverse in true. so `World.isRemote` means the world in **not** on the same side.

This caused a lot of confusion while working on Entropy Craft I had a block of code like the following
```Java
if ( !World.isRemote ) { // Here is the bug
  EntropyCraftMod.logger.debug("Skipping random tick on Client");
  return;
}
```
This should, in theory allow the code after it to only run on the server, at least that was my expectation.

Entropy Craft makes torches slowly burn out, so the light level gradually lowers. The code above is supposed to filter out the decay events so that the torch decays on the server, then sends a packet to the client so that it can update the light level.

The interesting failure here is that everything appears to work. The torch slowly burns out, and you can see it. Seems that everything is working as expected.

### The Problem

There was only one thing that did not work as expected, saving a world.

When a world was re-loaded all the torches went back to full brightness. What?

Looking around the code turns basically nothing up, all the methods to save NBT are overridden properly, the Tile Entity appears to exist properly.

One other odd thing is, the client never actually gets _any_ packets.

After a few hours I took to the Minecraft Forge forums ([thread](http://www.minecraftforge.net/forum/index.php/topic,41342.0.html))

### The Cause

All of my server-side code was running on the client!

This explains everything.

##### Bad Saves

Saves are done server-side, the server never actually decays the torch so its saved as a torch with 0 decay.

##### No Packets

Server -> Client packets must originate on the Server, obviously. Starting a Server -> Client packet on the client side does nothing at all.

This seems like a poor failure state. I would have probably figured this out if there was something in the log like
```
Warn: Server -> Client packet started on client side
```

#### Visible Decay

Only one odd thing, the torches actually did decay, it was visible from within the game. So why did it work?

Easy: On the server side you need to re-calculate the light just like on the client. Why? Because the server controls mob spawning. The **exact** same method is used on both sides to recalculate the light level.

So when I recalculated the light level to manage mob spawning, it updated the rendered light for the client.


### The Fix

If you remember that first piece of code, there was an if statement

```Java
if ( !World.isRemote )
```

All I needed to do was remove the `!`

```Java
if ( World.isRemote )
```

Magically everything suddenly worked correctly!
