# Chess Engine

A sophisticated web-based chess engine and interactive game interface built with vanilla JavaScript. Play against a strong AI opponent powered by a minimax algorithm with alpha-beta pruning.

## Overview

Chess-Engine is a fully-featured chess application that runs entirely in the browser. It implements a complete chess rules engine with a powerful AI opponent that can analyze chess positions and select optimal moves using advanced game-tree search algorithms.

## Features

### Core Features
- **Complete Chess Rules Implementation**: Full FEN notation support, castling, en passant, pawn promotion, check/checkmate detection
- **Minimax AI Engine**: Intelligent opponent using minimax algorithm with alpha-beta pruning for move optimization
- **Adjustable Difficulty**: Configurable search depth (1-8 plies) for varying difficulty levels
- **Move Hints**: Real-time best move suggestions for the white player
- **Interactive Board**: Drag-and-drop piece movement with visual feedback
- **Move Highlighting**: Automatic highlighting of last moves for easy game tracking

### Game Controls
- **Undo/Redo**: Navigate through move history with immediate board updates
- **Game Modes**:
  - Play vs Computer (you as white)
  - Computer vs Computer (watch two engines battle)
  - Custom positions (Ruy Lopez, Italian Game, Sicilian Defense presets)
- **Position Analysis**: Real-time evaluation display showing material advantage
- **Performance Metrics**: View positions-per-second, analysis time, and position count

### Advanced Features
- **Piece Square Tables**: Position-based evaluation adapted from Sunfish.py for strategic play
- **Endgame Optimization**: Special king behavior in endgame positions (material < 1500)
- **Move Validation**: Comprehensive validation including illegal move detection and stalemate detection
- **Game State Tracking**: Draw conditions (50-move rule, threefold repetition, insufficient material)

## Tech Stack

- **Frontend**: HTML5, CSS3, Vanilla JavaScript
- **Chess UI Library**: Chessboard.js for board visualization
- **Chess Logic**: Custom Chess.js implementation (0x88 board representation)
- **Algorithm**: Minimax with Alpha-Beta Pruning
- **Evaluation**: Material count + Piece Square Tables (PST)

## How It Works

### Board Representation
The engine uses the **0x88 coordinate system** - a 128-square array where each square maps to a unique integer (0-119), with gaps for invalid squares. This allows for fast move validation and boundary checking.

### Move Generation
- Pseudo-legal move generation with 8 piece types
- Legal move filtering (removes moves that leave king in check)
- Supports all special moves: castling, en passant, promotion

### AI Algorithm

```javascript
Minimax(game, depth, alpha, beta, isMaximizing, boardSum, color)
├── Generate all legal moves
├── For each move:
│   ├── Execute move
│   ├── Recursively evaluate position
│   ├── Undo move (backtrack)
│   ├── Update alpha/beta values
│   └── Prune branch if alpha >= beta
└── Return best move and evaluation score
```

**Alpha-Beta Pruning**: Eliminates branches that cannot affect the final decision, dramatically reducing search space.

### Position Evaluation

1. **Terminal States** (highest priority):
   - Checkmate: ±10^10 (decisive)
   - Check: ±50 bonus/penalty
   - Stalemate/Draw: 0

2. **Material Evaluation**:
   - Pawn: 100
   - Knight: 280
   - Bishop: 320
   - Rook: 479
   - Queen: 929
   - King: 60000

3. **Positional Evaluation**:
   - Piece Square Tables (different tables for opening and endgame)
   - Captures and promotions factored into running sum

## Project Structure

```
Chess-Engine/
├── index.html                # Main game interface
├── css/
│   └── chessboard.css       # Board styling and square colors
├── js/
│   ├── main.js              # Game controller, event handlers, UI logic
│   ├── chess.js             # Chess rules engine and move generation
│   ├── chessboard.min.js    # Chessboard.js library (UI rendering)
│   └── jquery.min.js        # jQuery dependency
└── README.md                 # This file
```

### Key Functions

**main.js**:
- `minimax()` - Core AI algorithm implementation
- `evaluateBoard()` - Position evaluation function
- `getBestMove()` - Wrapper for minimax with timing metrics
- `makeBestMove()` - Executes best move for AI player
- `compVsComp()` - Computer vs Computer automation

**chess.js**:
- `generate_moves()` - Generate all legal moves for current position
- `make_move()` - Update board state and game history
- `undo_move()` - Revert last move (used in minimax backtracking)
- `in_check/in_checkmate/in_stalemate()` - Game state queries
- `load()` / `validate_fen()` - FEN position parsing and validation

## Getting Started

