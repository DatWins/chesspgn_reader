# chesspgn_reader

Chess PGN viewer with inline comments and SVG arrow/circle overlays.

## Architecture

Single self-contained HTML file (`index.html`) — all CSS, chess.js, base64 piece sprites, and application JS are inline. No build step, no external dependencies.

### Layout

3-column responsive grid (same as game_viewer):
- **Left sidebar** (`nav.toc`): Game list, sticky
- **Center** (`main`): PGN moves with inline comments
- **Right** (`aside.bp`): Sticky chess board with SVG overlay, controls, and current-position comment

### Key Differences from game_viewer

- **No opening trees** — moves display inline with comments, no `.tree-grid`/`.tree-cell` mini-boards
- **PGN text parsing** — games come from raw PGN text in the `PGN_TEXT` constant (not a hand-coded GAMES array)
- **Inline comments** — `{ comment text }` from PGN displayed as italic text between moves
- **SVG annotation overlay** — `[%csl]` circles and `[%cal]` arrows rendered on the board

### PGN Annotation Format

Annotations are embedded in PGN comments using Lichess/ChessBase syntax:

- **Circles**: `[%csl Re4,Bf5,Gd3]` — colored circles on squares
- **Arrows**: `[%cal Re4g6,Gg1f3]` — colored arrows between squares
- **Color codes**: `R`=red, `G`=green, `B`=blue, `Y`=yellow

### Key Functions

| Function | Purpose |
|---|---|
| `parsePGNText(text)` | Splits raw PGN text into game objects with headers and movetext |
| `extractAnnotations(comment)` | Extracts `[%csl]`/`[%cal]` from comment text, returns `{text, circles, arrows}` |
| `parseMovetext(movetext)` | Tokenizes movetext into moves, comments, and results |
| `buildGameData(pgn)` | Walks moves with chess.js, builds positions[] with FEN/SAN/from/to/comment/circles/arrows per ply |
| `renderOverlay(circles, arrows)` | Draws SVG circles and arrows on the board overlay |
| `boardHTML(fen, from, to)` | Renders 8×8 CSS grid board from FEN string |
| `renderGame(g)` | Builds center content: header, moves with inline comments |
| `goTo(ply)` | Navigates to position, updates board + SVG overlay + move highlight |
| `loadGame(idx)` | Switches between games, respects `[Orientation]` header |

### How to Add Games

1. Paste PGN text into the `PGN_TEXT` constant (line ~355)
2. Multiple games can be concatenated — the parser splits on header blocks
3. Supported headers: Event, White, Black, Date, ECO, Opening, Result, Orientation, StudyName, ChapterName

### CSS Variables (Theming)

Same as game_viewer — see `:root` block at top of `<style>`. Dark mode toggled via `data-theme="dark"` on `<html>`.

### Board Size

`--bw: min(44vw, calc(100vh - 290px), 680px)` — same as game_viewer.

### Responsive Breakpoints

- `≤1100px`: Single column, board on top, sidebar hidden
- `≤480px`: Title hidden in top bar
- Landscape + short viewport: Side-by-side board + content

### Controls

- Arrow keys ←→: Navigate moves
- Home/End: Jump to start/end
- F: Flip board
- Dark mode toggle, text size A+/A−
- Download HTML, Print/PDF
