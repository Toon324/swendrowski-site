Published: 07/09/2020
Title: Making a rotating puzzle in FoundryVTT using Trigger Happy
PromoImageUrl: https://c10.patreonusercontent.com/3/eyJwIjoxfQ%3D%3D/patreon-media/p/post/35893869/664c75b13369403ca84ec648259329b8/1.jpg
Category: foundryvtt
Intro: It's like Skyrim! Kinda.
---

<img src="https://raw.githubusercontent.com/cswendrowski/swendrowski-site/master/input/img/tilepuzzle.gif" class="img-responsive" />

Advanced Macro: rotatetile
```js
var tile = canvas.tiles.placeables.filter(x => x.data.img.includes(args[0]))[0];
var toRotate = tile.data.rotation + 90;
if (toRotate == 360) {
  toRotate = 0;
}
await tile.rotate(toRotate, 90);
return;
```

Trigger Happy:
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
