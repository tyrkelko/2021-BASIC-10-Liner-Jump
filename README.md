       10LINER JUMP

(c) 2021 by Kobi Tyrkel

This is a 10-liner jump game.

Game written for NOMAM's BASIC 10-liners Contest 2021

Should qualify for PUR-120 using abbreviations as in attached code image will not exceed 120 characters per logical line.

Developed for ATARI 800XL (Other atari 8bit should work, tested on real atari 800XL and on ALTIRRA emulator v3.1 RAM basic):

Requires:
- Atari XL/XE (or emulator)
- Atari BASIC cartridge/ROM (Internal BASIC - boot without OPTION key)
- Joystick

Load instructions:
- Boot JUMP.ATR disk
- File AUTORUN.BAS will automatically load and run the game. In case it doesn't, type: LOAD "D:AUTORUN.BAS" and type: RUN
- Press fire to start a the game
- Play the game
- Press fire to play again

Game instructions:
Press fire on Joystick 1 to start the game.
You are Jumping Joe. Your goal is to get the highest score possible, take a screen capture and post on social media with a link to https://github.com/tyrkelko/2021-BASIC-10-Liner-Jump and to the contest page.
You need to avoid the poisoned rocks by jumping over them, while not being eaten by the mighty eagle.
Every time the rock arrives at the edge of the screen you will earn 10 score points.
The rock and bird speed increases over time, so, beware. My 7yr old son beat me at 160 points, so, see if you could at least beat that.
Use joystick 1 to move in left, right or up.
You can start over by clicking "Fire" on the joystick.

See code in image provided with this file

Code explanation:

Game initialization

0GR.2:SE.2,4,4:POKE559,46:POKE53277,3:POKE704,32:POKE705,32:POKE706,4:DIMF$(1),F2$((INT(ADR(F$)/1024)+1)*1024-ADR(F$)-1)

GR.2 - Set text mode 2, Set color of text area to brown so it looks like something to walk on.
POKE 559,46 to set the player missiles line width to double line resolution
POKE 53277,3 to turn on player missile graphics
POKE 704-706 to set colors for 3 of our players (0, 1 and 2)
DIM F$(1) to set a variable of one byte in memory. F2$ is then set to fill memory in such way that the next variable will start at a new 1kb block as required for Player Missile Graphic memory location.

1DIMB$(384),M$(128),P0$(128),P1$(128),P2$(128),P3$(128),I1$(33),I2$(33),I3$(33),I4$(33),Z$(8):X2=0:B$=" ":POKE707,4:J=0

The next string arrays are structured to fit the memory structure of player missiles with double line, so, there is a buffer of 328, and the missiles and each of the players get 128 bytes according to that order.
Strings I1$-I4$ are alocated for the sprite animation of Joe with two frames on two players.
Z$ string will be used for holding the repeating melody loop.
X2 is initialized for holding the eagle x position.
B$ is assigned the char equivalent to int(0) - a heart shape which can be achieved by hitting ctrl+, on the ATARI.
POKE 707 assigns the color of the 4th player and is in this line for balancing the characters between lines.
J is initialized and will be used for changing Joe sprite animation and the bird sprite animation as well as running on the melody notes as an index.

2B$(384)=" ":B$(2)=B$:P0$=B$:P1$=B$:P2$=B$:P3$=B$:I2$="  `    4tr    p ` PP `@@@@@@@  @ ":POKE54279,ADR(B$)/256:Y0=65

B$(384) and B$(2) are initialized to int(0) using the equivalent character which can't be viewed in this text document and reset the buffer.
Now we go over all four players to reset them to zeros.

I2$ is assigned the right frame of Joe.

POKE 54729 sets the page number of the begining of the player missile graphics in memory.
Y0 is initialized with the vertical position of Joe so that he appears to stand on the ground.

3I1$="    @@    HD":I3$="    @@    HD        <NJ3":I4$="  `    4tr    p  P< r  `00j%! ~"

I1$, I3$ and I4$ are set to hold the strings representing Joes sprite map.

All sprites and melody byte representations were converted to strings and entered using the ATASCII And Internal Character Code Values in Appendix TEN of Mapping the Atari book.

The next line is our intro and end game state which reset jump sounds and prints a "PRESS FIRE" message until fire in pressed in joystick 1.

