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
