# Introduction to TicTacToe++
Source code for game. Copy everything below. 


// Tic Tac Toe App
// By: Abduni Idris

PVector savedMouse = new PVector(-1, -1);
//colors used for background
final int GREEN = color(70, 255, 65);
final int PURPLE = color(190, 30, 255);
final int PINK = color(255, 55, 255);
final int ORANGE = color(255, 113, 18);
final int BLUE = color(20, 250, 255);
final int RED = color (255, 30, 35);
final int BLACK = 0;

final int REFERENCE_WIDTH = 400;
final int MARGIN_X = 35;
final int MARGIN_Y = 50;

int cellWidth = 100;
int TurnClearWidth = 100;
int imageWidth = 40;
int TS = 5;


int[] randomColor = {BLUE, RED, PINK, ORANGE, PURPLE, GREEN};


int BACKGROUND = randomColor[int(random(randomColor.length))]; 
final int MAX_POS = 9;

PImage img=null;
PImage img2=null;
PImage img3=null;
//if false images wont be displayed. Used when debugging and testing program
Boolean webImages = true;

void setup() {
  loadImages();
  size(400, 600);
  adjustSize();
  drawboard();
}

void loadImages() {
  if (webImages) {
    img = loadImage("http://clipart-library.com/images/ki8nKzjbT.png");
    img2 = loadImage("http://www.clker.com/cliparts/t/O/R/K/k/f/funny-elephant-face-cartoon-md.png");
    img3 = loadImage("http://www2.sintonisd.net/discovery/wp-content/uploads/2013/03/star-for-animation.png");
  } else {
    img = createImage(50, 50, RGB);
    img2 = createImage(50, 50, RGB);
    img3 = createImage(50, 50, RGB);
  }
}

//scaling everything to fit new screen size
void adjustSize() {
  float frac = (float)width/REFERENCE_WIDTH;
  cellWidth*=frac;
  TurnClearWidth*=frac;
  imageWidth*=frac;
  TS*=frac;
  for (int i=0; i < xPositions.length; i++) {
    xPositions[i]*=frac;
  }
  for (int i=0; i < yPositions.length; i++) {
    yPositions[i]*=frac;
  }
  for (int i=0; i < xPositions2.length; i++) {
    xPositions2[i]*=frac;
  }
  for (int i=0; i < yPositions2.length; i++) {
    yPositions2[i]*=frac;
  }
  for (int i=0; i < xPositions3.length; i++) {
    xPositions3[i]*=frac;
  }
  for (int i=0; i < yPositions3.length; i++) {
    yPositions3[i]*=frac;
  }
}

int[] xPositions3={0, 2, 5, 20, 30, 65, 70, 110, 150, 181, 185, 235, 255, 295, 300, 400, 188, 80, 300,250};
int[] yPositions3={0, 30, 33, 35, 40, 50, 80, 85, 250, 400, 405, 425, 460, 480, 500, 550, 600, 475, 410, 450, 115, 430, 415};

void drawboard() {
  background(BACKGROUND);
  ellipseMode(CORNER);
  strokeWeight(5); // thickness of lines
  stroke(BLACK);  // black lines
  //four lines to draw the tic tac toe board
  line(xPositions[1], yPositions[0], xPositions[1], yPositions[9]);
  line(xPositions[2], yPositions[0], xPositions[2], yPositions[9]);
  line(xPositions[0], yPositions[6], xPositions[9], yPositions[6]);
  line(xPositions[0], yPositions[3], xPositions[9], yPositions[3]);
  placeWords();
  textSize(TS*4);
  fill(BLACK);
  text("New Game", xPositions3[13], yPositions3[15]);
  text("Scoreboard", xPositions3[5], yPositions3[15]);
  text("Skip", xPositions3[10], yPositions3[15]);
  displayTurn();
}

// used to take turns drawing Xs and Os
boolean Xsturn = true;

//updated each time mouse is clicked 
int prevFrameCount=0;

