Drop da Bomb
============

![screenshot](/screenshot.png)

## How to build

On linux, in the folder where the source is:
```
qmake && make
```

On windows, download the Qt SDK, launch QtCreator, and open the "bomb.pro" file in the project, and click the pretty play button to the left.

### Default protocol 

```
PLAYERS
0 1,1 // ID x,y
2 3,9 // ID x,y
ENDPLAYERS
BOMBS
5,8 7 // x,y ticks left before kaboom
ENDBOMBS
X 15 // Your x-coordinate
Y 1 // Your y-coordinate
HEIGHT 13 // Map-height
WIDTH 17 // Map-width
MAP
+++++++++++++++++
+.....+###+.#...+
+++##...++.##.+++
+######.#.##....+
+##++.+.#.+.++#.+
+.#+.#.#++#.#+#.+
++.##.+...#.###++
+#++#++#+++#.++#+
+##...##+..#####+
+...+.#..###+...+
+.+#..#++..##.+.+
+.+###+##.+...+.+
+++++++++++++++++
ENDMAP
```

---

### JSON Protocol

After connecting, emit `JSON\n` to receive JSON objects instead of TEXT mode. The latter will require you to write your own parser - wich can be a good and a bad thing, depending on you :)

On each game tick the server emits one or more JSON-objects, depending on what happened the last tick.

`status update` - Contains all essential information your player will need to interact with the surroundings.

```JSON
{
    "type": "status update",
    "players": [
        {
            "id": 0,
            "x": 1,
            "y": 1
        }
    ],
    "bombs": [
        {
            "x": 2,
            "y": 1,
            "state": 0
        }
    ],
    "x": 10,
    "y": 1,
    "height": 13,
    "width": 17,
    "map": [
        "++++++++",
        "+..####+",
        "+.#####+",
        "+######+",
        "+######+",
        "+#####.+",
        "+####..+",
        "++++++++"
    ]
}
```

--

`dead` - You've been killed, and cannot expect more data until next round

```JSON
{ "type": "dead" }
```   

--

`end round` - There's only one player left (for your sake I hope it's you)

```JSON
{ "type": "round end" }
```

---

### The map

This example map can you add into the maps-folder in your game-folder. It'll be parsed automatically and you can use it to play on. Be creative, and test as many map combinations as possible to be sure your AI can cover all kinds of resistance! :)

```
480     Number of ticks
8x8     Mapsize in XxY
++++++++
+p__###+
+.#####+
+######+
+######+
+#####.+
+###__p+
++++++++

+     Walls that can not be destroyed
#     Walls that can be destroyed
.     Grass
_     Floor
p     Player spawn
```

When you are playing, the map looks a bit different. All walkable tiles have been converted to a dot (`.`), so the map above would now look like the one below, really not much of a difference.

```
++++++++
+...###+
+.#####+
+######+
+######+
+#####.+
+###...+
++++++++
```

---

### The bombs

Watch out! They sting!

On a serious note though, not too much special about them except for the `state` property. This tells you how many ticks the bomb has left to live. At a state of 0, the bomb will explode in a horizontal and vertical direction, covering the total of 5 tiles vertically and horizontally creating a + sign.

---

### Playing the game!

When you are connected to the server, you most likely wan to set your bots name. This is done by emitting `NAME RandomBot\n` to the server.

When you have joined a game you have the possibility to interact in 5 ways; navigating in 4 directions, and **dropping da bomb**!

```
"LEFT\n"
"RIGHT\n"
"UP\n"
"DOWN\n"
"BOMB"\n"
```

Easy, right? :)

Last but not least, the bots can throw shit at each other! For instance `SAY Puny humans!\n`

---

### Example of a connection

```
> - you
$ - server

You connect to the server
> "JSON\n"
> "NAME RandomAI\n"
Game starts
$ The server emits the JSON-package
> "BOMB\n"
$ The server emits the JSON-package
> "LEFT\N"
$ The server emits the JSON-package
> "SAY I'm just gonna hang out here for a bit...\n"
$ { "type": "dead" }

Now make your own bot, and try to make it do a better job than ours :)
```

**Remember:** Always end a command with `\n`

---

### Keymap

```
f5     Restarts round
space  Pause
esc    Stop game 

Arrowkeys for controlling the human player, and backspace for dropping bombs.
```

---

If you feel you need to test your bot, you can download a testbot created by Patrick Tørresvold [here](software.2rsvold.com/DemoMiner.zip).
