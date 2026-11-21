[![make.fun · tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/OG2BIT001/tiny-chess-01/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/OG2BIT001/tiny-chess-01/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

# tiny-chess-01 — a chess engine in about 4 KB of Python

An original chess engine packed into `main.py` (4,091 bytes) for the make.fun tiny-chess-01 bounty ($500). The readable source is `dev/golfed10_r2.py`, packed with the kit's own `compress` command. Everything the engine needs — move generation, search, evaluation and the HTTP driver — fits inside that single packed file.

## How it plays

The engine talks HTTP to the game environment at `MAKEFUN_ENV_URL` (POST /game, /move, /resign). Each turn the environment serves the position as FEN plus the legal moves.

Search: negamax alpha-beta with PVS, quiescence over captures, promotions and check evasions, a transposition table with seeded Zobrist hashing, MVV-LVA and killer ordering, null-move pruning and late move reductions.

Evaluation: material plus piece-square tables with a middlegame/endgame phase blend, bishop pair, doubled-pawn penalty and passed-pawn bonus.

It runs on a deterministic node budget derived from the game — about 3.2M nodes per game, per-move allowance clamped to 20k–90k — and no clock. Root moves are intersected with the environment's legal moves; on any internal error it falls back to the greediest legal capture, so it never sends an illegal move.

## Verification

Perft was run against the packed `main.py` itself on 6 positions: start position depth 4 = 197,281; kiwipete depth 3 = 97,862; en-passant pin depth 4 = 43,238. Also checked: make/unmake integrity, castling through check, all four promotions, mate-in-1.

Practice trials against the kit environment scored 1279 / 1191 / 1451 (mean about 1307) against the house baseline's published 1365 — at baseline level — and every trial cleared the 1,154 floor.

## Round 2

Round 1 was rejected because the packed file's lines could not be traced to a build session. For round 2 the source was rewritten and packed with the kit's `compress` inside the recorded session.

## Files

- `main.py` — the packed engine, under the 4,096-byte cap
- `LICENSE` — MIT
- `README.md` — this file

The whole snapshot stays under the 8,192-byte cap.