void mousePressed() { 
  //Because some phone processing apps register clicks too fast (multiple clicks registered in a row) a restriciton is created using framecount
  if (frameCount-prevFrameCount<15) {
    return;
  } 
  prevFrameCount=frameCount;
  //variables that represent x and y positions of the mouse and will be used to give values to positions (used in getPosition)
  savedMouse.x = mouseX;
  savedMouse.y = mouseY;
  int location = getPosition();
  // events that can occur on board page
  if (!showWords && !displayScores) {
    boardPageEvent(location);
    playerWin();
    //events that can occur on levels page
  } else if (showWords && !displayScores) {
    levelsPageEvent(location);
    // events that can occur on scoreboard page
  } else if (displayScores) {
    scoreboardPageEvent(location);
  } 
  //println("Turn Counter: " + turnCounter);
}

// saved values for each button on board page
final int NEWGAME = 10;
final int SKIP = 11;
final int SCOREBOARD = 70; 

void boardPageEvent(int location) {
  if (location < 10 && location > 0) {
    if (Xsturn) {
      drawX(location);
    } else {
      drawO(location);
    } 
    displayTurn();
  } else if (location==NEWGAME) {
    switchPage();
    turnCounter = 0;
    endgame = false;
  } else if (location==SKIP && !endgame) {
    //println("Skip Button");
    skipTurn();
    displayTurn();
  } else if (location==SCOREBOARD) {
    displayScores=true;
    scoreboard();
  }
}
void scoreboardPageEvent (int location) {
  if (location==SCOREBOARD) {
    displayScores=false;
    drawboard();
    restoreBoard();
  }
}
void levelsPageEvent(int location) {
  if (location <= 61 && location > 0) {
    selectedWords = levelsArray[location-1];
    // println("Displaying board for level " + location);
    shuffleWord(location);
    showWords=false;
    BACKGROUND=randomColor[int(random(randomColor.length))];
    resetGame();
  }
}

