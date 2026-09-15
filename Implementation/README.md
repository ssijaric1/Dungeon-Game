# DungeonGame

A pathfinding visualiser built as a game. You are dropped into a randomly
generated 10x10 dungeon and have to reach the exit with enough gold. Six
classical search and planning algorithms can then solve the same dungeon while
you watch them explore it, so the difference between them is something you see
on the grid rather than something you read about.

Built on the natID SDK.

## The dungeon

Each generated dungeon places the player somewhere on the left column, the exit
somewhere on the right, and scatters five rewards, five bandits and five mines.
The grid starts hidden and is revealed as you move.

| Tile | Effect |
|---|---|
| Reward | +10 gold |
| Bandit | halves your gold |
| Mine | asks you a quiz question, a wrong answer costs 5 gold |
| Exit | ends the run, but only counts as a win with at least 20 gold |

Reaching the exit with less than 20 gold ends the game without a win, which is
what makes the problem interesting: the shortest path is usually not the right
path.

The mine questions are drawn from an artificial intelligence course, covering
computer vision, neural networks, genetic algorithms, swarm optimisation and
rule based systems.

## Algorithms

Pick one from the dropdown and it replays its own search over the current
dungeon, drawing explored nodes and the final path, with a panel showing its
heuristic and complexity alongside the measured execution time.

| Algorithm | Notes |
|---|---|
| Breadth first search | shortest path in an unweighted grid, no heuristic |
| Depth first search | no optimality guarantee, shown for contrast |
| Dijkstra | least cost path over the tile costs (reward 0, mine 8, bandit 15) |
| A* | cost plus Manhattan distance, optimal because the heuristic is admissible |
| Greedy best first | heuristic only, fast and often wrong |
| MDP value iteration | the only one that treats the mine as uncertain, succeeding 70 percent of the time, rather than as a fixed cost |

The MDP solver treats gold as part of the state, so its policy is defined over
position and gold together, and it will happily take a longer route to clear the
20 gold threshold. The other five plan over position alone. Watching value
iteration pick a different route from A* on the same dungeon is the point of the
whole thing.

Playback has start, pause, single step and a speed control, so a search can be
walked one expansion at a time.

## Installing

Prebuilt installers for Windows, macOS and Linux are on the
[releases page](https://github.com/ssijaric1/ProjAI_DungeonG_Piralic_Smjecanin_Sijaric_/releases).

| Platform | File | Install |
|---|---|---|
| Windows | `dungeonGame-win.zip` | unzip, run the `.exe` (keep the `.msi` beside it) |
| macOS | `dungeonGame-macOS-Silicon.zip` or `dungeonGame-macOS-Intel.zip` | unzip, drag to Applications, first launch needs right click then Open |
| Linux | `dungeonGame-linux.zip` | unzip, then `sudo apt install ./dungeonGame.deb` |

Use `apt install` rather than `dpkg -i` on Linux, since dpkg will not pull in
the GTK dependencies. The macOS bundle is unsigned, which is why the first
launch needs the right click.

## Building from source

Needs the natID SDK at `~/natID.SDK` and GTK 4.

```bash
cmake -S . -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j$(nproc)
```

The binary lands in `$RAMDisk/Out/dungeonGame/Release/dungeonGame`. To build the
installer as well, see [INSTALLERS.md](INSTALLERS.md).

## Layout

```
src/Algorithms.h        all six search implementations
src/GameState.h         grid generation and movement rules
src/SimulationCanvas.h  rendering, controls, comparison panel
src/QuestionsPopUp.h    the mine question dialog
res/                    images, sounds, translations
installer/              SetupCollector config
.github/workflows/      installer builds for all four platforms
```

The interface is translated into English, Bosnian, German, French, Spanish,
Russian, Turkish and Chinese.
