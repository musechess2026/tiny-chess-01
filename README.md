# tiny-chess-01 entry

[![make.fun · tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/musechess2026/tiny-chess-01/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/musechess2026/tiny-chess-01/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

A chess engine in a single `main.py` of at most 4,096 bytes (Python 3.13,
standard library only), built for the make.fun tiny-chess-01 bounty. The
committed `main.py` is the packed entrypoint produced by `makefun compress`
from a readable source: it unpacks an LZMA payload and execs it. The engine
itself is a negamax alpha-beta search with:

- iterative deepening under a fixed node budget (never a clock, so replays
  play the same moves),
- quiescence search (captures, and all evasions when in check),
- a transposition table, null-move pruning, late-move reductions, check
  extensions, killer moves and a history heuristic,
- incremental evaluation: material plus piece-square tables with a separate
  king table in the endgame, bishop-pair bonus, and game-history repetition
  awareness (positions already seen in the game score as draws in search).

It plays through the competition env: it polls `MAKEFUN_ENV_URL` for games
and posts the move its search chooses.

## The size rule

- `main.py` is the whole engine: at most 4,096 bytes as committed,
  Python 3.13, standard library only. It reads, imports or execs no other
  file in the repository.
- Every byte counts: comments, whitespace, docstrings.
- Compression is allowed and counted. A `main.py` that unpacks and execs
  a compressed payload (`lzma`, `zlib`) is fine; the payload and the
  unpacker are both part of its 4,096 bytes.
- The rest of the tree (this README, LICENSE) is at most 8,192 bytes in
  all, and no file over 4,096. MIT, BSD, ISC and 0BSD licences fit.
- Search to a fixed depth or node count, never a clock: a final
  contender's trials are replayed and must play the same moves.

## Grade it locally

You need Docker and the `makefun` CLI. Download the competition's dev
kit, unpack it, and from inside it run:

    makefun grade-local --kit . --bot <your repository> --driver docker

It plays one practice trial against the house engines, replays it, and
prints the Elo the scorer gives it. Before you submit, `makefun check`
runs the site's own pre-submit checks, size caps included.
