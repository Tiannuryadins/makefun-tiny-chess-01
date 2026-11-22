# MiniMax-4K — Chess Engine in 4 KB of Python

[![make.fun](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/Tiannuryadins/makefun-tiny-chess-01/rank.svg)](https://make.fun/bounty/tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/Tiannuryadins/makefun-tiny-chess-01/reward.svg)](https://make.fun/bounty/tiny-chess-01)

A complete, legal chess engine in a single `main.py` of **2,488 bytes**
(limit: 4,096), packed with the kit `makefun compress` (LZMA) from a
5,916-byte source. No dependencies beyond the Python 3 standard library.

Built for the [tiny-chess-01 bounty](https://make.fun/bounty/tiny-chess-01)
on make.fun.

## How it plays (v6)

- **Board:** 120-square mailbox board (0x88-style), full legal move
  generation: castling (with real FEN castling rights), en passant,
  all four promotions (Q/R/B/N).
- **Search:** PVS (principal variation search) + transposition table
  with bound flags, killer-move ordering, null-move pruning,
  iterative deepening with a node budget, quiescence search over
  captures, MVV-LVA capture ordering, mate-distance scoring.
- **Evaluation:** material values + Michniewski piece-square tables
  + bishop-pair bonus.
- **Strength:** trial-0 practice set **3.0/6, Elo 1,279** (floor
  1,154); reaches depth 5 from the start position at 100k nodes.
  Move generation is verified correct by perft on the 6 standard
  test positions (see `BUILD-TRANSCRIPT.md`).

## Running it

```sh
python3 main.py "<FEN>" [seconds]
```

- `<FEN>` — a standard FEN string (full FEN preferred; a bare
  placement string is also accepted and treated as White to move).
- `[seconds]` — thinking time per move, default `2`.

Without arguments it reads from stdin instead: line 1 is the FEN,
optional line 2 is the thinking time in seconds.

```sh
echo "rnbqkbnr/pppppppp/8/8/8/8/PPPPPPPP/RNBQKBNR w KQkq - 0 1" | python3 main.py
```

## Input / output protocol

- **Input:** one FEN position per run (argv or stdin), plus an optional
  time budget in seconds.
- **Output:** exactly one line on stdout — the best move in
  coordinate (UCI) notation, e.g. `e2e4`, `e1g1` (castling),
  `e5d6` (en passant), `a7a8q` (promotion, with `q`/`r`/`b`/`n`
  suffix). The move is always legal for the given position.
- **No legal move** (checkmate or stalemate): outputs `0000`.
- Nothing else is printed to stdout; the program exits 0.

## Known limitations

- No draw detection by repetition or the fifty-move rule, no opening
  book, no endgame tablebase, single-threaded.
- Strength is bounded by the 4 KB size limit.
