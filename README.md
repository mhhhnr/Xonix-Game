# XONIX GAME
The Xonix game is a 2D arcade-style territory capturing game where a player moves on a grid,
creating paths to enclose and capture territory while avoiding enemies. Our implementation
meets the given requirements using C++ and SFML libraries. The project includes core features
like a main menu, level selection, scoreboard, movement tracking, and multiple enemy
behaviors. We have also implemented a two-player mode.

# Project Workflow
We began with the provided skeleton code. We read it thoroughly and tried to understand what
each function does. We then moved towards designing a multi-option start menu, level selector,
and end-game menu. The main game loop is where all the functioning occurs.

# Game Initialization
A render window is created (N\*ts by M\*ts), where ts = 18, representing tile size. A 2D array
`grid[M][N]` stores the state of the tile in each game where 0 represents empty, 1 represents
captures, 2 shows trail (player1) and 3 represents trail (player 2).

## Font & Textures
Arial.ttf font is used throughout the game and tiles, enemies and end screen is loaded using
SFML.

## Enemy Initialization
The Enemy struct creates default position (300, 300) with random.

# Main Game Loop (`while(window.isOpen())`)
MENU is used to draw the menu options and title. LEVEL_SELECT allows us to choose from
different levels including easy, medium, hard and a continuous mode. GAME executes the main
gameplay loop and controls player movement, collision, scoring, drawing. Navigation can be
done using the arrow keys and enter key is used to select an option.

# Level Selection and Modes
The array `levels[]` contain four difficulty options i.e easy , medium , hard and continuous mode.

# Enemy Behavior
We then worked on enemy behavior in each level such that Easy contains 2 enemies , Medium
contains 4 enemies, Hard contains 6 enemies and in continuous mode, enemies are increased
after a fixed amount of time. Their speed is increased using dx and dy. We then implemented the
function that enemy moves in a zigzag pattern after some time has passed.

# Scoring and Reward System
In the main game loop, score in incremented when the player captures a tile and when the player
completes a large tile fill (`movetiles > 10` or later `> 5`), they earn bonus points. Move is counted
whenever a fill is completed.

# Powerup System
Next, we implemented the powerup system which we found very challenging. Thresholds are
designed using the function `checkpower()`:
## Powerup System
The function in the end ensures that first powerup is given at 50 score and additional powerups are granted every 30 points after that. Powerups are stacked in `powerups` and can be used with $\text{P}$ key which results in enemies freezing for 3 seconds along with background sound being produced.

## Scoreboard
`Highscores.txt` file is created that stores 5 highest scores in descending order the function `sortscore(score)` is called in the gameover section to update the score if necesssry. Scoreboard display is integrated in the menu.

## Two Player Mode
We implemented logic for shared gameplay using arrows for player 1 and $\text{W}$, $\text{A}$, $\text{S}$, $\text{D}$ keys for player 2. Upon collision of both players, the game ends. 

## Music
Lastly, we added background music in the game. Music is also played when powerups are used.

## Testing
In the very end, we cleaned our code for better understanding and added comments along with fixing the indentation. A few bugs were fixed and we tried to refine the game as much as we could.

## Game Development Approach
Our approach to developing the Xonix game emphasized incremental progress. We tried to implement features step by step, slowly but gradually. Upon facing challenges, we divided the problem into smaller pieces and tried to figure out the solution. We started off by setting the core elements of the game including global constants and the necessary SFML resources. To make the code cleaner and easier to debug we made separate functions for level selection, score handling, powerups etc. SFML is used to for rendering the graphics handling user input, displaying text, background music and sound effects. After implementing each feature, we immediately tested it to ensure proper functioning and we resolved any arising bugs before moving forward. Game over screen, colors and audio added to enhance player’s experience.

## Work Flow Diagram


## Implementation Progress of Each Feature According to Requirements
* **Basic features:** Single player mode is implemented. Start and end menus are created and two player mode is implemented.
* **Difficulty and enemies:** Easy, medium, hard and continuous modes are correctly implemented.
* **Movement counter:** The number of moves made by player are accurately tracked and displayed where a move is counted each time a player builds a tile.
* **Enemy speed and pattern:** The enemy speed increases every 20 seconds and a zigzag pattern is formed after 30 seconds.
* **Scoring and rewards:** Points are continuously added along with bonuses. Powerups are properly implemented upon reaching certain score and unused powerups are stacked.
* **Scoreboard:** Top 5 highest scores are stored in a text file `highscores.txt`.
* **Two player mode:** Two players share the game board and player two plays using $\text{W}$, $\text{A}$, $\text{S}$, $\text{D}$ keys. Upon collision game ends.
* **Bonus features:** Background music is added throughput the game and sound effect is added for when power ups are used. The menu is made using different colors.
```cpp
void checkpower()
{
    if(score >= 50)
    {
        int scoreafterlastpower = score - powerscore;
        if(scoreafterlastpower >= 30)
        {
            int newpower = scoreafterlastpower / 30;
            powerups += newpower;
            powerscore = score - (scoreafterlastpower % 30);
        }
    }
}
