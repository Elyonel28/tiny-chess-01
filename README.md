# Tiny Chess Engine — 4 KB of Python

A chess engine in a single `main.py` of at most 4,096 bytes, written for the
make.fun bounty "Build the strongest chess engine in 4 KB of Python".

## How it plays

- **Board:** 0x88 mailbox, so edge tests are one bit check.
- **Search:** negamax with alpha-beta pruning and iterative deepening under
  a fixed node budget (deterministic: no wall clock, no unseeded random).
- **Quiescence:** captures and promotions at the leaves, plus every reply
  while in check, so it does not blunder into tactics past the horizon.
- **Ordering:** transposition-table move first, then captures by MVV-LVA,
  then killer moves.
- **Evaluation:** material (P 100, N 320, B 330, R 500, Q 900) plus
  piece-square tables and a bishop-pair bonus.

## How to run it

`main.py` is a client of the make.fun trial environment. It reads
`MAKEFUN_ENV_URL` and plays the gauntlet through it (`POST /game`,
`POST /move`, `POST /resign`). Run it with Python 3.13, standard library
only:

    MAKEFUN_ENV_URL=http://127.0.0.1:8081 python3 main.py

The readable source is `src/engine.py`; `main.py` is that source packed
with `makefun compress` (LZMA), so it fits the 4,096-byte cap.

## Strength

Move generation is verified by perft (start position, Kiwipete, en-passant
and promotion positions all match the known node counts). Against the
house gauntlet it plays a real search at roughly 55,000–100,000 nodes per
second, about 0.4–0.7 s a move — well inside the 10 s move limit.