// All sound words in Team Read curriculum (61 levels)
String[][] levelsArray = {
  {"the", "and", "to", "of", "a", "in", "", "", ""}, 
  {"is", "that", "he", "you", "it", "for", "", "", ""}, 
  {"was", "are", "with", "on", "as", "his", "", "", ""}, 
  {"they", "be", "from", "at", "this", "I", "", "", ""}, 
  {"have", "by", "had", "or", "one", "not", "", "", ""}, 
  {"what", "if", "do", "all", "were", "we", "there", "can", "an", "your", "but", "when", "which"}, 
  {"their", "said", "will", "each", "about", "how", "up", "out", "them", "then", "she", "many"}, 
  {"so", "these", "would", "other", "into", "more", "her", "two", "like", "him", "some", "has", "see"}, 
  {"time", "could", "no", "make", "than", "first", "been", "its", "who", "now", "people", "my"}, 
  {"made", "down", "find", "water", "over", "only", "use", "get", "did", "way", "through", "may"}, 
  {"long", "little", "very", "after", "words", "called", "just", "where", "most", "know", "back", "much"}, 
  {"go", "good", "new", "write", "our", "used", "me", "man", "too", "any", "gas", "same", "right" }, 
  {"look", "think", "around", "also", "came", "come", "another", "work", "three", "word", "because", "must"}, 
  {"does", "part", "even", "place", "well", "such", "here", "take", "why", "things", "help", "before"}, 
  {"put", "years", "different", "way", "again", "off", "went", "old", "number", "sir", "seat", "divided", "underline", "circle", "view"}, 
  {"great", "tell", "men", "say", "small", "every", "found", "still", "between", "name", "should", "home", "Mr.", "big", "give"}, 
  {"air", "own", "line", "set", "under", "read", "us", "never", "last", "left", "end", "along", "while", "might", "next"}, 
  {"pounds", "below", "saw", "something", "thought", "both", "few", "those", "always", "looked", "show", "large", "row", "together", "lost"}, 
  {"house", "don't", "world", "going", "want", "school", "important", "until", "form", "food", "keep", "children", "feet", "land", "slide"}, 
  {"without", "boy", "once", "city", "life", "enough", "took", "sometimes", "four", "head", "above", "kind", "began", "almost", "broken"}, 
  {"five", "page", "got", "earth", "need", "far", "second", "suit", "eyes", "picture", "study", "hand", "high", "year", "mother"}, 
  {"light", "parts", "country", "father", "let", "night", "being", "following", "soon", "times", "story", "boys", "since", "white", "days"}, 
  {"ever", "paper", "hard", "near", "better", "best", "across", "college", "during", "today", "others", "gold", "sure", "means", "knew"}, 
  {"it's", "try", "told", "miles", "sun", "ways", "things", "whole", "hear", "example", "heard", "several", "build", "middle", "point"}, 
  {"change", "answer", "room", "sea", "against", "top", "usually", "city", "play", "toward", "five", "using", "himself", "money", "seen"}, 
  {"didn't", "car", "morning", "given", "trees", "I'm", "body", "upon", "later", "family", "milk", "turn", "move", "face", "door"}, 
  {"cut", "done", "group", "true", "half", "sentences", "red", "fish", "plants", "living", "wanted", "black", "United States", "eat", "short"}, 
  {"run", "kinds", "book", "gave", "order", "open", "ground", "American", "lines", "cold", "really", "table", "tree", "stone", "course"}, 
  {"front", "known", "party", "space", "inside", "ago", "making", "Mrs.", "early", "Seattle", "learned", "brought", "close", "nothing", "though"}, 
  {"started", "idea", "call", "lived", "makes", "became", "looking", "add", "become", "grow", "draw", "yet", "hands", "less", "wear"}, 
  {"wind", "places", "behind", "cannot", "letter", "among", "letters", "learn", "count", "comes", "able", "dog", "shown", "mean", "English"}, 
  {"rest", "perhaps", "certain", "six", "feel", "fire", "ready", "green", "yes", "built", "special", "ran", "full", "town", "complete"}, 
  {"oh", "hot", "seemed", "hold", "state", "list", "stood", "anything", "shows", "ten", "fast", "felt", "hundred", "kept", "person"}, 
  {"beat", "can't", "strong", "hole", "probably", "needed", "birds", "area", "horse", "tiny", "sounds", "matter", "stand", "box", "start"}, 
  {"that's", "class", "piece", "slowly", "surface", "river", "numbers", "common", "stop", "am", "talk", "quickly", "whether", "fine", "afternoon"}, 
  {"round", "dark", "meaning", "past", "ball", "poor", "tied", "coming", "blue", "instead", "girls", "road", "questions", "either", "held"}, 
  {"friends", "already", "warm", "taken", "gone", "finally", "summer", "understand", "animal", "mind", "moon", "outside", "problem", "trip", "says"}, 
  {"longer", "everyone", "dress", "deep", "heavy", "mountains", "beginning", "moved", "everything", "game", "carefully", "county", "winter", "leave", "follow"}, 
  {"bottom", "bring", "watch", "hair", "type", "chances", "act", "die", "real", "speak", "fingers", "doctor", "step", "itself", "nine"}, 
  {"baby", "minute", "ring", "wrote", "happen", "fourth", "bear", "swim", "heart", "appear", "I'll", "wall", "iron", "herself", "hill"}, 
  {"drop", "jumped", "smile", "son", "bat", "buy", "themselves", "floor", "sort", "fact", "tall", "fear", "care", "free", "king"}, 
  {"lake", "spring", "copy", "wonder", "twenty", "bed", "whose", "snow", "demand", "however", "figure", "case", "increase", "enjoy", "rather"}, 
  {"continue", "court", "human", "music", "sleep", "eleven", "force", "suppose", "sound", "law", "moment", "plant", "husband", "price", "result"}, 
  {"serve", "aunt", "cause", "supply", "condition", "national", "ride", "marry", "thousand", "pushed", "wife", "lie", "possible", "pen", "produce"}, 
  {"twelve", "rode", "uncle", "labor", "public", "president", "mark", "power", "least", "consider", "voice", "cross", "yourself", "control", "practice"}, 
  {"wild", "straight", "rise", "wide", "stick", "job", "seeds", "store", "woman", "coast", "bank", "period", "wire", "choose", "clean"}, 
  {"visit", "bit", "race", "recieved", "garden", "please", "strange", "caught", "fell", "team", "brother", "captain", "direct", "edge", "sat"}, 
  {"child", "desert", "present", "finished", "sign", "separate", "break", "arm", "beautiful", "flow", "lady", "students", "main", "art", "feeling"}, 
  {"cost", "legs", "insects", "drive", "skin", "maybe", "meet", "doctors", "hit", "won't", "corner", "sky", "climbed", "sand", "cook"}, 
  {"bones", "tail", "board", "grass", "ears", "catch", "foot", "fit", "wasn't", "mine", "safe", "key", "guess", "silent", "trade"}, 
  {"save", "cloud", "pole", "famous", "brown", "sent", "lost", "crowd", "blow", "wings", "interesting", "poem", "trouble", "string", "mouth"}, 
  {"exciting", "cool", "seven", "bad", "fun", "expect", "flat", "blood", "spot", "loud", "except", "thick", "branches", "bell", "alone"}, 
  {"equal", "thin", "engine", "entered", "fruit", "sight", "send", "dollars", "rich", "pay", "chief", "touch", "stream", "planets", "single"}, 
  {"eight", "science", "major", "yard", "tube", "burn", "lifted", "meat", "weight", "shouted", "rolled", "hat", "fight", "cat", "someone"}, 
  {"park", "sell", "shoulder", "age", "wash", "sail", "speed", "action", "spread", "block", "sharp", "bright", "hope", "radio", "we'll"}, 
  {"solve", "clothes", "truck", "third", "born", "flowers", "isn't", "fair", "ahead", "level", "settled", "yellow", "printed", "wouldn't", "triangle"}, 
  {"repeated", "glass", "agreed", "church", "western", "interested", "west", "wrong", "sister", "teacher", "prepared", "root", "chart", "soft", "opposite"}, 
  {"pretty", "length", "fresh", "shop", "simple", "especially", "shoes", "love", "nose", "afraid", "dead", "sugar", "paint", "eggs", "office"}, 
  {"huge", "wish", "similar", "rain", "score", "train", "stretched", "experience", "rose", "allow", "window", "workers", "Washington", "exercise", "women"}, 
  {"bought", "led", "march", "summer", "create", "doesn't", "win", "match", "difficult", "forest", "steel", "total", "deal", "dance", "evening"}, 
  {"test", "rope", "cotton", "apple", "details", "entire", "corn", "center", "smell", "tools", "square", "cows", "track", "arrived", "located"}
};

