#include <iostream>
#include <string>
#include<fstream>
#include <SFML/Audio.hpp>
#include <SFML/Graphics.hpp>
#include <time.h>
#include <math.h>
using namespace sf;
using namespace std;

const int M=25;
const int N=40;
int grid[M][N]={0};//initially set the grid to 0
int ts=18; //tile size
bool game=1;
sf::Font font;//initializing font
int moves=0; //initializing moves
RenderWindow window(VideoMode(N*ts,M*ts),"Xonix game!");//start game title
enum GameState{MENU,GAME,LEVEL_SELECT,SCOREBOARD,TWOPLAYERS};//define start game menue items
int score=0;//initially set the scores to 0
int powerups=0;//initially set the powerups to 0
bool powerupon=false;//initially off the powerup
Clock powerupclock;//clock for powerups
float poweruptime = 3.0f;//duration of power up as 3 seconds
GameState currentitem=MENU;//Start the game with menue items
int menuitem=0;//it tells which menue item based on index
enum Difficulty{EASY,MEDIUM,HARD,CONTINUOUS};//define difficulty levels in select level option
Difficulty currentDifficulty=EASY;//set difficulty to easy
sf::Music backgroundMusic;//bg music for statup menue
sf::SoundBuffer blockMadeBuffer;//bg music for tiles
sf::Sound blockMadeSound;//sound for a big block
int enemies=2; //Default to enemies as 2
sf::Clock continousclock;//clock for continous mode of level
int timercounter=0;//it is the counter that increments every second
//======================enemies number increment================//
int enemyincreasetime=-1;//it keep on the track of enemy numbers increasing
float lasttime=0.0f;
//====================for enemies speed increment========================//
static int lastspeedtime=-1;
float speedincreasetime=20.0f;//increase speed after very 20 seconds
//=======================for the file===================================//
int x=0, y=0;
int x2=10, y2=24;
int dx=0, dy=0;
int dx2=0, dy2=0;
float powercool=0.0f;//initialize the powerup time
const float powercooltime=1.0f; // 1 second cooldown between power-ups
bool inendmenu=true;
int selected=0;
int movetiles=0; 
int occurence=0;
bool newmove=true;
bool usepowerup=false;//whether a power up is just used or not
int powerscore=0;//last score of powerup
float enemyspeed=3.0f;//initial speed of enemy
const float speedamount=2.0f;
const float ztime=0.2f;//time between zig and zag
const float cradius=2.0f;

struct Enemy
{
	int x,y,dx,dy;
	float xcentre, ycentre;
	float angle;
	bool circular=false;
	bool canmove=true;  
	Enemy()
	{
    	x=y=300;
    	dx=4-rand()%8;
    	dy=4-rand()%8;
    	xcentre=ycentre=0;
    	angle=0.0f;
    }

	void move()
	{ 
		if(canmove)
		{
			x+=dx; if (grid[y/ts][x/ts]==1) {dx=-dx; x+=dx;}
			y+=dy; if (grid[y/ts][x/ts]==1) {dy=-dy; y+=dy;}
		}
	}
};

void drop(int y,int x)
{
  if (grid[y][x]==0) 
  {
  	grid[y][x]=-1;
  	
  }
  if (grid[y-1][x]==0)
  {
  	
  	drop(y-1,x);
  }
  if (grid[y+1][x]==0)
  {
  	drop(y+1,x);
  }
  if (grid[y][x-1]==0)
  {
  	drop(y,x-1);
  }
  if (grid[y][x+1]==0)
  {
  
  	drop(y,x+1);
  }
}
Enemy a[10];
void zigzagmovement(Enemy& enemy, float elapsedTime)
{
    float horizontalOffset=sin(elapsedTime*7.0f)*enemyspeed;
    //new position
    float newx=enemy.x+horizontalOffset;
    float newy=enemy.y+(enemyspeed*0.5f);
    //reverse the direction after hitting a left or right boundary
    if(grid[int(newy/ts)][int(newx/ts)]==1)
    {
        newx=enemy.x-horizontalOffset;
    }
    //reverse the direction after hitting a top or bottom boundary    
    if(newy<=0 || newy>=(M-1)*ts)
    {
        newy=enemy.y;
    }
    enemy.x=newx;
    enemy.y=newy;
    enemy.dx=horizontalOffset;
    enemy.dy=enemyspeed*0.5f;
}

void moveCircular(Enemy& enemy)
{
    enemy.angle += 0.1f;
    float newx = enemy.xcentre + cradius * cos(enemy.angle);
    float newy = enemy.ycentre + cradius * sin(enemy.angle);
    //check for the collision woth boundaries
    if(grid[int(newy/ts)][int(newx/ts)]==1)
    {
        enemy.xcentre=enemy.x;
        enemy.ycentre=enemy.y;
        enemy.angle=0.0f;
    }
    enemy.x=newx;
    enemy.y=newy;
}

