<template>
  <div class="about">
    <h1>This is an about page</h1>

    <div id="board" style="width: 400px"></div>
  </div>
</template>
<script>
require('../assets/chessboard/js/chessboard-0.3.0')
require('../assets/chessboard/css/chessboard-0.3.0.css')
import $ from 'jquery'
window.jQuery = $
window.$ = $
import Chess from 'chess.js'

export default {
    mounted() {
        var board,
            game = new Chess()

        // do not pick up pieces if the game is over
        // only pick up pieces for White
        var onDragStart = function(source, piece, position, orientation) {
            if (
                game.in_checkmate() === true ||
                game.in_draw() === true ||
                piece.search(/^b/) !== -1
            ) {
                return false
            }
        }

        var makeRandomMove = function() {
            var possibleMoves = game.moves()

            // game over
            if (possibleMoves.length === 0) return

            var randomIndex = Math.floor(Math.random() * possibleMoves.length)
            game.move(possibleMoves[randomIndex])
            board.position(game.fen())
        }

        var onDrop = function(source, target) {
            // see if the move is legal
            var move = game.move({
                from: source,
                to: target,
                promotion: 'q' // NOTE: always promote to a queen for example simplicity
            })

            // illegal move
            if (move === null) return 'snapback'

            // make random legal move for black
            window.setTimeout(makeRandomMove, 250)
        }

        // update the board position after the piece snap
        // for castling, en passant, pawn promotion
        var onSnapEnd = function() {
            board.position(game.fen())
        }

        var cfg = {
            draggable: true,
            position: 'start',
            onDragStart: onDragStart,
            onDrop: onDrop,
            onSnapEnd: onSnapEnd,
            pieceTheme: function(piece) {
                if (piece.search(/w/) !== -1) {
                    return require('../assets/chessboard/img/chesspieces/wikipedia/' +
                        piece +
                        '.png')
                }

                // alpha theme for black pieces
                return require('../assets/chessboard/img/chesspieces/wikipedia/' +
                    piece +
                    '.png')
            }
        }
        board = window.ChessBoard('board', cfg)
    }
}
</script>