// setup in the drawWord method 
String[] selectedWords = levelsArray[0];


//even though its blank this method is needed so that everything in the program draws
void draw() {
}


//arrays of positions used on 
int[] xPositions = {35, 135, 235, 35, 135, 235, 35, 135, 235, 335};   
int[] yPositions = {50, 50, 50, 150, 150, 150, 250, 250, 250, 350};  
int[] boardState = {0, 0, 0, 0, 0, 0, 0, 0, 0, 0};

void placeWords() {
  for (int i=1; i <= MAX_POS; i++) {
    drawWord(i);
  }
}
// draw each word in a specific location on the board 
void drawWord(int position) {
  textSize(TS*4);
  textAlign(CENTER, CENTER);
  fill(BLACK);
  String word = selectedWords[position-1];
  if (boardState[position-1]==0 || boardState[position-1]==3) {
    text(word, xPositions[position-1]+(cellWidth*0.5), yPositions[position-1]+(cellWidth*0.5)); 
    boardState[position-1] = 3;
  }
}

void drawX(int position) {

  if (boardState[position-1]==3) {
    drawX0(position);
    boardState[position-1] = 1;
    turnCounter ++;
    Xsturn=false;
  }
}
void drawO(int position) {

  if (boardState[position-1]==3) {
    drawO0(position);
    boardState[position-1]=2;
    turnCounter ++;
    Xsturn=true;
  }
}

void drawX0(int position) {
  fill(BACKGROUND);
  noStroke();
  rect(xPositions[position-1]+5, yPositions[position-1]+5, (cellWidth*0.9), (cellWidth*0.9)); 
  stroke(BLACK);
  line(xPositions[position-1], yPositions[position-1], xPositions[position-1]+cellWidth, yPositions[position-1]+cellWidth);
  line(xPositions[position-1]+cellWidth, yPositions[position-1], xPositions[position-1], yPositions[position-1]+cellWidth);
}

void drawO0(int position) {
  stroke(BLACK);
  fill(BACKGROUND);
  ellipse(xPositions[position-1], yPositions[position-1], cellWidth, cellWidth);
}