//==========================================================Function for select level menu================================================================//
void selectLevel()
{
    string levels[4]={"Easy","Medium","Hard","Continuous Mode"};
    Text levelitems[4];
    int currentlevelitem=0;
    //===========================Select Level title======================//
    Text title;
    title.setFont(font);  // Use the same font as the menu items
    title.setString("Select Level :)");
    title.setCharacterSize(40);
    title.setFillColor(Color::Cyan);
    title.setPosition(200, 50);
    //================Loading textures for the tiles and enemies=========//
	Texture t1, t2, t3;
    t1.loadFromFile("images/tiles.png");
    t2.loadFromFile("images/gameover.png");
    t3.loadFromFile("images/enemy.png");
    Sprite sTile(t1),sGameover(t2),sEnemy(t3);
    sGameover.setPosition(100,100);
    sEnemy.setOrigin(20,20);
	//================styling the level items===========================//
    for (int i=0;i<4;i++)
    {
        levelitems[i].setFont(font);
        levelitems[i].setString(levels[i]);
        levelitems[i].setCharacterSize(25);
        levelitems[i].setFillColor(Color::White);
        levelitems[i].setPosition(180, 150 + i * 60);
    }
    while (window.isOpen())
    {
    	static sf::Clock continousclock;
		static int timercounter=0;
		static int enemyincreasetime=-1;//to prevent many increase in speed
		static float lasttime=0.0f;
        Event event;
        while (window.pollEvent(event))
        {
            if (event.type==Event::Closed)
                window.close();

            if (event.type==Event::KeyPressed)
            {
                if (event.key.code==Keyboard::Up)
                {
                    currentlevelitem=(currentlevelitem+3) % 4;
                }
                else if (event.key.code==Keyboard::Down)
                {
                    currentlevelitem=(currentlevelitem+1)%4;
                }
                else if (event.key.code==Keyboard::Enter)
                {
                    currentDifficulty=static_cast<Difficulty>(currentlevelitem);
                    if (currentDifficulty==EASY)
                        enemies=2;
                    else if (currentDifficulty==MEDIUM)
                        enemies=4;
                    else if (currentDifficulty==HARD)
                        enemies=6;
                    else if (currentDifficulty==CONTINUOUS)
					{
						enemies=2;
						continousclock.restart();//it restart timer before every contionus game
						timercounter=0;
						enemyincreasetime=-1;
						lasttime=0.0f;
					}
                    currentitem=GAME;//it Return to main menu logic
                    return;
                }
            }
            if (!game)
            {
            	continue;//it will Skip the rest of the game logic
            }
        }
        window.clear(Color::Black);
        window.draw(title);
        //========================styling for the selected item=================//
        for(int i=0;i<4;i++)
        {
            if(i==currentlevelitem)
            {
                RectangleShape selector(Vector2f(350,40));
                selector.setFillColor(Color(70,70,180));
                selector.setPosition(levelitems[i].getPosition().x-10,levelitems[i].getPosition().y-5);
                window.draw(selector);
                levelitems[i].setFillColor(Color::Yellow);
            }
            else
            {
                levelitems[i].setFillColor(Color::White);
            }

            window.draw(levelitems[i]);
        }

        window.display();
    }
}

//======================================highscore in file===============================//

const int maxscore=5;
int scores[maxscore]={0};
int numscore=0;

void loadscoreinfile ()
{
    ifstream file("highscores.txt");
    if(file.is_open())
    {
        numscore=0;
        while(numscore<maxscore && file>>scores[numscore])
        {
            numscore++;
        }
        file.close();
    }
}

void savescore()
{
    ofstream file("highscores.txt", ios::out | ios::trunc);
    if (file.is_open()) {
        for (int i = 0; i < numscore; i++) {
            file << scores[i] << endl;
        }
        file.close();
    } else {
        cout << "Error: Could not save high scores!" << endl;
    }
}

void sortscore(int currentscore)
{
    
    loadscoreinfile();
    bool addscore=(numscore<maxscore) || (currentscore>scores[numscore-1]);
    
    if(addscore)
    {
        int insertposition=0;
        while(insertposition<numscore && currentscore<scores[insertposition])
        {
            insertposition++;
        }
        for(int i=numscore-1;i>=insertposition && i<maxscore-1;i--)
        {
            scores[i+1]=scores[i];
        }
        scores[insertposition]=currentscore;
        if(numscore<maxscore)
        {
            numscore++;
        }
        savescore();
    }

}
void checkpower()
{
    if(score>=50)
    {
        int scoreafterlastpower=score-powerscore;
        if(scoreafterlastpower>=30)
        {
            int newpower=scoreafterlastpower/30;
            powerups+=newpower;
            powerscore=score-(scoreafterlastpower%30);
        }
    }
}

