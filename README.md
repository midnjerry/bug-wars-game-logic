# Bug Wars Game Engine
The Bug Wars Game Engine contains an Interpreter for the Bug Wars AI Script language.  During gameplay, the Game Engine manages the Program Counter for each bug, executes commands, and processes results.

## Order of Operations
1. Conditionals (up to a maximum of 6) otherwise a bug action is NOOP
2. EAT - Mark all eating actons
3. Conditionals
4. MOVEMENT
5. JUMP
6. ATTACK
7. RESOLVE Marked squares by generating new bugInfos from food.

## Data Models
Description of the data models used in the requests and responses for the Game Engine API.

### Map
Represents the terrain of the arena.  In this example, a 5x5 map is passed.

#### Legend
`#` = wall
`_` = open space
`F` = food
`1` = SpawnPoint for species #1
`2` = SpawnPoint for species #2

```
1 _ _ _ _
_ # _ # _
_ _ F _ _
_ # _ # _
_ _ _ _ 2
```

```json
{
    "rows": [
        ["OPEN", "OPEN", "OPEN", "OPEN", "OPEN"],
        ["OPEN", "WALL", "OPEN", "WALL", "OPEN"],
        ["OPEN", "OPEN", "OPEN", "OPEN", "OPEN"],
        ["OPEN", "WALL", "OPEN", "WALL", "OPEN"],
        ["OPEN", "OPEN", "OPEN", "OPEN", "OPEN"]
    ],
    "spawns": [
        {
            "team": 1,
            "x": 0,
            "y": 0
        },
        {
            "team": 2,
            "x": 4,
            "y": 4
        }
    ],
    "food": [
        {
            "x": 2,
            "y": 2
        }
    ]
}
```
### Game
The Game model has all starting information for the game to be executed.  This includes:
* Map Terrain
* Starting locations of bugInfos (Spawn Points)
* Starting locations of food
* Object Code used to execute the given bug

```json
{
    "map": {
        "rows": [
            "(see above)"
        ],
        "spawns": [
            {
                "team": 1,
                "x": 0,
                "y": 0
            },
            {
                "team": 2,
                "x": 4,
                "y": 4
            }
        ],
        "food": {
            "x": "2",
            "y": "2"
        }
    },
    "bugInfos": [
        {
            "team": 1,
            "name": "AI NAME",
            "code": "23 0 1 3 5 0 3 2 4 21 3 4 5 1 2 3 8"
        },
        {
            "team": 2,
            "name": "Bug Slayer",
            "code": "20 4 5 2 3 5 5 2 1 3 5 21 4 5 2 4"
        }
    ],
    "ticks": 300,
    "seed": "<generated by backend, but not revealed to ui>"
}
```

### GameState
```json
{
    "tick": 1,
    "food": [
        {
            "x": 2,
            "y": 2
        }
    ],
    "bug": {
        "team": 1,
        "direction": "NORTH, EAST, SOUTH, WEST",
        "x": 3,
        "y": 4,
        "command": "attack, noop, turnRight, ...",
        "dying": "true/false"
    }
}
```

## POST /games
Launch a new game and get an array of 300 GameStates representing 5 minutes of gameplay.

### Request Body
```json
{
   "game": {"(see above)"}
}
```

### Response Body for a Draw
```json
{
  "game": {"(see above)"},
  "winners": [1,2],
  "result": "DRAW",
  "gamestates": ["(see above)"]
}
```

### Response Body for a Victory
```json
{
  "game": {"(see above)"},
  "winner": [1],
  "result": "VICTORY",
  "gamestates": ["(see above)"]
}
```

## GET /games/{id}/replay
View a replay of game with `id`

Success- 200
Invalid ID- 404

### Response Body for a Draw
```json
{
  "game": {"(see above)"},
  "winners": [1,2],
  "result": "DRAW",
  "gamestates": ["(see above)"]
}
```

### Response Body for a Victory
```json
{
  "game": {"(see above)"},
  "winner": [1],
  "result": "VICTORY",
  "gamestates": ["(see above)"]
}
```

## GET /games
Get a list of Played Games
```json
["(Array of Games -- see above)"]
```