String P1 = "Player 1: X";
String P2 = "Player 2: O";
int P1turn = 20;
int P2turn = 20;

void displayTurn () {
  if (!endgame){
  if (Xsturn && !showWords && !displayScores) {
    noStroke();
    fill(BACKGROUND);
    rect(xPositions3[0], yPositions3[9], (TurnClearWidth*3), TurnClearWidth);  
    P1turn = 30;
    P2turn = 20;
  } else if (!Xsturn && !showWords && !displayScores) {
    noStroke();
    fill(BACKGROUND);
    rect(xPositions3[0], yPositions3[9], (TurnClearWidth*3), TurnClearWidth); 
    P2turn = 30;
    P1turn = 20;
  }
  fill(BLACK);
  textSize(P1turn);
  text(P1, xPositions[1], yPositions3[11]);
  textSize(P2turn);
  text(P2, xPositions[1], yPositions3[17]);
  image(img, xPositions3[0], yPositions3[18], imageWidth, imageWidth); 
  image(img2, xPositions3[2], yPositions3[12], imageWidth, imageWidth);
  }
}

boolean endgame = false;

// changes boolean to allow user to skip turn
void skipTurn() {
  //println("Skip");
    Xsturn=!Xsturn;
}

int Player1wins = 0;
int Player2wins = 0;
int turnCounter = 0;



// Checks the board state to determine whether player 1 or player 2 wins or ties
void playerWin() {
  if ((boardState[0]==1 && boardState[1]==1 && boardState[2]==1) || (boardState[3]==1 && boardState[4]==1 && boardState[5]==1) || (boardState[6]==1 && boardState[7]==1 && boardState[8]==1) 
    || (boardState[0]==1 && boardState[3]==1 && boardState[6]==1) || (boardState[1]==1 && boardState[4]==1 && boardState[7]==1) || (boardState[2]==1 && boardState[5]==1 && boardState[8]==1) 
    || (boardState[0]==1 && boardState[4]==1 && boardState[8]==1) || (boardState[2]==1 && boardState[4]==1 && boardState[6]==1)) {
    noStroke();
    fill(BACKGROUND);
    rect(xPositions3[0], yPositions3[9], (TurnClearWidth*3), TurnClearWidth);
    P1 = "Player 1 WINS!";
    image(img, xPositions3[3], yPositions3[10], imageWidth*2, imageWidth*2);
    fill(BLACK);
    textSize(TS*6);
    text(P1, xPositions[2], yPositions3[11]);
    Player1wins ++;
    turnCounter = 0;
    endgame = true;
    // doesn't allow user to keep playing after a winner is declared
    for (int i=0; i < boardState.length; i++) {
      boardState[i]=4;
    }
  } else if ((boardState[0]==2 && boardState[1]==2 && boardState[2]==2) || (boardState[3]==2 && boardState[4]==2 && boardState[5]==2) || (boardState[6]==2 && boardState[7]==2 && boardState[8]==2) 
    || (boardState[0]==2 && boardState[3]==2 && boardState[6]==2) || (boardState[1]==2 && boardState[4]==2 && boardState[7]==2) || (boardState[2]==2 && boardState[5]==2 && boardState[8]==2) 
    || (boardState[0]==2 && boardState[4]==2 && boardState[8]==2) || (boardState[2]==2 && boardState[4]==2 && boardState[6]==2)) {
    noStroke();
    fill(BACKGROUND);
    rect(xPositions3[0], yPositions3[9], (TurnClearWidth*3), TurnClearWidth);
    P2 = "Player 2 WINS!";
    image(img2, xPositions3[3], yPositions3[10], imageWidth*2, imageWidth*2);
    fill(BLACK);
    textSize(TS*6);
    text(P2, xPositions[2], yPositions3[11]);
    Player2wins ++;
    turnCounter = 0;
    endgame = true;
    // doesn't allow user to keep playing after a winner is declared
    for (int i=0; i < boardState.length; i++) {
      boardState[i]=4;
    }
  } else {
    if (turnCounter==9) {
      noStroke();
      fill(BACKGROUND);
      rect(xPositions3[0], yPositions3[9], (TurnClearWidth*3), TurnClearWidth);
      fill(BLACK);
      textSize(TS*6);
      text("Its a TIE!", xPositions3[10], yPositions3[11]);
    }
  }
}