void resetGameState()
{
    score=0;
    moves=0;
    powerups=0;
    powerupon=false;
    occurence=0;
    movetiles=0;
    newmove=true;
    game=true;
    inendmenu=true;
    selected=0;
	//grid reset
    for(int i=1;i<M-1;i++)
        for(int j=1;j<N-1;j++)
            grid[i][j]=0;
    x=10;
    y=0;
    x2=10;
    y2=24;
    dx=0;
    dy=0;
    dx2=0;
    dy2=0;
    for(int i=0; i<enemies; i++)
    {
        a[i].x=a[i].y=300;
        a[i].dx=4-rand()%8;
        a[i].dy=4-rand()%8;
        a[i].circular=false;
        a[i].canmove=true;
    }
    powerupon=true;
    powercool=0.0f;
    usepowerup=false;
    powerupclock.restart();
    powerscore=0;
}
//=================scoreboard================//
void scoreboard(sf::RenderWindow &window)
{
    loadscoreinfile();
    sf::Font font;
    if(!font.loadFromFile("arial.ttf"))
    {
        return;
    }
    sf::Text title("Top 5 High Scores YAYYYYYYY",font,40);
    title.setFillColor(sf::Color::Yellow);
    title.setPosition(200,50);
    window.clear();
    window.draw(title);  
    // Display scores
    for (int i = 0; i < 5 && i < numscore; ++i)
    {
        sf::Text scoretext(std::to_string(i + 1)+".  "+std::to_string(scores[i]),font,30);
        scoretext.setFillColor(sf::Color::White);
        scoretext.setPosition(200,120+i*50);
        window.draw(scoretext);
    } 
    window.display();   
    sf::Event event;
    while(window.isOpen())
    {
        while(window.pollEvent(event))
        {
            if(event.type==sf::Event::KeyPressed || event.type==sf::Event::Closed)
            {
                return;
            }
        }
    }
}

void powerupdisplay(sf::RenderWindow& window,const sf::Font& font)
{
    Text powertext;
    powertext.setFont(font);
    powertext.setCharacterSize(24);
    powertext.setFillColor(Color::White);
    powertext.setPosition(200,-5);
    powertext.setString("PowerUps: "+to_string(powerups));
    window.draw(powertext);
    RectangleShape powerbg(Vector2f(170, 25));
    powerbg.setFillColor(Color(139, 0, 0));
    powerbg.setPosition(190, -5);
    window.draw(powerbg);
    window.draw(powertext);
}

