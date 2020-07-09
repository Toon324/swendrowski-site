Published: 07/09/2020
Title: Making a rotating puzzle in FoundryVTT using Trigger Happy
PromoImageUrl: https://raw.githubusercontent.com/cswendrowski/swendrowski-site/master/input/img/tilepuzzle.gif
Category: foundryvtt
Intro: It's like Skyrim! Kinda.
---

## Modules used

### Trigger Happy
Trigger Happy is the bread and butter of how this works - it allows you to setup a Journal entry with triggers such as "When Actor A is clicked, execute Macro 1"

https://foundryvtt.com/packages/trigger-happy/

### Furnace
By default, Macros can't accept any sort of data, meaning they can only do one thing. Furnace has an "Advanced Macros" feature that allow us to pass in "arguments" that will let us rotate different puzzle tiles using the same Macro.

https://foundryvtt.com/packages/furnace/

## So you want a puzzle

The general structure of the puzzle looks like this: When a player clicks on a torch (their character pulls it, they magically enable it, whatever you want to narrate it as), it rotates the associated puzzle tile 90 degrees clockwise. Door unlocking is done manually by the GM once they connect a path, but in theory you could setup the macro to check if the tiles are in the correct rotation to open a door and unlock it automatically.

The first thing you'll need to do is create a new macro named "rotatetile":
```js
// args[0] contains the name of the time to rotate, such as "A1"
// Get the tile to rotate
var tile = canvas.tiles.placeables.filter(x => x.data.img.includes(args[0]))[0];

var toRotate = tile.data.rotation + 90;
// Tiles only rotate up to 360, so reset it if we exceed that
if (toRotate == 360) {
  toRotate = 0;
}

// Do the rotation
await tile.rotate(toRotate, 90);
return;
```

The Macro does the rotation, but we still need to know when and what to rotate.

For each torch, hide an invisible actor on the torch so the players have something to "click":

<img src="https://raw.githubusercontent.com/cswendrowski/swendrowski-site/master/input/img/puzzletriggeractors.PNG" class="img-responsive" />

Now that there are things to click, Trigger Happy will let us connect each Actor to a Macro call using the format `@Token[Token Name] @Trigger[click] @ChatMessage[/rotatetile TileName]`

Either add to an existing Trigger Happy Journal entry, or create a new one, and include the following:
```
@Token[A1] @Trigger[click] @ChatMessage[/rotatetile A1]

@Token[A2] @Trigger[click] @ChatMessage[/rotatetile A2]

@Token[A3] @Trigger[click] @ChatMessage[/rotatetile A3]

@Token[B1] @Trigger[click] @ChatMessage[/rotatetile B1]

@Token[B2] @Trigger[click] @ChatMessage[/rotatetile B2]

@Token[B3] @Trigger[click] @ChatMessage[/rotatetile B3]

@Token[C1] @Trigger[click] @ChatMessage[/rotatetile C1]

@Token[C2] @Trigger[click] @ChatMessage[/rotatetile C2]

@Token[C3] @Trigger[click] @ChatMessage[/rotatetile C3]
```

## All done!