### Prerequisites
- Modern web browser (Chrome, Firefox, Safari, Edge)
- No installation or dependencies required

### Running the Game

1. Clone the repository:
   ```bash
   git clone https://github.com/PR3S1D3NT/Chess-Engine.git
   cd Chess-Engine
   ```

2. Open `index.html` in your web browser:
   ```bash
   # Option 1: Direct file open
   open index.html

   # Option 2: Local server (recommended)
   python -m http.server 8000
   # Then navigate to http://localhost:8000
   ```

## Usage Guide

### Playing the Game

1. **Select Difficulty**: Choose search depth (1-8 plies) from the dropdown
   - Depth 1-2: Quick moves, weaker play
   - Depth 4-5: Moderate challenge
   - Depth 6-8: Strong opponent (slower computation)

2. **Make Your Move**: 
   - Click a white piece to select it (grey highlights show legal moves)
   - Click target square to move
   - AI automatically responds as black

3. **Enable Move Hints**:
   - Check "Show Hints" to see the engine's recommended move highlighted in yellow

4. **View Analysis**:
   - **Advantage Bar**: Shows material balance (±2000 scale, center = equal)
   - **Position Count**: Total positions evaluated
   - **Time**: Search time in seconds
   - **Positions/sec**: Throughput metric

5. **Game Controls**:
   - **Undo**: Reverses last two moves (your move + AI response)
   - **Redo**: Replays undone moves
   - **Reset**: Start new game from initial position
   - **Presets**: Load famous opening positions (Ruy Lopez, etc.)
   - **Comp vs Comp**: Watch the engine play against itself

## Configuration

### Difficulty Levels (by search depth)
```javascript
// In index.html, modify the dropdown options:
Depth 1 (instant)     - Random-like play
Depth 2 (< 1 sec)     - Beginner level
Depth 3 (1-2 sec)     - Novice level  
Depth 4 (2-5 sec)     - Intermediate
Depth 5 (5-15 sec)    - Advanced
Depth 6+ (15+ sec)    - Expert level
```

### Customizing Piece Values
In `main.js`, modify the `weights` object:
```javascript
var weights = { 
  p: 100,    // Pawn
  n: 280,    // Knight
  b: 320,    // Bishop
  r: 479,    // Rook
  q: 929,    // Queen
  k: 60000   // King
};
```

## Performance Notes

- **Branching Factor**: ~30-40 legal moves average
- **Ply 4**: ~1M positions, ~100ms (modern CPU)
- **Ply 5**: ~30M positions, ~2-3s
- **Ply 6+**: Exponential growth, use for offline analysis

Current implementation is optimized for moderate depths (4-6). For deeper analysis, consider:
- Move ordering heuristics (killer moves, history heuristic)
- Transposition tables (zobrist hashing)
- Iterative deepening with time management

## Limitations

- No endgame tablebases
- No opening book optimization
- No iterative deepening (fixed depth search)
- No move ordering optimizations
- Evaluation is material + PST only (no pawn structure, piece coordination, etc.)

## Future Improvements

- [ ] Opening book integration
- [ ] Transposition table caching
- [ ] Move ordering heuristics (MVV-LVA, killer moves)
- [ ] Zobrist hashing for position caching
- [ ] Iterative deepening with time management
- [ ] Advanced endgame evaluation
- [ ] PGN import/export
- [ ] Game analysis panel
- [ ] Network play (multiplayer)
- [ ] Mobile app version

## Testing

The chess rules engine is thoroughly tested including:
- Castling (kingside and queenside)
- En passant captures
- Pawn promotion (all piece types)
- Check and checkmate detection
- Stalemate and draw conditions
- Illegal move rejection

To test manually:
1. Load a specific FEN position via the preset buttons
2. Use the move hints to verify engine choices
3. Try unusual positions (endgames, complex tactics)

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Contributing

Contributions are welcome! Feel free to:
- Submit bug reports and feature requests via GitHub Issues
- Fork the repository and submit pull requests
- Improve the evaluation function or add new features

Areas for contribution:
- Performance optimizations
- New evaluation heuristics
- Testing and bug fixes
- Documentation improvements

## References

- **Piece Square Tables**: Adapted from [Sunfish.py](https://github.com/thomasahle/sunfish) - a minimal chess engine
- **0x88 Board Representation**: Classic efficient board representation
- **Alpha-Beta Pruning**: Standard minimax optimization technique
- **Chessboard.js**: UI component library

## Author

Created by [@PR3S1D3NT](https://github.com/PR3S1D3NT)

---

**Enjoy playing chess and analyzing engine moves! ♟️♛♚**
