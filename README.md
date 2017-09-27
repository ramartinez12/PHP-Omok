##  CS 3360 - Design and Implementation of Programming Languages
==================================================================	       
##	PROJECT 1: WEB SCRIPTING WITH PHP
------------------------------------------------------------------

**Due: October 3, 2017**

### The Game

You are to write a lightweight web service in PHP for playing omok
games. Omok, meaning "five pieces", is a two-player strategy game
typically played with go pieces, black and white stones, on a go board
with 15x15 intersections (or places) [Wikipedia]. It can also be
played with a paper and pencils because stones, once placed, are not
allowed to be moved or removed from the board. The two players
alternate in placing a stone of their color on an empty intersection,
and the goal of the game is to place one's stones in a row of five
consecutive intersections vertically, horizontally, or diagonally. The
winner is the first player to get an unbroken row of five stones.

### The Web Service

As a web service, your PHP code provides only a few APIs implementing
the logic, data or process for playing omok games, not a complete Web
application including a GUI. The web service APIs are predefined and
specified as URLs (see below). Key requirements for your web service
include:

	1. The web service shall work with a provided Java client available
	and downloadble from the course website (omokClient.jar).

	2. The web service shall support multiple clients concurrently, each
	client playing against the server (see R5 below). 

	3. A game board shall consist of 15*15 intersections (or places) on
	which black/white stones can be placed.

	4. An individual place of a board shall be identified by a pair (x,y)
	of 0-based column/row indices, e.g., (0,2) for the intersection
	of the first column and the third row.

	5. The web service shall provide at least two different move
	strategies for the computer, e.g., random and smart. The smart
	strategy shall be indeed "smart" to allow for a realistic
	play. Minimally, it should detect a winning/loosing row, i.e., a
	sequence of three stones with both ends open and four stones with
	at least one end open. Hint: Use the Strategy design patterns to
	support different computer move strategies [Chapter 5 of 2].

	6. The web service shall determine the outcome: win, loss, or draw.
	
#### APIs

The web service APIs are implemented as a set of URLs as specified
below. All communications between the web service and a client shall
be done using the HTTP query string and the JavaScript Object Notation
(JSON), a lightweight data-interchange format (see www.json.org)
[3]. All inputs to the web service shall be obtained from HTTP query
strings, and all outputs to the client shall be written in
JSON. Following the REST principles, the web service shall be
stateless and provide the following URL:

1. http://<omok-home>/info (short for .../info/ or .../info/index.php),
   where <omok-home> is the address of your omok service typically
   consisting of a host name and a pathname.
   Provide game information, including the board size and available
   computer move strategies. Below is a sample JSON output.

   {"size": 15, "strategies": ["Smart", "Random"]}

   Hint: Use json_encode() function to create a JSON (string)
   representation of a PHP value or object.

2. http://<omok-home>/new?strategy=s
   Create a new game to play against the specified computer strategy.
   A normal response will be a JSON string like:

     {"response": true, "pid": "55ed3eb95f5a3"}
     
   where pid is a unique play identifier generated by the web service. 
   It will be used to play the newly created game (see 3 below).

   Upon an error, it shall be notified to the client by providing an
   an appropriate error response like:

     {"response": false, "reason": "Strategy not specified"}
     {"response": false, "reason": "Unknown strategy"}

   Hint: Use uniqid() function to generate a unique identifier based
   on the current time in microseconds. Use the Strategy design pattern
   to define different strategy classes, e.g., RandomStrategy and
   SmartStrategy [Chapter 5 of 2].

3. http://<omok-home>/play?pid=p&move=x,y
   Make a move by placing a stone on the specified place (x,y) to play
   the specified game (p).

   A normal response will be a JSON string like:
     
     {"response": true,
      "ack_move": {
        "x": 4, 
        "y": 5, 
        "isWin": false,   // winning move?
	"isDraw": false,  // draw?
	"row": []},       // winning row if isWin is true
      "move": {
        "x": 4, 
        "y": 6, 
        "isWin": false, 
        "isDraw": false, 
        "row": []}}

   where the "ack_move" is the acknowledgement and outcome of the
   requested player move, and the "move" is the computer move made
   right after the player's; there will be no computer move if the
   player move is a game-ending (win or draw) move. For a winning
   move, the value of "row" is an array of numbers denoting the
   indices of the winning sequence [x1,y1,x2,y2,...,xn,yn], e.g.,

     "row":[6,7,7,7,8,7,9,7,10,7]

   If there is an error, it should be reported to the client by
   providing an appropriate error message like:

     {"response": false, "reason": "Pid not specified"}
     {"response": false, "reason": "Unknown pid"}
     {"response": false, "reason": "Move not specified"}
     {"response": false, "reason": "Move not well-formed"}
     {"response": false, "reason": "Invalid x coordinate, 20"}
     {"response": false, "reason": "Invalid y coordinate, 30"}

   Hint: Define several classes to model the omok game, say, Play,
   Board, etc. The states of some of these classes need to be stored
   externally, e.g., a file, because the web service sessions are
   stateless and the game state should be preserved between sessions.

   Hint: You may use the player identifier (pid) as a file name to
   store the game state, and the game state may be stored as or
   restored from a JSON string; use json_encode() and json_decode()
   functions.

   Hint: Use json_decode() function to convert a JSON encoded string
   into a PHP value or object.

 ### Testing
 
You code should be deployed and run correctly on the class website
at http://cs3360.cs.utep.edu. Refer to the FAQ page of the CS Tech
Support website for accessing the class website and uploading files
(http://csts.cs.utep.edu/faq/index.html; see the section "WebRelated").