int getPosition() {
  if (!showWords && !displayScores) {
    //positions for buttons and board on board
    if (savedMouse.x >= xPositions[0] && savedMouse.x < xPositions[1] && savedMouse.y < yPositions[3] && savedMouse.y >= yPositions[0]) {
      return 1;
    } else if ((savedMouse.x >= xPositions[1] && savedMouse.x < xPositions[2]) && savedMouse.y < yPositions[4]) {
      return 2;
    } else if (savedMouse.x >= xPositions[3] && savedMouse.y < yPositions[5]) {
      return 3;
    } else if (savedMouse.x < xPositions[4] && savedMouse.y >= yPositions[5] && savedMouse.y < yPositions[6]) {
      return 4;
    } else if (savedMouse.x >= xPositions[7] && savedMouse.x < xPositions[5] && savedMouse.y >= yPositions[3] 
      && savedMouse.y < yPositions[7]) {
      return 5;
    } else if (savedMouse.x >= xPositions[2] && savedMouse.y >= yPositions[3] && savedMouse.y < yPositions[6]) {
      return 6;
    } else if (savedMouse.x < xPositions[1] && savedMouse.y >= yPositions[6] && savedMouse.y <= yPositions[9]) {
      return 7;
    } else if (savedMouse.x >= xPositions[1] && savedMouse.x < xPositions[2] && savedMouse.y >= yPositions[6] && savedMouse.y <= yPositions[9]) {
      return 8;
    } else if (savedMouse.x >= xPositions[2] && savedMouse.x < xPositions[9] && savedMouse.y < yPositions[9] && savedMouse.y >= yPositions[6]) {
      return 9;
    } else if (savedMouse.x >= xPositions3[19] && savedMouse.x <= xPositions3[15] && savedMouse.y >= yPositions3[14] && savedMouse.y <= yPositions3[16]) {
      return 10;
    } else if (savedMouse.x >= xPositions3[0]  && savedMouse.x <= xPositions[1]  && savedMouse.y >= yPositions3[14] && savedMouse.y <= yPositions3[16]) {
      return 70;
    } else if (savedMouse.x >= xPositions[1]  && savedMouse.x <= xPositions[2]  && savedMouse.y >= yPositions3[14]  && savedMouse.y < yPositions3[16]) {
      return 11;
    } 
    //positions for buttons on words page
  } else if (showWords && !displayScores) {
    // first column of levels
    if (savedMouse.x >= xPositions2[0] && savedMouse.x < xPositions2[1]) {
      if (savedMouse.y >= 0 && savedMouse.y <= yPositions2[0]) {
        return 1;
      }
      for (int i=0; i<17; i++) {
        if (savedMouse.y > yPositions2[i] && savedMouse.y <= yPositions2[i+1] ) {
          return i+2;
        }
      }
    }  
    // second column of levels
    if (savedMouse.x >= xPositions2[2] && savedMouse.x < xPositions2[3]) {
      if (savedMouse.y > 0 && savedMouse.y <= yPositions2[0]) {
        return 18;
      }
      for (int i=0; i<17; i++) {
        if (savedMouse.y > yPositions2[i] && savedMouse.y <= yPositions2[i+1]) {
          return i+19;
        }
      }
    }
    // third column of levels
    if (savedMouse.x >= xPositions2[4] && savedMouse.x < xPositions2[5]) {
      if (savedMouse.y > 0 && savedMouse.y <= yPositions2[0]) {
        return 35;
      } 
      for (int i=0; i<17; i++) {
        if (savedMouse.y > yPositions2[i] && savedMouse.y <= yPositions2[i+1]) {
          return i+36;
        }
      }
    }
    // fourth column of levels
    if (savedMouse.x >= xPositions2[5] && savedMouse.x < xPositions2[6]) {
      if (savedMouse.y > 0 && savedMouse.y <= yPositions2[0]) {
        return 52;
      } 
      for (int i=0; i<10; i++) {
        if (savedMouse.y > yPositions2[i] && savedMouse.y <= yPositions2[i+1]) {
          return i+53;
        }
      }
    }
  } 
  //position of BACK button on scoreboard page
  else {
    if (savedMouse.x >= xPositions3[0]  && savedMouse.x <= xPositions3[8]  && savedMouse.y >= yPositions3[14] && savedMouse.y <= yPositions3[16]) {
      return 70;
    }
  } 
  return -1;
}

