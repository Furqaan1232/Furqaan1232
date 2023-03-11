<p align="center"><h1>Happy to see you here</h1> The green dots on my GitHub represent my journey :running_man: - I’m Furqan. Moreover, I’m a Python Developer, software engineer by profession and entrepreneur by passion. 
</p>
[![Anurag's GitHub stats](https://github-readme-stats.vercel.app/api?username=Furqaan1232)](https://github.com/Furqaan1232/github-readme-stats)

@@ -1,22 +1,123 @@
require_relative './board'
#   6 13 20 27 34 41 48   55 62     Additional row
# +---------------------+
# | 5 12 19 26 33 40 47 | 54 61     top row
# | 4 11 18 25 32 39 46 | 53 60
# | 3 10 17 24 31 38 45 | 52 59
# | 2  9 16 23 30 37 44 | 51 58
# | 1  8 15 22 29 36 43 | 50 57
# | 0  7 14 21 28 35 42 | 49 56 63  bottom row
# +---------------------+

class Game
  def initialize
    @board = Board.new
    game_loop
  end
require 'yaml'
require_relative './invalid_move_error'

module Connect4
  class Game
    attr_reader :history, :turn

    def initialize(
      player1_board: 0,
      player2_board: 0,
      peaks: [0, 7, 14, 21, 28, 35, 42],
      history: [],
      turn: 0
    )
      @bitboards = [player1_board, player2_board]
      @peaks = peaks
      @history = history
      @turn = turn
    end

  def game_loop
    until @board.winner
      puts 'Make move'
      puts "Options: #{@board.list_moves.join(' ')}"
      col = gets.chomp.to_i
    def self.load(game_data)
      data = YAML.load(game_data)
      Game.new(**data)
    end

    def over?
      valid_moves.empty? || !winner.nil?
    end

      @board.make_move(col)
      puts '0 1 2 3 4 5 6'
      puts @board
    def result
      if winner.nil?
        'Draw'
      else
        winner
      end
    end

    def winner
      @winner ||= begin
        if win?(@bitboards[0])
          'red'
        elsif win?(@bitboards[1])
          'blue'
        else
          nil
        end
      end
    end

    def make_move(column)
      raise InvalidMoveError unless valid_moves.include?(column)

      column -= 1 # Logic uses 0 based indexing
      move = 1 << @peaks[column]
      @peaks[column] += 1
      @bitboards[@turn & 1] ^= move
      history[@turn] = column
      @turn += 1
    end

    def valid_moves
      (1..7).filter { |column| (TOP & (1 << @peaks[column - 1])) == 0 }
    end

    def current_turn
      turn.even? ? 'red' : 'blue'
    end

    def serialize
      {
        player1_board: @bitboards[0],
        player2_board: @bitboards[1],
        peaks: @peaks,
        history: history,
        turn: turn,
      }.to_yaml
    end

    def board
      board = Array.new(6) { Array.new(7) { '*' } }

      0.upto(5) do |row|
        0.upto(7) do |col|
          value = row + 7 * col
          if ((@bitboards[0] >> value) & 1) == 1
            board[row][col] = 'X'
          elsif ((@bitboards[1] >> value) & 1) == 1
            board[row][col] = 'O'
          end
        end
      end
      board = board.reverse
    end

    def print_board
      puts board.map { |row| row.join(' ') }.join("\n")
    end

    private

    TOP = 0b1000000_1000000_1000000_1000000_1000000_1000000_1000000

    def win?(bitboard)
      [1, 6, 7, 8].each do |direction|
        # shifted_bitboard = bitboard & (bitboard >> direction)
        # return true if (shifted_bitboard && (shifted_bitboard >> (2 * direction))) != 0
        return true if (bitboard & (bitboard >> direction) & (bitboard >> (2 * direction)) & (bitboard >> (3 * direction)) != 0)
      end

      false
    end
  end
end

Game.new