int main()
{
    srand(time(0));
    if(!font.loadFromFile("arial.ttf"))
	{
		return -1;
	}
	sf::Font font;
	font.loadFromFile("arial.ttf");
	//background music in overall game
	if(!backgroundMusic.openFromFile("start.ogg"))
	{
        std::cout<<"Failed to load background music"<<std::endl;
        return -1;
    }
    backgroundMusic.setLoop(true);//bg music in loop
    backgroundMusic.play();
    //set music for tiles
	if(!blockMadeBuffer.loadFromFile("block.ogg"))
	{
		std::cout<<"Failed to load block made sound"<<std::endl;
		return -1;
	}
	blockMadeSound.setBuffer(blockMadeBuffer);
    Texture t1,t2,t3;
    t1.loadFromFile("images/tiles.png");
    t2.loadFromFile("images/gameover.png");
    t3.loadFromFile("images/enemy.png");
    bool fill=false;
    bool showScores;
	bool inendmenu=true;
    int selected=0;
    window.setFramerateLimit(60);
   	//=========draw the timer=========//
	sf::Clock clock;
	int secondelapsed=0;
	sf::Time lastupdated=sf::Time::Zero;
	sf::Text timertext;
	timertext.setFont(font);
	timertext.setCharacterSize(24);
	timertext.setFillColor(sf::Color::White);
	timertext.setPosition(600,-5);
	timertext.setString("Time:0");
	sf::Clock gameClock;
	float lasttime=0.0f;
	int timercounter=0;
	float lastspeedtime=0.0f;
    //===================Start menue Title==================//
	Text title;
	title.setFont(font);
	title.setString("Xonix game");
	title.setCharacterSize(50);
	title.setFillColor(Color::Cyan);
	title.setStyle(Text::Bold);
	title.setPosition(180,50);

	Text menu[4]; 
	std::string menuItems[4]={"Start game","Select Level","Scoreboard","Two Players"};
	//==========style startup menue items===============//
	for(int i=0;i<4;i++)
	{
		menu[i].setFont(font);//set the font of menue items
		menu[i].setString(menuItems[i]);//sets which menue item to be displayed
		menu[i].setCharacterSize(30);//sets the font size of menue items
		menu[i].setFillColor(Color::White);//sets the colour of menue items
		menu[i].setPosition(200,150+i*60); //spaceing of menue items
	}
	Enemy a[10];
	float timer=0,delay=0.01; 
    Sprite sTile(t1),sGameover(t2),sEnemy(t3);
    sGameover.setPosition(100,100);
    sEnemy.setOrigin(20,20);
    for(int i=0;i<M;i++)
     for(int j=0;j<N;j++)
      if(i==0 || j==0 || i==M-1 || j==N-1)  grid[i][j]=1;
      
    Event event;
    while(window.isOpen())
    {
    	if(event.type==sf::Event::KeyPressed || event.type==sf::Event::MouseButtonPressed)
    	{
        	//stop music
            backgroundMusic.stop();
            if(event.type==Event::KeyPressed)
            {
            	if(event.key.code==sf::Keyboard::Enter)
            	{
                	std::cout<<"Start game selected!"<<std::endl;
                }
            }
		}
		if(showScores)
		{
			window.clear();
			Text scoretext;
			scoretext.setFont(font);
			scoretext.setCharacterSize(40);
			scoretext.setFillColor(Color::White);
			scoretext.setPosition(210,150);
			scoretext.setString("Your Score: "+std::to_string(score));
			window.draw(scoretext);
			Text backText("Press Enter to return to End Menu",font,24);
			backText.setFillColor(Color::Yellow);
			backText.setPosition(120,200);
			window.draw(backText);
			window.display();
			
			Event e;
			while(window.pollEvent(e))
			{
				if(e.type==Event::Closed)
				    window.close();
				if(e.type==Event::KeyPressed && (e.key.code==Keyboard::Enter || e.key.code==Keyboard::Return))
				{
				    showScores=false;
				    inendmenu=true;
				    selected=0;
				}
			}

			continue;
		}

     	sf::Time currentTime=clock.getElapsedTime();
		if(currentTime.asSeconds()-lastupdated.asSeconds()>=1.0f)
		{
			secondelapsed++;
			lastupdated=currentTime;
			/// ------------for enemies increasing and speed increasing------------//
			if(currentDifficulty==CONTINUOUS && currentitem==GAME)
			{
				float elapsed=continousclock.getElapsedTime().asSeconds();

				if(elapsed-lasttime>=1.0f)//make the loop to run after every second
				{
					lasttime=elapsed;
					timercounter++;
					if(timercounter%20==0&&timercounter!=enemyincreasetime)
					{
						enemies+=2;
						enemyincreasetime=timercounter;
						for(int i=0;i<enemies;i++)
						{
							a[i].dx+=(a[i].dx>0?speedamount:-speedamount);
						    a[i].dy+=(a[i].dy>0?speedamount:-speedamount);
						}
						lastspeedtime=timercounter;
					}
				}
			}
			if(currentitem==GAME)
			{
				static bool ppressed=false;
				bool isppressed=Keyboard::isKeyPressed(Keyboard::P);
				if(isppressed&&!ppressed && powerups>0 && !powerupon)
				{
					powerupon=true;
					powerups--;
					powerupclock.restart();
					blockMadeSound.play();
					for(int i=0;i<enemies;i++)
					{
						a[i].canmove=false;
					}
				}
				ppressed=isppressed;
				if(powerupon && powerupclock.getElapsedTime().asSeconds()<=poweruptime)
				{
					for(int i=0;i<enemies;i++)
					{
						a[i].canmove=false;
					}
				}
				else
				{
					powerupon=false;
					for(int i=0;i<enemies;i++)
					{
						a[i].canmove=true;
					}
				}
				
				float elapsed=gameClock.getElapsedTime().asSeconds();
				static bool patternactive=false;
				if(elapsed-lasttime>=1.0f)
				{
					lasttime=elapsed;
					timercounter++;
					if(timercounter%20==0 && timercounter!=lastspeedtime)
					{
						enemyspeed+=speedamount;
						for(int i=0;i<enemies;i++)
						{
							if(!a[i].circular)
							{
								a[i].dx=(a[i].dx>0?enemyspeed:-enemyspeed);
								a[i].dy=(a[i].dy>0?enemyspeed:-enemyspeed);
							}
						}
						lastspeedtime=timercounter;
					}
					if(timercounter>=30 && !patternactive)
					{
						patternactive=true;
						int half=enemies/2;
						for(int i=0;i<half;i++)
						{
							if(i%2==0)
							{
								a[i].circular=false;
							}
							else
							{
								a[i].xcentre=a[i].x;
								a[i].ycentre=a[i].y;
								a[i].angle=0.0f;
								a[i].circular=true;
							}
						}
					}
				}
				for(int i=0;i<enemies;i++)
				{
					if(a[i].canmove) 
					{  
						if(a[i].circular)
						{
							moveCircular(a[i]);
						}
						else if(i<enemies/2 && patternactive)
						{
							zigzagmovement(a[i],elapsed);
						} 
						else
						{
							a[i].move();
						}
					}
				}
				checkpower();
			}
			timertext.setString("Time: "+std::to_string(secondelapsed));
		}
		//==================Menue==============//
		if(currentitem==MENU)
		{
			Event e;
			while(window.pollEvent(e))
			{
				if(e.type==Event::Closed)
					window.close();
				if(e.type==Event::KeyPressed)
				{
					if(e.key.code==Keyboard::Up)
						menuitem=(menuitem+3)%4;
					if(e.key.code==Keyboard::Down)
						menuitem=(menuitem+1)%4;
					if(e.key.code==Keyboard::Enter)
					{
						if(menuitem==0)
						    currentitem=GAME;
						else if(menuitem==1)
						    currentitem=LEVEL_SELECT;
						else if(menuitem==2)
						    currentitem=SCOREBOARD;
						else if(menuitem==3)
						    currentitem=TWOPLAYERS;
					}
				}
			}
			window.clear(Color::Black);
			window.draw(title);
			//========styling of selected menu items===========//
			for(int i=0;i<4;i++)
			{
				if(i==menuitem)
				{
					RectangleShape selector(Vector2f(250,40));
					selector.setFillColor(Color(70,70,180));
					selector.setPosition(menu[i].getPosition().x-10,menu[i].getPosition().y-5);
					window.draw(selector);
					menu[i].setFillColor(Color::Yellow);
				}
				else
				{
					menu[i].setFillColor(Color::White);
				}

				window.draw(menu[i]);
			}
			for(int i=0;i<enemies;i++) a[i].move();
			//==========game========//
			if(currentitem==GAME)
			{
				float time=clock.getElapsedTime().asSeconds();
				timer+=time;
				Event e;
				while(window.pollEvent(e))
				{
					if(e.type==Event::Closed)
						window.close();
					if(e.type==Event::KeyPressed && e.key.code==Keyboard::Escape)
					{
						for(int i=1;i<M-1;i++)
						    for(int j=1;j<N-1;j++)
						        grid[i][j]=0;
						x=10;y=0;
						game=true;
					}
				}
				if(e.type==Event::KeyPressed)
				{
					if(Keyboard::isKeyPressed(Keyboard::Left))
					{
						dx=-1;
						dy=0;
					}
					if(Keyboard::isKeyPressed(Keyboard::Right))
					{
						dx=1; 
						dy=0;
					}
					if(Keyboard::isKeyPressed(Keyboard::Up))
					{
						dx=0; 
						dy=-1;
					}
					if(Keyboard::isKeyPressed(Keyboard::Down))
					{
						dx=0;
						dy=1;
					}
				}
				window.clear();
				for(int i=0;i<M;i++)
					for (int j=0;j<N;j++)
					{
						if(grid[i][j]==0) continue;
						if(grid[i][j]==1) sTile.setTextureRect(IntRect(0,0,ts,ts));
						if(grid[i][j]==2) sTile.setTextureRect(IntRect(54,0,ts,ts));
						sTile.setPosition(j*ts,i*ts);
						window.draw(sTile);
					}
				sTile.setTextureRect(IntRect(36,0,ts,ts));
				sTile.setPosition(x*ts,y*ts);
				window.draw(sTile);
				sEnemy.rotate(10);
				for(int i=0;i<enemies;i++)
				{
					sEnemy.setPosition(a[i].x,a[i].y);
					window.draw(sEnemy);
				}
				powerupdisplay(window, font);
				if(!game)
				{
					window.draw(sGameover);
					sortscore(score);
				}
				window.display();
			}
			if(currentitem==LEVEL_SELECT) 
			{
		   		selectLevel();
			}
			if(currentitem==SCOREBOARD)
			{
				scoreboard(window);
			}
			window.display();
			continue;
	}
	window.clear();
	float playerSpeed=1.0f;
	if(currentitem==TWOPLAYERS)
	{
		Event e;
		while(window.pollEvent(e))
		{
			if(e.type==Event::Closed)
				window.close();
			if(e.type==Event::KeyPressed && e.key.code==Keyboard::Escape)
			{
				for(int i=1;i<M-1;i++)
					for(int j=1;j<N-1;j++)
					    grid[i][j]=0;
				x=0; y=0;
				x2=10;y2=24;
				game=true;
			}
		}
		if(Keyboard::isKeyPressed(Keyboard::Left)) {dx=-1; dy=0;}
		else if(Keyboard::isKeyPressed(Keyboard::Right)) {dx=1; dy=0;}
		else if(Keyboard::isKeyPressed(Keyboard::Up)) {dx=0; dy=-1;}
		else if(Keyboard::isKeyPressed(Keyboard::Down)) {dx=0; dy=1;}

		if(Keyboard::isKeyPressed(Keyboard::A)) {dx2=-1; dy2=0;}
		else if(Keyboard::isKeyPressed(Keyboard::D)) {dx2=1; dy2=0;}
		else if(Keyboard::isKeyPressed(Keyboard::W)) {dx2=0; dy2=-1;}
		else if(Keyboard::isKeyPressed(Keyboard::S)) {dx2=0; dy2=1;}
		if(timer>delay)
		{
			int xprev=x;
			int yprev=y;
			x+=dx*playerSpeed;
			y+=dy*playerSpeed;
			x2+=dx2*playerSpeed;
			y2+=dy2*playerSpeed;
			if(x<0) x=0; if(x>N-1) x=N-1;
			if(y<0) y=0; if(y>M-1) y=M-1;
			if(x2<0) x2=0; else if(x2>N-1)x2=N-1;
		    if(y2<0) y2=0; else if(y2>M-1)y2=M-1;
			for(int i=0;i<enemies;i++)
			{
				if((a[i].x/ts==x && a[i].y/ts==y) || 
				   (a[i].x/ts==xprev && a[i].y/ts==yprev))
				   {
					   game = false;
					   break;
				   }
			}
			if(grid[y][x]==2 || grid[y][x]==3) game=false;
		    if(grid[y][x]==0) grid[y][x]=2;
		    if(grid[y2][x2]==2 || grid[y2][x2]==3) game=false;
		    if(grid[y2][x2]==0) grid[y2][x2]=3;
		    if(x==x2 && y==y2)
		    {
		    	game=false;
			}
			if(game)
			{
				if(grid[y][x]==0)
				{
					grid[y][x]=2;
					fill=true;
					movetiles++;
					score++;
					checkpower();
				}
				if(grid[y][x]==1 && fill==true)
				{
					moves++;
					fill=false;
					if(movetiles>10 || (occurence>=3 && movetiles>5))
					{
						int multiplybonus=(occurence>=5)?4:2;
						int bonuspts=(movetiles-1)*(multiplybonus-1);
						score+=bonuspts;
						occurence++;
						checkpower();
					}
					
					movetiles=0;
				}
			}
			
			timer=0;
		}
		for(int i=0;i<enemies;i++) a[i].move();
		if (grid[y][x]==1)
		{
			dx=dy=0;
		    for (int i=0;i<enemies;i++)
		    	drop(a[i].y/ts, a[i].x/ts);
		    for (int i=0;i<M;i++)
		    	for (int j=0;j<N;j++)
		    		if (grid[i][j]==-1)
		            {
		          		grid[i][j]=0;
				  	}
		          else
		          {
		          	grid[i][j]=1;
		          }
		}
		for(int i=0;i<enemies;i++)
			if (grid[a[i].y/ts][a[i].x/ts]==2) game=false;
		//draw the squares
		for(int i=0;i<M;i++)
		{
		    for(int j=0;j<N;j++)
		    {
		        if(grid[i][j]==0) continue;
		        if(grid[i][j]==1)
		        	sTile.setTextureRect(IntRect(0,0,ts,ts));
		        else if(grid[i][j]==2)
		        {
		            sTile.setTextureRect(IntRect(54,0,ts,ts));
		            sTile.setColor(Color::Green);
		        }
		        else if(grid[i][j]==3)
		        {
		            sTile.setTextureRect(IntRect(54,0,ts,ts));
		            sTile.setColor(Color::Red);
		        }
		        sTile.setPosition(j*ts,i*ts);
		        window.draw(sTile);
		        sTile.setColor(Color::White);
		    }
		}
		// Draw Player 1
		sTile.setTextureRect(IntRect(36,0,ts,ts));
		sTile.setPosition(x*ts,y*ts);
		sTile.setColor(Color::Green);
		window.draw(sTile);
		// Draw Player 2
		sTile.setPosition(x2*ts,y2*ts);
		sTile.setColor(Color::Red);
		window.draw(sTile);
		sTile.setColor(Color::White);
		sEnemy.rotate(10);
			  for(int i=0;i<enemies;i++)
			   {
					sEnemy.setPosition(a[i].x,a[i].y);
					window.draw(sEnemy);
			   }
			   for(int i=0;i<enemies;i++)
		       if(grid[a[i].y/ts][a[i].x/ts]==2) game=false;
		timer += clock.restart().asSeconds();
		if(!game)
		{
			window.draw(sGameover);	
			continue;
		}
		window.display();
		continue;
	}
	


	float time=clock.getElapsedTime().asSeconds();
	timer+=time;
	Event e;
    while(window.pollEvent(e))
    {
		if(e.type==Event::Closed)
		window.close();
		if(e.type==Event::KeyPressed)
			if(e.key.code==Keyboard::Escape)
             {
				for(int i=1;i<M-1;i++)
					for(int j=1;j<N-1;j++)
                		grid[i][j]=0;
                x=10;y=0;
                game=true;
			}
            if(!game)
            {
            	window.draw(sGameover);
            	continue;
            }
	}
	//==================key pressing=================//
	if(Keyboard::isKeyPressed(Keyboard::Left)) {dx=-1;dy=0;};
	if(Keyboard::isKeyPressed(Keyboard::Right))  {dx=1;dy=0;};
	if(Keyboard::isKeyPressed(Keyboard::Up)) {dx=0;dy=-1;};
	if(Keyboard::isKeyPressed(Keyboard::Down)) {dx=0;dy=1;};
	if(e.type==Event::KeyPressed)
		if(e.key.code==Keyboard::Escape)
		{
			for(int i=1;i<M-1;i++)
				for(int j=1;j<N-1;j++)
				{
					
                   grid[i][j]=0;
                }

            x=10;y=0;
           	game=true;
		}
        if(timer>delay)
        {
        	x+=dx*playerSpeed;
    		y+=dy*playerSpeed;
			if(x<0) x=0; if(x>N-1) x=N-1;
		    if(y<0) y=0; if(y>M-1) y=M-1;
		     if(grid[y][x]==2)
		     {
		     	game=false;
		     	
		     }
		     if(grid[y][x]==0)
		     {
		     	grid[y][x]=2;
		     	fill=true;
		     	movetiles++;
		     	score++;
		     }

		     if(grid[y][x]==1 && fill==true)
		     {
		     	moves++;
		     	fill=false;
		     	if(movetiles>10 || (occurence>=3 && movetiles>5))
		     	{
		     		int multiplybonus=(occurence>=5)?4:2;
		     		score+=(movetiles-1)*(multiplybonus-1);
		     		occurence++;
		     		if(score>=50 && score<70 && powerups==0)
		     			powerups=1;
		     		else if(score>=70 && score<100 && powerups==1)
		     			powerups=2;
		     		else if(score>=100 && score<130 && powerups==2)
		     			powerups=3;
		     		else if(score>=130 && powerups==3)
		     			powerups=4;
		     		else if(score>=160 && powerups==4)
		     			powerups=5;
		     		else if(score>=190 && powerups==5)
		     			powerups=6;
		     		else if(score>=220 && powerups==6)
		     			powerups=7;
		     		else if(score>=250 && powerups==7)
		     			powerups=8;
		     	}
		     	
		     	movetiles=0;
		     }
		     timer=0;
        }
        for(int i=0;i<enemies;i++) a[i].move();
        if(grid[y][x]==1)
        {
        	dx=dy=0;
            for(int i=0;i<enemies;i++)
            	drop(a[i].y/ts,a[i].x/ts);
           for(int i=0;i<M;i++)
           	for(int j=0;j<N;j++)
            	if(grid[i][j]==-1)
                {
                	grid[i][j]=0;
			    }
              	else
              	{
              		grid[i][j]=1;
              	}
		}
        for(int i=0;i<enemies;i++)
        	if(grid[a[i].y/ts][a[i].x/ts]==2) game=false;
        window.clear();
        for(int i=0;i<M;i++)
        	for(int j=0;j<N;j++)
         	{
		        if(grid[i][j]==0)
		        {
		        	continue;
		        }
		        if(grid[i][j]==1)
		        {
					sTile.setTextureRect(IntRect( 0,0,ts,ts));
				}
		        if(grid[i][j]==2)
		        {
		        	sTile.setTextureRect(IntRect(54,0,ts,ts));
		        }
		        sTile.setPosition(j*ts,i*ts);
		        window.draw(sTile);
         	}
    	for(int i=0;i<M;i++)
		    for (int j=0;j<N;j++)
		    {
		        if(grid[i][j]==2)
		        {
		        	
		            
		        }
		    }
		sTile.setTextureRect(IntRect(36,0,ts,ts));
		sTile.setPosition(x*ts,y*ts);
		window.draw(sTile);
		sEnemy.rotate(10);
		for(int i=0;i<enemies;i++)
		{
		    sEnemy.setPosition(a[i].x,a[i].y);
			window.draw(sEnemy);
		}
		if(!game)
		{
			sortscore(score);
			Font font;
			font.loadFromFile("arial.ttf");
			Text options[3];
			std::string optionLabels[3]={"RESTART","MAIN MENU","SCORES"};
			for(int i=0;i<3;i++)
			{
				options[i].setFont(font);
				options[i].setString(optionLabels[i]);
				options[i].setCharacterSize(30);
				options[i].setPosition(180,150+i*60);
				options[i].setFillColor(i==selected?Color::Yellow:Color::White);
			}

			while(inendmenu && window.isOpen())
			{
				Text title;
				title.setFont(font);
				title.setString("Game Over :(");
				title.setCharacterSize(50);
				title.setFillColor(Color::Cyan);
				title.setStyle(Text::Bold);
				title.setPosition(180,50);
				
				Event e;
				while(window.pollEvent(e))
				{
					if(e.type==Event::Closed)
						window.close();
					if(e.type==Event::KeyPressed)
					{
						if(e.key.code==Keyboard::Up)
							selected=(selected+2)%3;
						else if(e.key.code==Keyboard::Down)
							selected=(selected+1)%3;
						else if(e.key.code==Keyboard::Enter || e.key.code==Keyboard::Return)
						{
							if(selected==0)
							{
								resetGameState();
								currentitem=GAME;
								inendmenu=false;
							}
							else if(selected==1) // Main Menu
							{
								resetGameState();
								currentitem=MENU;
								inendmenu=false;
							}
							else if(selected==2) // Scores
							{
								showScores=true;
								inendmenu=false;
							}
						}
						else if(e.key.code==Keyboard::Escape)
						{
							resetGameState();
							currentitem=MENU;
							inendmenu=false;
						}
					}
				}
				window.clear(Color::Black);
				window.draw(title);
				for(int i=0;i<3;i++)
				{
					if(i==selected)
					{
						RectangleShape selector(Vector2f(250,40));
						selector.setFillColor(Color(70,70,180));
						selector.setPosition(options[i].getPosition().x-10,options[i].getPosition().y-5);
						window.draw(selector);
						options[i].setFillColor(Color::Yellow);
					}
					else
					{
						options[i].setFillColor(Color::White);
					}
					window.draw(options[i]);
				}
				
				window.display();
			}

			continue;
		}
		bool scoreUpdated=false;
		if (!game && !scoreUpdated)
		{
			scoreUpdated=true;
		}
     	//=====Display powerups text=====//
		Text powertext;
		powertext.setFont(font);
		powertext.setCharacterSize(24);
		powertext.setFillColor(Color::White);
		powertext.setPosition(200, -5);
		powertext.setString("Power-Ups: " + to_string(powerups));
		//=======draw powerups text background=======//
		sf::RectangleShape powerbg(sf::Vector2f(170, 25));
		powerbg.setFillColor(sf::Color(139, 0, 0));
		powerbg.setPosition(190, -5);
		window.draw(powerbg);
		window.draw(powertext);
		//======Display scores======//
		Text scoretext;
		scoretext.setFont(font);
		scoretext.setCharacterSize(24);
		scoretext.setFillColor(Color::White);
		scoretext.setPosition(400, -5);
		scoretext.setString("Score: " + std::to_string(score));
		//=======draw score text background=======//
		sf::RectangleShape scorebg(sf::Vector2f(170, 25));
		scorebg.setFillColor(sf::Color(0, 139, 0));
		scorebg.setPosition(390, -5);
		window.draw(scorebg);
		window.draw(scoretext);
		//=====draw moves text=====//
		Text movestext;
		movestext.setFont(font);
		movestext.setStyle(Text::Bold);
		movestext.setCharacterSize(24);
		movestext.setFillColor(Color::White);
		movestext.setPosition(40, -5);
		movestext.setString("Moves: " + std::to_string(moves));
		//======DRaw moves text background=====//
		RectangleShape movesbg(Vector2f(150, 25));
		movesbg.setFillColor(Color(0, 0, 139));
		movesbg.setPosition(20, -3);
		window.draw(movesbg);
		window.draw(movestext);
		//=======draw timer text background=======//
		sf::RectangleShape timerbg(sf::Vector2f(170, 25));
		timerbg.setFillColor(sf::Color(140, 120, 0)); 
		timerbg.setPosition(590, -5);
		window.draw(timerbg);
		window.draw(timertext);

		window.display();
	}


    return 0;
}