4P2$(90)="0L " L0":X1=1:POKE 53278,1:POS.4,5:?#6;"PRESS FIRE":S=PEEK(644):Z$="yy` QHQ":SOUND 1,0,0,0:IFS=1THENG.4

P2$ sets the poisoned stone sprite directly to sprite memory.
X1 - Stone position is set to 1 so it starts right after the position that grants the user 10 score points.
POKE 53278,1 resets the collision detected registers of our sprites.
Printing the "PRESS FIRE" message on screen.
S is taking the value of byte 644 which is equivalent to strig(0) and will return 1 if fire isn't clicked and 0 if it is. This also resets the score once fire is pressed and game begins.
Z$ is assigned the string representing melody from Donkey Kong which repeats notes C E GAG on and on.
SOUND 1,0,0,0 resets the voice of singer #1 which is used for the jump sounds.
If S=1 which means fire wasn't pressed, we loop to this line and once fire is clicked and S=0 we continue to the game main routine with S=0 which represents our players score.

Game main loop:

5 POKE 53248,X0:POKE 53249,X0+8:POKE 53250,230-X1:POKE 53251,230-X2:G=4+((PEEK(53262)=0)*(PEEK(53263)=0)):Q=ASC(Z$(F+1))

Addresses 53248-53251 are the players horizontal position.
53248 and 53248 are Joe's player0 and player1 graphics horizontal position. 8 bits appart so the appear one next to the other.
53250 is for the poisoned stone and is 230-X1 so that we see it coming from right to left and for ease of advancing it.
Same for 53251 which is for horizontal position of our eagle.
G represents the line number to go to after the main loop. It will be 4 if game was ended and 5 otherwise.
It checks for collision detection between players for the eagle and stone players and if not detected it will result in 4+1 so the return line will be 5 and game will continue. Otherwise, it will be set to 4 which will result in game ended loop at line 4.
Q is assigned the loop melody next note value to play.

6X1=(X1<220)*(X1+2+S/30):S=S+(X1=0)*10:X2=(X2<220)*(X2+S/9):F=(F<7)*(F+1):D=PEEK(632):J=(J=0)*(INT(D/2)=D/2)+J:Q=Q*(G=5)

X1 is the poisoned stone horizontal position which progresses faster and faster based on the score and resets to 0 if it is over 230 which is out of screen boundaries.
Same for X2 only with a different relation with score value for speed.
F is used as an index for the melody and repeats between 0 to 7. It is also used for determining when to switch sprite animation frame.
D will peek the value of the joystick and is a little faster than using stick(0).
J is being calculated to define what to add to the vertical position of Joe. 1 means jump, it will occur if Joe is currently not jumping, and the joystick value is even which corresponds with up-left, up and up-right values 10-14-6.
Q - the next note to play is set to 0 in case we identified the game is over so we'll reset the music and not leave a terrible annoying sound till user starts over.

7J=J-2*(J=1)*(Y0=52):Y0=Y0-J*2:J=J+(Y0=66)*(J=-1):X0=X0+2*(D=7)*(X0<150)-2*(D=11)*(X0>50):SO.0,Q,10,5:IFF<3THENG.9

J changes to -1 when Y height is 52 and to 0 when Joes is on the ground again.
Joes horizontal position is set between 50 and 150 using joystick right and left to increase of decrease.
Sound 0,Q,10,5 plays our next note
If F counted to 4 and on we'll skip the next line to change the second sprite frame or else we'll continue to the first frame for Joe and the eagle.

8SO.1,(Y0-40)*(Y0<>66),10,10:P0$(Y0)=I1$:P1$(Y0)=I2$:P3$(55)="Ak d":POS.4,5:?#6;"           ":X0=(X0<50)*50+X0:G.G

Only with the first frame we'll make a jump sound if on a jump.
P0$ and P1$ are Joes sprite and assigned with the first sprite frames images I1$ and I2$.
The eagle frame is written directly to sprite 3 memory.
Position 4, 5 is cleared with spaces to delete the "PRESS FIRE" message from the game end loop.

X0 is set to at least 50 if it is lower so since we didn't initialize it so we can avoid having it off screen.
Goto G will take us to the beginning of our game loop or game over according to value stored in G.

9POS.2,0:?#6;"SCORE: ";:?#6;S;:?#6;"   ":P0$(Y0)=I3$:P1$(Y0)=I4$:P3$(55)=" k  ":POKE707,64:SO.1,0,0,0:G.G

With this we update the score on the screen and delete any previous score characters that follow.
We also set the second frame for Joe sprites player 0 and 1 using I3$ and I4$. We also assign the second eagle frame directly to sprite memory.
Now we update the eagle sprite color with poke 707, set jump sound singer 1 to 0 and go to the start of the game loop or end game loop according to value assigned to G.

That's it... Jumping Joe in 10 lines PUR-120.

Thanks for reading this far :)