// Starts a new game
void resetGame() {
  // Reset the state of the board
  for (int i=0; i < boardState.length; i++) {
    boardState[i]=0;
  }
  // Draw an X first after game is reset
  Xsturn=true;
  endgame = false;
  P1 = "Player 1: X";
  P2 = "Player 2: O";
  drawboard();
}

//
void restoreBoard() {
  // Use notTaken array to determine whether each position should be blank, have an X or circle
  for (int i=0; i< boardState.length; i++) {
    int pos = i+1;
    if (boardState[i]==1) {
      drawX0(pos);
    } else if (boardState[i]==2) {
      drawO0(pos);
    } else if (boardState[i]==3) {
      drawWord(pos);
    } else if (boardState[i]==4) {
      resetGame();
    }
  }
}

boolean showWords = false;
//displays either the levels page or the board 
void switchPage () {
  background(BACKGROUND); // clears the screen
  showWords=!showWords;  // flips whether we are showing words vs board
  if (showWords) {
    wordPage();
  } else {
    drawboard();
    restoreBoard();
  }
}

int[] xPositions2={0, 80, 100, 180, 200, 280, 350, 400};
int[] yPositions2={30, 60, 90, 120, 150, 180, 210, 240, 270, 300, 330, 360, 390, 420, 450, 480, 510};

void wordPage () {
  textAlign(LEFT, TOP);
  fill(BLACK);
  textSize(15);
  for (int i=0; i<17; i++) {
    text("Level " + (i+1), xPositions3[2], yPositions3[1]*i);
    text("Level " + (i+18), xPositions2[2], yPositions3[1]*i);
    text("Level " + (i+35), xPositions2[4], yPositions3[1]*i);
    int level = (i+52);
    // 4th column has different number of levels then the rest
    if (level <= 61) {
      text("Level " + (i+52), xPositions3[14], yPositions3[1]*i);
    }
  }
}


// shuffles words to randomly choose 9 words to draw onto board
void shuffleWord (int level) {
  String[] words = levelsArray[level-1];
  for (int i=1; i<words.length; i++) {
    int j = (int) random(i, words.length);
    String x= words[j];
    words[j]=words[i-1];
    words[i-1]=x;
  }
} 
//determines whether or not to show scoreboard page
boolean displayScores = false; 

void scoreboard () {
  background(BACKGROUND);
  stroke(BLACK);
  line(xPositions3[9], yPositions3[0], xPositions3[9], yPositions3[19]);
  line(xPositions3[0], yPositions3[6], xPositions3[15], yPositions3[6]);
  line(xPositions3[0], yPositions3[19], xPositions3[15], yPositions3[19]);
  line(xPositions3[0], yPositions3[22], xPositions3[15], yPositions3[22]);
  textSize(TS*5);
  fill(BLACK);
  text("Player 1: X", xPositions3[7], yPositions3[5]);
  text("Player 2: O", xPositions3[14], yPositions3[5]);
  text(Player1wins, xPositions3[17], yPositions3[21]);
  text(Player2wins, xPositions3[18], yPositions3[21]);
  text("BACK", xPositions3[6], yPositions3[15]);
  image(img, xPositions3[1], yPositions3[3], imageWidth, imageWidth);
  image(img2, xPositions3[16], yPositions3[2], imageWidth, imageWidth);
  image(img3, xPositions3[7], yPositions3[12], imageWidth*1.25, imageWidth*1.25);
  text("= Games Won", xPositions3[12], yPositions3[13]);
  int xStart = 0;
  int yStart = yPositions3[7];
  float w = imageWidth*0.75;
  int cols = 6;
  for (int i=0; i<Player1wins; i++) {
    image(img3, xStart+(i%cols)*w, yStart+(i/cols)*w, w, w);
  }
  xStart = xPositions3[9];
  for (int i=0; i<Player2wins; i++) {
    image(img3, xStart+(i%cols)*w, yStart+(i/cols)*w, w, w);
  }
}
