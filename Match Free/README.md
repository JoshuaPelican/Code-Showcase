# Match Free
Match Free is a turn-based puzzle game where you play as a creature trapped in a matching puzzle! Dodge the Puzzler's matches to stay alive and earn mana for powerful abilities. Match Free was created from a challenge to invert the roles of traditional games, and so this reverse puzzle game was born. I developed a typical match-3 architecture with a player controller on top of it. The Puzzler AI features a lookahead brute-force algorithm for challenging single-player gameplay.

See the whole repository for this project here: https://github.com/JoshuaPelican/Match-Free

<details>
  <summary> 
    <h3>
      Brute-force Lookahead AI
    </h3>
  </summary>

  In order to have the player play as someone other than the puzzle-solver, I needed a puzzle-solving AI. This was a big technical challenge, which turned out to be really fun to make!

  The quickest and easies way for me to create a smart AI for this match 3 puzzle game was to make an AI that checked all possible matches on the board, and score each match based on a few conditions. Matches are scored higher if the match:
  - Includes the player
  - Is near the player
  - Has more orbs in its match
  - Involves more combos

This works very well because it uses all the same code that the matching algorithims use to determine matches, so it saved alot of development time.

  ```C#
  void MakeBestMatch()
  {
      List<MoveData> moves = puzzleManager.GetAllPossibleMoves();

      moves.Sort((x, y) => y.Score.CompareTo(x.Score));

      MoveData bestMove = moves[0];

      //Debug.Log("Best Move: " + bestMove.ToString() + "\n" + bestMove.Matches[0].Shape.ToString());

      puzzleManager.MakeMatch(bestMove.X1, bestMove.Y1, bestMove.X2, bestMove.Y2);
  }
  ```

```C#
// Match Scoring
if(player.x == (match.X + a) && player.y == (match.Y + b))
{
    bonus = 6;
    goto done; // End scoring cause this match wins the game
}
//True Adjacency
else if (board.IsAdjacent(player.x, player.y, match.X + a, match.Y + b, true, 1))
{
    bonus = 3;
}
// Diagonal Adjacency
else if (board.IsAdjacent(player.x, player.y, match.X + a, match.Y + b, false, 1))
{
    if(bonus < 1.25f)
        bonus = 1.25f;
}
```

The AI worked well at this point, but it needed to be smarter to outwit the player and to make the short experience challenging and more puzzle-like. This is where I had the idea to make it look a few matches ahead to determine possible combos it could make from the current board state. Now the AI simulates board matches up to 4 times to consider combo/falling matches in its calculations.

```C#
for (int s = 0; s < 4; s++) // 4 is depth
{
    deeperCopy = new PuzzleBoard(deeperCopy);

    ApplyMatches(deeperCopy, deeperMatches);
    deeperCopy.ApplyGravity();

    matches.AddRange(deeperCopy.GetMatches());
    deeperMatches = new List<MatchData>(deeperCopy.GetMatches());

    if (deeperMatches.Count == 0) //No more matches found so leave loop
        break;
}
```

I am really happy with how the AI tuned out for Match Free. It is fun and challenging to play against. If I were to change anything, I would make the difficulty adjustable by changing which of the top scoring matches are chosen, and how far ahead it can look at the board.


</details>
