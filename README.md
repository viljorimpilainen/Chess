# Chess

A Windows chess app built with Python and pygame. It runs three engines:
**Stockfish**, **Leela Chess Zero (LC0)** and **Guardian**, my own NNUE engine.
You can play them, watch them play each other, solve puzzles, train openings
and endgames, and review your games with a coach.

## Features

- **Play**: human vs AI, AI vs AI (engines keep thinking the whole time) and
  human vs human, with clocks, premoves, drag-and-drop and arrows.
- **Bots** with personalities (Pip, Rex, Sage, Viper, Titan, plus Echo, which
  adapts to your strength), or Stockfish with an Elo cap.
- **Coach**: chess.com-style move ratings, a blunder warning, a threat view (T)
  and a review summary with accuracy for every game.
- **Puzzles**: themes, Daily, For my rating, Rush and Streak. The set comes from
  the Lichess CC0 database plus classics checked by Stockfish. Your own
  mistakes become puzzles too, with spaced repetition.
- **Training**: opening trainer, endgame trainer and Guess the Move on
  famous games.
- **Analysis board**: FEN/PGN import, variations, an eval bar and MultiPV lines.
- **Tools**: board editor, game explorer, engine-vs-engine matches with SPRT,
  and an opening-book builder through the bundled [Arbiter](arbiter/README.md)
  toolkit.
- **LAN play**: direct TCP games between two computers (port 47800).
- **Stats**: rating and accuracy history.
- Full screen or windowed, with board and piece themes, text scaling and
  synthesised sounds.

## Download and run (Windows x64)

The ready-built app is in [`dist/Chess/`](dist/Chess). Keep the folder
together and start `Chess.exe`. The engines run from `_internal/`.

- LC0 uses CUDA when it finds an NVIDIA GPU. Otherwise it falls back to the
  CPU backend, which is much slower.
- The first start of Guardian compiles its kernels, which takes about a
  minute. Later starts take a few seconds.
- Settings, saved games and puzzle progress go to
  `%LOCALAPPDATA%\ChessDualEngine`.

## Run from source

Python 3.12+ (developed on 3.14):

```bash
pip install pygame-ce pygame_gui python-chess stockfish nvidia-ml-py psutil
```

```bash
python run_chess.py
```

## Project layout

```
run_chess.py          entry point
src/                  the app (game.py + game_* mixins, puzzles, coach, bots, ...)
  pics/               piece and UI images
  openings1.db        opening tree
  puzzles.db          puzzle collection (built by tools/build_puzzles.py)
  chess_1.spec        PyInstaller one-folder build
engines/              stockfish.exe, lc0.exe + 791556.pb.gz + CUDA DLLs, guardian.exe
tests/                pytest suite (fake engines, runs in seconds)
tools/                puzzle DB builder, MSIX packaging, store assets
packaging/msix/       MSIX manifest template and assets
arbiter/              engine-testing toolkit used by the Tools menu (prebuilt in arbiter/dist)
dist/Chess/           the built app (Chess.exe + _internal/)
```

All paths are relative to this folder, so you can clone it anywhere.

## Build

```bash
pyinstaller src/chess_1.spec
```

This writes `dist/Chess/Chess.exe`. To make an MSIX package from that build:

```bash
python tools/build_msix.py
```

## Tests

```bash
python -m pytest tests
```

## Engines and licences

- [Stockfish](https://github.com/official-stockfish/Stockfish) and
  [Leela Chess Zero](https://github.com/LeelaChessZero/lc0) are GPL-3.0. Their
  binaries are included unchanged; their source code is at those links.
- The CUDA DLLs (`cublas*`, `cudart*`) are NVIDIA redistributables.
- Guardian (`guardian.exe`) is my own engine. The bundled copy is the lite
  build without the experimental MCTS back-end, so it needs no PyTorch
  (166 MB instead of 2.2 GB).
- The puzzles come from the [Lichess puzzle database](https://database.lichess.org/#puzzles) (CC0).
