[![make.fun · tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/OG2BIT001/tiny-chess-01/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/OG2BIT001/tiny-chess-01/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

# tiny-chess-01 — a chess engine in 4,082 bytes of Python

An original engine (MIT, see LICENSE) written for the make.fun
tiny-chess-01 competition. `main.py` is the whole engine: it plays
each game of the house gauntlet through the env at `MAKEFUN_ENV_URL`
(POST /game, /move) and exits when the env answers "done".

The file is a self-unpacking lzma payload (the rules count the
payload and its unpacker toward the 4,096-byte cap). Unpacked, it is
a complete engine:

- 120-square mailbox board, full move generation (castling, en
  passant, all promotions) with perft-verified counts
- negamax alpha-beta with PVS and iterative deepening under a
  deterministic per-game node budget (3.2M nodes a game; each move's
  allowance is (budget - spent) // 40 clamped to [20k, 90k]) — no
  clock anywhere, so a replay plays the same moves
- quiescence search over captures and promotions with delta
  pruning, extending every reply when in check
- transposition table (seeded Zobrist hashing), MVV-LVA and killer
  move ordering, null-move pruning, late-move reductions
- evaluation: material and piece-square tables, a game-phase blend
  of middlegame/endgame king tables, the bishop pair, passed-pawn
  bonuses and a tempo bonus; repetitions and the fifty-move rule
  score 0 in search, with the game's own history replayed from the
  env's move list
- root moves are intersected with the env's legal move list, and
  any internal error falls back to a legal move, so the program
  never sends an illegal move or dies mid-trial

Local practice (the kit's env and sealed scorer run directly, as
`makefun grade-local --driver process` cannot run Python bots):
Elo 1279 / 1191 / 1451 on practice trials 0-2 (mean 1307), against
the house baseline's published 1364 / 1451 / 1279 (mean 1365) on
the same instances — at the baseline's level, within trial noise,
and clear of the 1154 practice floor on every trial.
