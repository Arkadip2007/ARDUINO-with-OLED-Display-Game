# ARDUINO-with-OLED-Display 📟

<img src="public/ARDUINO-with-OLED-Display 01.jpg">

https://github.com/user-attachments/assets/48ad755c-c7cd-465b-91a9-31d1da794d67

> The above video is -> funal versan of the game.

</br>

<details>
  <summary style="opacity: 0.85;"><b>⚙️📌 Upload in GitHub without Login</b></summary><br>

If you want to **push just this one project folder** to a **different GitHub account/repo (temporarily)** using my **personal access token**, without affecting your existing projects or GitHub account setup in VS Code.

---

## ✅ Steps to Upload This Folder to That Repo (Temporarily)

### 🐦 1. Open terminal in the project folder

```bash
cd path/to/ARDUINO-with-OLED-Display
```

### 🐦 2. Set Git user for just that project:
Open the terminal in that project folder and run:

```bash
git config user.name "Their Name"
git config user.email "their-email@example.com"
```

This overrides your global Git identity only for this repo. So your commits will look like they came from them (or from whoever should be listed as contributor).

### 🐦 3. Initialize Git (if not already)

```bash
git init
```

### 🐦 4. Add and commit all files

```bash
git add .
git commit -m "Initial commit for OLED project"
```

### 🐦 5. Add the remote with token (for one-time push)

![Screenshot (328)](https://github.com/user-attachments/assets/b66abca0-c6e4-4285-aa8b-4fc74923ccdc)

Replace `<YOUR_TOKEN_HERE>` with your actual **Personal Access Token**:

```bash
git remote add origin https://<YOUR_TOKEN_HERE>@github.com/Arkadip2007/ARDUINO-with-OLED-Display.git
```

**Important**: Don’t put `<>` around the token when actually typing.

✅ Example:
```bash
git remote add origin https://ghp_abcd123456789yourtoken@github.com/Arkadip2007/ARDUINO-with-OLED-Display.git
```

> This way it won’t ask for username/password and uses your token directly.

### 🐦 6. Set the branch name (if needed)

```bash
git branch -M main
```

### 🚀 6. Push to GitHub

```bash
git push -u origin main
```

---

## ✅ Optional: Clean up after push (secure)

After you're done, you can **remove the remote with token** to avoid accidentally leaking it:

```bash
git remote set-url origin https://github.com/Arkadip2007/ARDUINO-with-OLED-Display.git
```

Or just delete the `.git` folder if this was a one-time thing:

```bash
rm -rf .git
```

</details>

---

## only check the Display working or not ?

### Wiring Connections:

```go
OLED VCC -> Arduino 5V
OLED GND -> Arduino GND
OLED SDA -> Arduino A4
OLED SCL -> Arduino A5
Jump Button -> Arduino D2 (other leg to GND), using INPUT_PULLUP
```

![ARDUINO-with-OLED-Display](https://github.com/user-attachments/assets/9df7aa80-0795-4f0e-8859-f09b91ff5278)

https://github.com/user-attachments/assets/2608ca3f-f541-46a6-96dd-fb340bdfc8a1

---

```cpp
#include <Arduino.h>
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64

#define OLED_RESET -1
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, OLED_RESET);

void setup() {
  Serial.begin(9600);

  if (!display.begin(SSD1306_SWITCHCAPVCC, 0x3C)) {
    Serial.println(F("SSD1306 allocation failed"));
    while (true);
  }

  display.clearDisplay();
  display.setTextSize(2);
  display.setTextColor(SSD1306_WHITE);
  display.setCursor(0, 10);
  display.println("Hello");
  display.setCursor(0, 35);
  display.println("Arkadip :)");
  display.display();
}

void loop() {
  // Nothing here for now
}
```

### .ini `(libraryes)`
```ini
[env:uno]
platform = atmelavr
board = uno
framework = arduino
monitor_speed = 9600

lib_deps =
  adafruit/Adafruit SSD1306@^2.5.7
  adafruit/Adafruit GFX Library@^1.11.9
```

---

### create a Game 🎯


<details>
  <summary style="opacity: 0.85;"><b>🎯 old game code 0.1</b></summary><br>

https://github.com/user-attachments/assets/c07c5469-c741-4694-b076-7ea87a0b5272

> For the best verson of code --> goes down of this page

```cpp
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64
#define OLED_RESET -1
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, OLED_RESET);

#define BUTTON_PIN 2

int playerX = 0;
int playerY = 50; // moved down
bool isJumping = false;
int jumpCounter = 0;
int groundLevel = 56; // moved down
int obstacleX = SCREEN_WIDTH;
int score = 0;
bool gameStarted = false;

void setup() {
  pinMode(BUTTON_PIN, INPUT_PULLUP);
  display.begin(SSD1306_SWITCHCAPVCC, 0x3C);
  display.clearDisplay();
  display.setTextSize(1);
  display.setTextColor(SSD1306_WHITE);
  display.display();
}

void loop() {
  if (!gameStarted && digitalRead(BUTTON_PIN) == LOW) {
    gameStarted = true;
    delay(200);
  }

  if (!gameStarted) {
    display.clearDisplay();
    display.setCursor(0, 30);
    display.print("Press button to start");
    display.display();
    return;
  }

  if (digitalRead(BUTTON_PIN) == LOW && !isJumping) {
    isJumping = true;
    jumpCounter = 0;
  }

  if (isJumping) {
    if (jumpCounter < 10) {
      playerY -= 2;
    } else if (jumpCounter < 20) {
      playerY += 2;
    } else {
      isJumping = false;
      playerY = groundLevel - 6;
    }
    jumpCounter++;
  }

  obstacleX -= 2;
  if (obstacleX < 0) {
    obstacleX = SCREEN_WIDTH;
    score++;
  }

  display.clearDisplay();

  // Score (stay at top)
  display.setCursor(0, 0);
  display.print("Score: ");
  display.print(score);

  // Ground (moved down)
  display.setCursor(0, groundLevel);
  display.print("--------------------");

  // Player
  display.setCursor(playerX, playerY);
  display.print("I");

  // Obstacle
  display.setCursor(obstacleX, groundLevel - 6);
  display.print("#");

  // Collision detection
  if (obstacleX <= playerX + 5 && obstacleX >= playerX) {
    if (playerY >= groundLevel - 6) {
      display.setCursor(0, 20);
      display.print("Game Over!");
      display.display();
      delay(2000);
      score = 0;
      obstacleX = SCREEN_WIDTH;
      gameStarted = false;
      return;
    }
  }

  display.display();
  delay(50);
}
```

</details>

<details>
  <summary style="opacity: 0.85;"><b>🎯 old code 0.2</b></summary><br>

https://github.com/user-attachments/assets/9f0eb908-9c17-4df7-9ebb-b0a0df6821a1

> For the best verson of code --> goes down of this page
  
```cpp
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64
#define OLED_RESET -1
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, OLED_RESET);

#define BUTTON_PIN 2

int playerX = 5;
int playerY = 50;
bool isJumping = false;
int jumpCounter = 0;
int groundLevel = 56;
int obstacleX = SCREEN_WIDTH;
int score = 0;
bool gameStarted = false;

void drawPlayer(int x, int y, bool jumping) {
  // Head
  display.fillCircle(x + 2, y - 6, 2, SSD1306_WHITE);
  // Body
  display.drawLine(x + 2, y - 4, x + 2, y, SSD1306_WHITE);

  if (jumping) {
    // Jumping legs (spread)
    display.drawLine(x + 2, y, x, y + 3, SSD1306_WHITE);
    display.drawLine(x + 2, y, x + 4, y + 3, SSD1306_WHITE);
  } else {
    // Standing legs
    display.drawLine(x + 2, y, x + 2, y + 4, SSD1306_WHITE);
  }

  // Arms
  display.drawLine(x + 2, y - 3, x, y - 2, SSD1306_WHITE);
  display.drawLine(x + 2, y - 3, x + 4, y - 2, SSD1306_WHITE);
}

void drawObstacle(int x) {
  display.fillRect(x, groundLevel - 8, 6, 8, SSD1306_WHITE);
}

void setup() {
  pinMode(BUTTON_PIN, INPUT_PULLUP);
  display.begin(SSD1306_SWITCHCAPVCC, 0x3C);
  display.clearDisplay();
  display.setTextSize(1);
  display.setTextColor(SSD1306_WHITE);
  display.display();
}

void loop() {
  if (!gameStarted && digitalRead(BUTTON_PIN) == LOW) {
    gameStarted = true;
    delay(200);
  }

  if (!gameStarted) {
    display.clearDisplay();
    display.setCursor(0, 30);
    display.print("Press button to start");
    display.display();
    return;
  }

  if (digitalRead(BUTTON_PIN) == LOW && !isJumping) {
    isJumping = true;
    jumpCounter = 0;
  }

  if (isJumping) {
    if (jumpCounter < 10) {
      playerY -= 2;
    } else if (jumpCounter < 20) {
      playerY += 2;
    } else {
      isJumping = false;
      playerY = groundLevel - 6;
    }
    jumpCounter++;
  }

  obstacleX -= 2;
  if (obstacleX < -6) {
    obstacleX = SCREEN_WIDTH;
    score++;
  }

  display.clearDisplay();

  // Score
  display.setCursor(0, 0);
  display.print("Score: ");
  display.print(score);

  // Ground
  display.drawLine(0, groundLevel + 4, SCREEN_WIDTH, groundLevel + 4, SSD1306_WHITE);

  // Draw player and obstacle
  drawPlayer(playerX, playerY, isJumping);
  drawObstacle(obstacleX);

  // Collision
  if (obstacleX <= playerX + 4 && obstacleX + 6 >= playerX) {
    if (playerY + 6 >= groundLevel - 4) {
      display.setCursor(30, 20);
      display.print("Game Over!");
      display.display();
      delay(2000);
      score = 0;
      obstacleX = SCREEN_WIDTH;
      gameStarted = false;
      return;
    }
  }

  display.display();
  delay(50);
}
```
</details>
<details>
  <summary style="opacity: 0.85;"><b>🎯 old code 0.3</b></summary><br>

###  features added:

- Random obstacle types: box 📦, tree 🌲🌳, bird 🐦
- Gradually increasing speed
- Motivational messages every 10 points

> For the best verson of code --> goes down of this page

```cpp
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64
#define OLED_RESET -1
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, OLED_RESET);

#define BUTTON_PIN 2

int playerX = 5;
int playerY = 50;
bool isJumping = false;
int jumpCounter = 0;
int groundLevel = 56;
int obstacleX = SCREEN_WIDTH;
int obstacleType = 0;
int score = 0;
bool gameStarted = false;
int gameSpeed = 50;
bool showMotivation = false;
String motivationMessage = "";

void drawPlayer(int x, int y, bool jumping) {
  display.fillCircle(x + 2, y - 6, 2, SSD1306_WHITE); // Head
  display.drawLine(x + 2, y - 4, x + 2, y, SSD1306_WHITE); // Body

  if (jumping) {
    display.drawLine(x + 2, y, x, y + 3, SSD1306_WHITE);
    display.drawLine(x + 2, y, x + 4, y + 3, SSD1306_WHITE);
  } else {
    display.drawLine(x + 2, y, x + 2, y + 4, SSD1306_WHITE);
  }

  display.drawLine(x + 2, y - 3, x, y - 2, SSD1306_WHITE); // Arms
  display.drawLine(x + 2, y - 3, x + 4, y - 2, SSD1306_WHITE);
}

void drawObstacle(int x, int type) {
  if (type == 0) {
    // Box
    display.fillRect(x, groundLevel - 8, 6, 8, SSD1306_WHITE);
  } else if (type == 1) {
    // Tree (trunk + leaves)
    display.fillRect(x + 2, groundLevel - 6, 2, 6, SSD1306_WHITE); // Trunk
    display.fillCircle(x + 3, groundLevel - 8, 3, SSD1306_WHITE);  // Leaves
  } else if (type == 2) {
    // Bird (simple V shape)
    display.drawPixel(x, groundLevel - 16, SSD1306_WHITE);
    display.drawPixel(x + 1, groundLevel - 17, SSD1306_WHITE);
    display.drawPixel(x + 2, groundLevel - 16, SSD1306_WHITE);
  }
}

void setup() {
  pinMode(BUTTON_PIN, INPUT_PULLUP);
  display.begin(SSD1306_SWITCHCAPVCC, 0x3C);
  display.clearDisplay();
  display.setTextSize(1);
  display.setTextColor(SSD1306_WHITE);
  display.display();
}

void loop() {
  if (!gameStarted && digitalRead(BUTTON_PIN) == LOW) {
    gameStarted = true;
    delay(200);
  }

  if (!gameStarted) {
    display.clearDisplay();
    display.setCursor(0, 30);
    display.print("Press button to start");
    display.display();
    return;
  }

  if (digitalRead(BUTTON_PIN) == LOW && !isJumping) {
    isJumping = true;
    jumpCounter = 0;
  }

  if (isJumping) {
    if (jumpCounter < 10) {
      playerY -= 2;
    } else if (jumpCounter < 20) {
      playerY += 2;
    } else {
      isJumping = false;
      playerY = groundLevel - 6;
    }
    jumpCounter++;
  }

  obstacleX -= 3;
  if (obstacleX < -10) {
    obstacleX = SCREEN_WIDTH;
    obstacleType = random(0, 3);
    score++;

    if (score % 10 == 0) {
      showMotivation = true;
      motivationMessage = (random(0, 2) == 0) ? "Great job!" : "Quick reflex!";
    } else {
      showMotivation = false;
    }

    if (gameSpeed > 20) gameSpeed -= 2;
  }

  display.clearDisplay();

  display.setCursor(0, 0);
  display.print("Score: ");
  display.print(score);

  if (showMotivation) {
    display.setCursor(30, 10);
    display.print(motivationMessage);
  }

  display.drawLine(0, groundLevel + 4, SCREEN_WIDTH, groundLevel + 4, SSD1306_WHITE);

  drawPlayer(playerX, playerY, isJumping);
  drawObstacle(obstacleX, obstacleType);

  if (obstacleX <= playerX + 4 && obstacleX + 6 >= playerX) {
    if (playerY + 6 >= groundLevel - 4) {
      display.setCursor(30, 20);
      display.print("Game Over!");
      display.display();
      delay(2000);
      score = 0;
      obstacleX = SCREEN_WIDTH;
      gameSpeed = 50;
      gameStarted = false;
      return;
    }
  }

  display.display();
  delay(gameSpeed);
}
```

</details>
<details>
  <summary style="opacity: 0.85;"><b>🎯 old code 0.4</b></summary><br>

###  features added:

- Add music or sound (with buzzer)
- Store high score in EEPROM
- Show WELLCOME SCREEN before the game starts

> For the best verson of code --> goes down of this page

```cpp
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64
#define OLED_RESET -1
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, OLED_RESET);

#define BUTTON_PIN 2

int playerX = 5;
int playerY = 50;
bool isJumping = false;
int jumpCounter = 0;
int groundLevel = 56;
int obstacleX = SCREEN_WIDTH;
int obstacleType = 0;
int score = 0;
bool gameStarted = false;
int gameSpeed = 50;

void drawPlayer(int x, int y, bool jumping) {
  display.fillCircle(x + 2, y - 6, 2, SSD1306_WHITE);              // Head
  display.drawLine(x + 2, y - 4, x + 2, y, SSD1306_WHITE);         // Body

  if (jumping) {
    display.drawLine(x + 2, y, x, y + 3, SSD1306_WHITE);           // Legs
    display.drawLine(x + 2, y, x + 4, y + 3, SSD1306_WHITE);
  } else {
    display.drawLine(x + 2, y, x + 2, y + 4, SSD1306_WHITE);       // Standing leg
  }

  display.drawLine(x + 2, y - 3, x, y - 2, SSD1306_WHITE);         // Arms
  display.drawLine(x + 2, y - 3, x + 4, y - 2, SSD1306_WHITE);
}

void drawObstacle(int x, int type) {
  if (type == 0) {
    display.fillRect(x, groundLevel - 8, 6, 8, SSD1306_WHITE);           // Box
  } else if (type == 1) {
    display.fillRect(x + 2, groundLevel - 6, 2, 6, SSD1306_WHITE);       // Tree trunk
    display.fillCircle(x + 3, groundLevel - 8, 3, SSD1306_WHITE);        // Leaves
  } else if (type == 2) {
    display.drawPixel(x, groundLevel - 16, SSD1306_WHITE);              // Bird (V shape)
    display.drawPixel(x + 1, groundLevel - 17, SSD1306_WHITE);
    display.drawPixel(x + 2, groundLevel - 16, SSD1306_WHITE);
  }
}

void showWelcomeScreen() {
  display.clearDisplay();
  display.setTextSize(2);
  display.setCursor(0, 10);
  String title = "WELCOME TO";
  for (int i = 0; i < title.length(); i++) {
    display.print(title[i]);
    display.display();
    delay(80);
  }

  display.setCursor(0, 32);
  String title2 = "THE GAME";
  for (int i = 0; i < title2.length(); i++) {
    display.print(title2[i]);
    display.display();
    delay(80);
  }

  display.setTextSize(1);
  display.setCursor(0, 52);
  String sub = "Made by ARKADIP MAHAPATRA";
  for (int i = 0; i < sub.length(); i++) {
    display.print(sub[i]);
    display.display();
    delay(60);
  }

  delay(1500);
  display.clearDisplay();
  display.display();
}

void setup() {
  pinMode(BUTTON_PIN, INPUT_PULLUP);
  display.begin(SSD1306_SWITCHCAPVCC, 0x3C);
  display.setTextColor(SSD1306_WHITE);
  showWelcomeScreen();
}

void loop() {
  if (!gameStarted && digitalRead(BUTTON_PIN) == LOW) {
    gameStarted = true;
    delay(200);
  }

  if (!gameStarted) {
    display.clearDisplay();
    display.setCursor(0, 30);
    display.setTextSize(1);
    display.print("Press button to start");
    display.display();
    return;
  }

  if (digitalRead(BUTTON_PIN) == LOW && !isJumping) {
    isJumping = true;
    jumpCounter = 0;
  }

  if (isJumping) {
    if (jumpCounter < 10) {
      playerY -= 2;
    } else if (jumpCounter < 20) {
      playerY += 2;
    } else {
      isJumping = false;
      playerY = groundLevel - 6;
    }
    jumpCounter++;
  }

  obstacleX -= 2;
  if (obstacleX < -6) {
    obstacleX = SCREEN_WIDTH;
    obstacleType = random(0, 3);
    score++;

    if (score % 10 == 0 && score != 0) {
      display.clearDisplay();
      display.setCursor(0, 20);
      if (score % 20 == 0) {
        display.print("GREAT JOB!");
      } else {
        display.print("QUICK RESPONSE!");
      }
      display.display();
      delay(1000);
    }

    if (gameSpeed > 20) gameSpeed -= 2;
  }

  display.clearDisplay();
  display.setCursor(0, 0);
  display.setTextSize(1);
  display.print("Score: ");
  display.print(score);

  display.drawLine(0, groundLevel + 4, SCREEN_WIDTH, groundLevel + 4, SSD1306_WHITE);

  drawPlayer(playerX, playerY, isJumping);
  drawObstacle(obstacleX, obstacleType);

  // Collision detection
  if (obstacleX <= playerX + 4 && obstacleX + 6 >= playerX) {
    if (playerY + 6 >= groundLevel - 4) {
      display.setCursor(30, 20);
      display.print("Game Over!");
      display.display();
      delay(2000);
      score = 0;
      obstacleX = SCREEN_WIDTH;
      gameStarted = false;
      gameSpeed = 50;
      return;
    }
  }

  display.display();
  delay(gameSpeed);
}
```
</details>
<details>
  <summary style="opacity: 0.85;"><b>🎯 old code 0.5 ✅</b></summary><br>

###  features added:

https://github.com/user-attachments/assets/ad962201-deb9-49cd-b17d-0b9988c45b53

> For the best verson of code --> goes down of this page
    
```cpp
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64
#define OLED_RESET -1
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, OLED_RESET);

#define BUTTON_PIN 2

int playerX = 5;
int playerY = 50;
bool isJumping = false;
int jumpCounter = 0;
int groundLevel = 56;

int obstacleX = SCREEN_WIDTH;
int obstacleType = 0;

int score = 0;
bool gameStarted = false;
bool welcomeDone = false;
bool hardMode = false;
bool inCountdown = false;

int gameSpeed = 50;

// ========== PLAYER ============
void drawPlayer(int x, int y, bool jumping) {
  display.fillCircle(x + 2, y - 6, 2, SSD1306_WHITE); // Head
  display.drawLine(x + 2, y - 4, x + 2, y, SSD1306_WHITE); // Body
  display.drawLine(x + 2, y - 3, x, y - 2, SSD1306_WHITE); // Arms
  display.drawLine(x + 2, y - 3, x + 4, y - 2, SSD1306_WHITE);
  if (jumping) {
    display.drawLine(x + 2, y, x, y + 3, SSD1306_WHITE); // Jumping legs
    display.drawLine(x + 2, y, x + 4, y + 3, SSD1306_WHITE);
  } else {
    display.drawLine(x + 2, y, x + 1, y + 4, SSD1306_WHITE); // Standing legs
    display.drawLine(x + 2, y, x + 3, y + 4, SSD1306_WHITE);
  }
}

// ========== OBSTACLES ============
void drawObstacle(int x, int type) {
  switch (type) {
    case 0: // box
      display.fillRect(x, groundLevel - 8, 6, 8, SSD1306_WHITE);
      break;
    case 1: // tree
      display.fillRect(x + 2, groundLevel - 10, 2, 10, SSD1306_WHITE); // trunk
      display.fillTriangle(x - 2, groundLevel - 10, x + 3, groundLevel - 16, x + 8, groundLevel - 10, SSD1306_WHITE);
      break;
    case 2: // bird
      display.drawPixel(x, groundLevel - 16, SSD1306_WHITE);
      display.drawPixel(x + 1, groundLevel - 17, SSD1306_WHITE);
      display.drawPixel(x + 2, groundLevel - 16, SSD1306_WHITE);
      break;
  }
}

// ========== SCREENS ============
void showWelcomeScreen() {
  display.clearDisplay();
  display.setTextSize(2);
  display.setTextColor(SSD1306_WHITE);
  display.setCursor(0, 15);
  display.println("WELCOME TO");
  display.setCursor(0, 35);
  display.println("THE GAME");
  display.display();
  delay(2000);

  display.clearDisplay();
  display.setTextSize(1);
  display.setCursor(25, 15);
  display.println("made by:");
  display.setCursor(0, 35);
  display.println("ARKADIP MAHAPATRA");
  display.display();
  delay(2000);
  welcomeDone = true;
}

void showDifficultyScreen(bool selectedHard, int countdown) {
  display.clearDisplay();
  display.setTextSize(1);
  display.setCursor(5, 15);
  display.print("Select Difficulty:");
  display.setCursor(25, 35);
  display.print(selectedHard ? "  Easy   >Hard<" : ">Easy<   Hard");

  display.setCursor(20, 50);
  display.print("Starting in: ");
  display.print(countdown);
  display.display();
}

// ========== SETUP ============
void setup() {
  pinMode(BUTTON_PIN, INPUT_PULLUP);
  display.begin(SSD1306_SWITCHCAPVCC, 0x3C);
  display.clearDisplay();
  display.display();
}

// ========== MAIN LOOP ============
void loop() {
  static bool waitingToStart = true;
  static bool difficultySelected = false;
  static unsigned long countdownStartTime = 0;
  static int countdown = 3;
  static unsigned long lastCountdownUpdate = 0;

  // Show welcome screen once
  if (!welcomeDone) {
    showWelcomeScreen();
    welcomeDone = true;
    return;
  }

  // Start screen: wait for first press
  if (!gameStarted && waitingToStart) {
    display.clearDisplay();
    display.setTextSize(1);
    display.setCursor(0, 30);
    display.print("Press button to start");
    display.display();

    if (digitalRead(BUTTON_PIN) == LOW) {
      delay(200); // debounce
      waitingToStart = false;
      countdownStartTime = millis();
      lastCountdownUpdate = millis();
      countdown = 3;
      inCountdown = true;
    }
    return;
  }

  // Difficulty selection with countdown
  if (!gameStarted && inCountdown) {
    // Handle button press to toggle difficulty
    if (digitalRead(BUTTON_PIN) == LOW) {
      delay(200); // debounce
      hardMode = !hardMode;
      countdown = 3;
      countdownStartTime = millis();
      lastCountdownUpdate = millis();
    }

    // Update countdown every second
    if (millis() - lastCountdownUpdate >= 1000) {
      countdown--;
      lastCountdownUpdate = millis();
    }

    showDifficultyScreen(hardMode, countdown);

    if (countdown <= 0) {
      inCountdown = false;
      gameStarted = true;
      gameSpeed = hardMode ? 30 : 50;
    }
    return;
  }

  // ================= GAME LOOP =================
  if (digitalRead(BUTTON_PIN) == LOW && !isJumping) {
    isJumping = true;
    jumpCounter = 0;
    delay(100);
  }

  if (isJumping) {
    if (jumpCounter < 10) {
      playerY -= 2;
    } else if (jumpCounter < 20) {
      playerY += 2;
    } else {
      isJumping = false;
      playerY = groundLevel - 6;
    }
    jumpCounter++;
  }

  obstacleX -= 2;
  if (obstacleX < -10) {
    obstacleX = SCREEN_WIDTH;
    obstacleType = random(0, 3);
    score++;

    if (score % 10 == 0) {
      display.clearDisplay();
      display.setCursor(0, 20);
      display.setTextSize(1);
      if (score % 20 == 0)
        display.print("YOU REALLY HAVE QUICK REFLEXES!");
      else
        display.print("Great job!");
      display.display();
      delay(1500);
    }

    if (hardMode && gameSpeed > 25) gameSpeed--;
  }

  display.clearDisplay();
  display.setTextSize(1);
  display.setCursor(0, 0);
  display.print("Score: ");
  display.print(score);

  display.drawLine(0, groundLevel + 4, SCREEN_WIDTH, groundLevel + 4, SSD1306_WHITE);
  drawPlayer(playerX, playerY, isJumping);
  drawObstacle(obstacleX, obstacleType);

  // Collision detection
  if (obstacleX <= playerX + 4 && obstacleX + 6 >= playerX) {
    if (playerY + 6 >= groundLevel - 6) {
      display.setCursor(30, 20);
      display.print("Game Over!");
      display.display();
      delay(2000);

      // Reset game state
      score = 0;
      obstacleX = SCREEN_WIDTH;
      gameStarted = false;
      waitingToStart = true;
      hardMode = false;
      inCountdown = false;
      return;
    }
  }

  display.display();
  delay(gameSpeed);
}
```
</details>

### 🎯 final GAME code 0.6

> For the best verson of code --> goes down of this page

https://github.com/user-attachments/assets/80d1986c-a755-4a24-8fe5-f48015c9af53

```cpp
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64
#define OLED_RESET -1
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, OLED_RESET);

#define BUTTON_PIN 2

int playerX = 5;
int playerY = 50;
bool isJumping = false;
int jumpCounter = 0;
int groundLevel = 56;

int obstacleX = SCREEN_WIDTH;
int obstacleType = 0;

int score = 0;
bool gameStarted = false;
bool welcomeDone = false;
bool hardMode = false;
bool inCountdown = false;

int gameSpeed = 50;

// ========== PLAYER ============
void drawPlayer(int x, int y, bool jumping) {
  display.fillCircle(x + 2, y - 6, 2, SSD1306_WHITE); // Head
  display.drawLine(x + 2, y - 4, x + 2, y, SSD1306_WHITE); // Body
  display.drawLine(x + 2, y - 3, x, y - 2, SSD1306_WHITE); // Arms
  display.drawLine(x + 2, y - 3, x + 4, y - 2, SSD1306_WHITE);
  if (jumping) {
    display.drawLine(x + 2, y, x, y + 3, SSD1306_WHITE); // Jumping legs
    display.drawLine(x + 2, y, x + 4, y + 3, SSD1306_WHITE);
  } else {
    display.drawLine(x + 2, y, x + 1, y + 4, SSD1306_WHITE); // Standing legs
    display.drawLine(x + 2, y, x + 3, y + 4, SSD1306_WHITE);
  }
}

// ========== OBSTACLES ============
void drawObstacle(int x, int type) {
  switch (type) {
    case 0: // box
      display.fillRect(x, groundLevel - 8, 6, 8, SSD1306_WHITE);
      break;
    case 1: // tree
      display.fillRect(x + 2, groundLevel - 10, 2, 10, SSD1306_WHITE); // trunk
      display.fillTriangle(x - 2, groundLevel - 10, x + 3, groundLevel - 16, x + 8, groundLevel - 10, SSD1306_WHITE);
      break;
    case 2: // bird
      display.fillCircle(x + 2, groundLevel - 20, 2, SSD1306_WHITE); // body
      display.drawPixel(x + 1, groundLevel - 22, SSD1306_WHITE); // wing top
      display.drawPixel(x + 3, groundLevel - 22, SSD1306_WHITE);
    break;
  }
}

// ========== SCREENS ============
void showWelcomeScreen() {
  display.clearDisplay();
  display.setTextSize(2);
  display.setTextColor(SSD1306_WHITE);
  display.setCursor(0, 15);
  display.println("WELCOME TO");
  display.setCursor(0, 35);
  display.println("THE GAME");
  display.display();
  delay(2000);

  display.clearDisplay();
  display.setTextSize(1);
  display.setCursor(25, 15);
  display.println("made by:");
  display.setCursor(0, 35);
  display.println("ARKADIP MAHAPATRA");
  display.display();
  delay(2000);
  welcomeDone = true;
}

void showDifficultyScreen(bool selectedHard, int countdown) {
  display.clearDisplay();
  display.setTextSize(1);
  display.setCursor(5, 15);
  display.print("Select Difficulty:");
  display.setCursor(22, 35);
  display.print(selectedHard ? "  Easy   >Hard<" : ">Easy<   Hard");

  display.setCursor(20, 50);
  display.print("Starting in: ");
  display.print(countdown);
  display.display();
}

// ========== SETUP ============
void setup() {
  pinMode(BUTTON_PIN, INPUT_PULLUP);
  display.begin(SSD1306_SWITCHCAPVCC, 0x3C);
  display.clearDisplay();
  display.display();
}

// ========== MAIN LOOP ============
void loop() {
  static bool waitingToStart = true;
  static bool difficultySelected = false;
  static unsigned long countdownStartTime = 0;
  static int countdown = 3;
  static unsigned long lastCountdownUpdate = 0;

  // Show welcome screen once
  if (!welcomeDone) {
    showWelcomeScreen();
    welcomeDone = true;
    return;
  }

  // Start screen: wait for first press
  if (!gameStarted && waitingToStart) {
    display.clearDisplay();
    display.setTextSize(1);
    display.setCursor(0, 30);
    display.print("Press button to start");
    display.display();

    if (digitalRead(BUTTON_PIN) == LOW) {
      delay(200); // debounce
      waitingToStart = false;
      countdownStartTime = millis();
      lastCountdownUpdate = millis();
      countdown = 3;
      inCountdown = true;
    }
    return;
  }

  // Difficulty selection with countdown
  if (!gameStarted && inCountdown) {
    // Handle button press to toggle difficulty
    if (digitalRead(BUTTON_PIN) == LOW) {
      delay(200); // debounce
      hardMode = !hardMode;
      countdown = 3;
      countdownStartTime = millis();
      lastCountdownUpdate = millis();
    }

    // Update countdown every second
    if (millis() - lastCountdownUpdate >= 1000) {
      countdown--;
      lastCountdownUpdate = millis();
    }

    showDifficultyScreen(hardMode, countdown);

    if (countdown <= 0) {
      inCountdown = false;
      gameStarted = true;
      gameSpeed = hardMode ? 20 : 50; //gameSpeed = hardMode ? 30 : 50; // Adjust game speed "for hard mode"
    }
    return;
  }

  // ================= GAME LOOP =================
  if (digitalRead(BUTTON_PIN) == LOW && !isJumping) {
    isJumping = true;
    jumpCounter = 0;
    delay(100);
  }

  if (isJumping) {
    if (jumpCounter < 10) {
      playerY -= 2;
    } else if (jumpCounter < 20) {
      playerY += 2;
    } else {
      isJumping = false;
      playerY = groundLevel - 6;
    }
    jumpCounter++;
  }

  obstacleX -= 2;
  if (obstacleX < -10) {
    obstacleX = SCREEN_WIDTH;
    obstacleType = random(0, 3);
    score++;

    if (score % 10 == 0) {
      display.clearDisplay();
      display.setCursor(0, 20);
      display.setTextSize(1);
      if (score % 20 == 0)
        display.print("YOU REALLY HAVE QUICK REFLEXES!");
      else
        display.print("Great job!");
      display.display();
      delay(1500);
    }

    if (hardMode && gameSpeed > 10) gameSpeed -= 2; //if (hardMode && gameSpeed > 25) gameSpeed--; // Adjust game speed for hard mode
  }

  display.clearDisplay();
  display.setTextSize(1);
  display.setCursor(0, 0);
  display.print("Score: ");
  display.print(score);

  display.drawLine(0, groundLevel + 4, SCREEN_WIDTH, groundLevel + 4, SSD1306_WHITE);
  drawPlayer(playerX, playerY, isJumping);
  drawObstacle(obstacleX, obstacleType);

// Collision detection
if (obstacleX <= playerX + 4 && obstacleX + 6 >= playerX) {
  if (obstacleType == 2) {
    // Bird: check if player jumped into it
    if (playerY <= groundLevel - 16) {
      display.setCursor(30, 20);
      display.print("Game Over!");
      display.display();
      delay(2000);

      // Reset game state
      score = 0;
      obstacleX = SCREEN_WIDTH;
      gameStarted = false;
      waitingToStart = true;
      hardMode = false;
      inCountdown = false;
      return;
    }
  } else {
    // Ground obstacle collision
    if (playerY + 6 >= groundLevel - 6) {
      display.setCursor(30, 20);
      display.print("Game Over!");
      display.display();
      delay(2000);

      // Reset game state
      score = 0;
      obstacleX = SCREEN_WIDTH;
      gameStarted = false;
      waitingToStart = true;
      hardMode = false;
      inCountdown = false;
      return;
    }
  }
}


  display.display();
  delay(gameSpeed);
}
```

![Screenshot (329)](https://github.com/user-attachments/assets/c34d7ad1-2428-4b33-8127-fabe6551ddc6)

> For the best verson of code --> goes down of this page

To make **Hard** mode a bit more challenging, you can increase the initial speed (i.e., decrease the delay) and allow it to ramp up faster over time.

---

### 🔧 Here's what to tweak:

#### 1. **Set a faster starting speed for Hard mode**
In the line where `gameSpeed` is set after countdown ends:
```cpp
gameSpeed = hardMode ? 30 : 50;
```

➡️ Change `30` to something like `20`:
```cpp
gameSpeed = hardMode ? 20 : 50;
```

---

#### 2. **Make Hard mode get faster more aggressively**
Find this line inside your `obstacleX < -10` block:
```cpp
if (hardMode && gameSpeed > 25) gameSpeed--;
```

➡️ Make it more aggressive, like:
```cpp
if (hardMode && gameSpeed > 10) gameSpeed -= 2;
```

This will reduce delay by 2ms every 10 points, making it noticeably harder as score increases.

---

### Final Tweaked Lines:
```cpp
gameSpeed = hardMode ? 20 : 50;   // faster start for hard
...
if (hardMode && gameSpeed > 10) gameSpeed -= 2;  // faster acceleration
```

---

## Update for NANO

```ini
[env:nanoatmega328]
platform = atmelavr
board = nanoatmega328
framework = arduino
monitor_speed = 9600

board_build.mcu = atmega328p
board_build.f_cpu = 16000000L
upload_protocol = arduino
upload_speed = 57600  ; ← Old Bootloader uses 57600 baud
lib_deps =
  adafruit/Adafruit SSD1306@^2.5.7
  adafruit/Adafruit GFX Library@^1.11.9
```

## 🎯 Update with 🚗🌲👻

- add car 🚗
- add Ghost 👻
- add cactus 🌵
- update Trees 🌲
- increse the speed for "Heard" 🎯

<details>
  <summary style="opacity: 0.85;"><b>🎯 game code 0.7</b></summary><br>

> For the best verson of code --> goes down of this page

```cpp
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64
#define OLED_RESET -1
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, OLED_RESET);

#define BUTTON_PIN 2

int playerX = 5;
int playerY = 50;
bool isJumping = false;
int jumpCounter = 0;
int groundLevel = 56;

int obstacleX = SCREEN_WIDTH;
int obstacleType = 0;
int birdVariant = 0;

int score = 0;
bool gameStarted = false;
bool welcomeDone = false;
bool hardMode = false;
bool inCountdown = false;

int gameSpeed = 50;

// ========== PLAYER ============
void drawPlayer(int x, int y, bool jumping) {
  display.fillCircle(x + 2, y - 6, 2, SSD1306_WHITE); // Head
  display.drawLine(x + 2, y - 4, x + 2, y, SSD1306_WHITE); // Body
  display.drawLine(x + 2, y - 3, x, y - 2, SSD1306_WHITE); // Arms
  display.drawLine(x + 2, y - 3, x + 4, y - 2, SSD1306_WHITE);
  if (jumping) {
    display.drawLine(x + 2, y, x, y + 3, SSD1306_WHITE); // Jumping legs
    display.drawLine(x + 2, y, x + 4, y + 3, SSD1306_WHITE);
  } else {
    display.drawLine(x + 2, y, x + 1, y + 4, SSD1306_WHITE); // Standing legs
    display.drawLine(x + 2, y, x + 3, y + 4, SSD1306_WHITE);
  }
}

// ========== OBSTACLES ============
void drawObstacle(int x, int type) {
  switch (type) {
    case 0: // Car
      display.fillRect(x, groundLevel - 8, 16, 8, SSD1306_WHITE); // Body
      display.drawLine(x + 3, groundLevel - 8, x + 6, groundLevel - 12, SSD1306_WHITE); // Windshield
      display.drawLine(x + 6, groundLevel - 12, x + 10, groundLevel - 12, SSD1306_WHITE); // Roof
      display.drawLine(x + 10, groundLevel - 12, x + 13, groundLevel - 8, SSD1306_WHITE); // Back slope
      display.fillCircle(x + 4, groundLevel, 2, SSD1306_WHITE); // Left wheel
      display.fillCircle(x + 12, groundLevel, 2, SSD1306_WHITE); // Right wheel
      break;

    case 1: // Pine Tree
      display.fillRect(x + 3, groundLevel - 6, 2, 6, SSD1306_WHITE); 
      display.fillTriangle(x - 4, groundLevel - 6, x + 4, groundLevel - 14, x + 12, groundLevel - 6, SSD1306_WHITE);
      display.fillTriangle(x - 3, groundLevel - 10, x + 4, groundLevel - 18, x + 11, groundLevel - 10, SSD1306_WHITE);
      break;

    case 2: // Bird Variants
  switch (birdVariant) {
    case 0: // Small Bird
      display.fillCircle(x + 5, groundLevel - 20, 2, SSD1306_WHITE); // Body
      display.drawPixel(x + 3, groundLevel - 20, SSD1306_WHITE);     // Head
      display.drawPixel(x + 2, groundLevel - 20, SSD1306_WHITE);     // Beak
      display.drawLine(x + 5, groundLevel - 20, x + 7, groundLevel - 22, SSD1306_WHITE); // Wing top
      display.drawLine(x + 5, groundLevel - 20, x + 7, groundLevel - 18, SSD1306_WHITE); // Wing bottom
      display.drawPixel(x + 7, groundLevel - 20, SSD1306_WHITE);     // Tail
      break;


    case 1: // Wide-Wing Bird (soaring)
      display.drawLine(x + 3, groundLevel - 20, x + 7, groundLevel - 20, SSD1306_WHITE); // Wingspan
      display.drawPixel(x + 5, groundLevel - 21, SSD1306_WHITE); // Head/body
      display.drawPixel(x + 6, groundLevel - 21, SSD1306_WHITE); // Back
      break;

    case 2: // Perched-Looking Bird (longer body)
      display.fillRect(x + 4, groundLevel - 21, 3, 3, SSD1306_WHITE); // Body
      display.drawPixel(x + 3, groundLevel - 21, SSD1306_WHITE); // Head
      display.drawPixel(x + 2, groundLevel - 21, SSD1306_WHITE); // Beak
      display.drawPixel(x + 5, groundLevel - 24, SSD1306_WHITE); // Wing tip
      display.drawPixel(x + 7, groundLevel - 20, SSD1306_WHITE); // Tail feather
      break;
  }
  break;

    case 3: // Walking dog
      display.fillRect(x + 2, groundLevel - 8, 10, 5, SSD1306_WHITE);   // Body
      display.drawRect(x - 1, groundLevel - 10, 4, 4, SSD1306_WHITE);   // Head
      display.drawPixel(x, groundLevel - 11, SSD1306_WHITE);           // Ear
      display.drawLine(x + 10, groundLevel - 7, x + 12, groundLevel - 6, SSD1306_WHITE); // Tail
      display.drawLine(x + 3, groundLevel - 3, x + 3, groundLevel, SSD1306_WHITE); // Front leg
      display.drawLine(x + 5, groundLevel - 3, x + 5, groundLevel, SSD1306_WHITE); // Mid leg
      display.drawLine(x + 7, groundLevel - 3, x + 7, groundLevel, SSD1306_WHITE); // Mid leg
      display.drawLine(x + 9, groundLevel - 3, x + 9, groundLevel, SSD1306_WHITE); // Back leg
      break;

      case 4: // Cactus with head
      display.drawLine(x + 3, groundLevel - 14, x + 3, groundLevel, SSD1306_WHITE); // Main trunk
      display.drawLine(x + 3, groundLevel - 10, x, groundLevel - 10, SSD1306_WHITE); // Left arm
      display.drawLine(x, groundLevel - 10, x, groundLevel - 6, SSD1306_WHITE);     // Left arm vertical
      display.drawLine(x + 3, groundLevel - 12, x + 6, groundLevel - 12, SSD1306_WHITE); // Right arm
      display.drawLine(x + 6, groundLevel - 12, x + 6, groundLevel - 8, SSD1306_WHITE);  // Right arm vertical

      // 🌵 Head (a circle-like cactus top)
      display.drawPixel(x + 2, groundLevel - 15, SSD1306_WHITE);
      display.drawPixel(x + 3, groundLevel - 16, SSD1306_WHITE);
      display.drawPixel(x + 4, groundLevel - 15, SSD1306_WHITE);
      break;
  }
}

// ========== SCREENS ============
void showWelcomeScreen() {
  display.clearDisplay();
  display.setTextSize(2);
  display.setTextColor(SSD1306_WHITE);
  display.setCursor(0, 15);
  display.println("WELCOME TO");
  display.setCursor(0, 35);
  display.println("THE GAME");
  display.display();
  delay(2000);

  display.clearDisplay();
  display.setTextSize(1.5);
  display.setCursor(0, 15);
  display.println("made by: ");
  display.setCursor(0, 35);
  display.println("ARKADIP MAHAPATRA");
  display.display();
  delay(2000);
  welcomeDone = true;
}

void showDifficultyScreen(bool selectedHard, int countdown) {
  display.clearDisplay();
  display.setTextSize(1);
  display.setCursor(5, 15);
  display.print("Select Difficulty:");
  display.setCursor(22, 35);
  display.print(selectedHard ? "  Easy   >Hard<" : ">Easy<   Hard");

  display.setCursor(20, 50);
  display.print("Starting in: ");
  display.print(countdown);
  display.display();
}

// ========== SETUP ============
void setup() {
  pinMode(BUTTON_PIN, INPUT_PULLUP);
  display.begin(SSD1306_SWITCHCAPVCC, 0x3C);
  display.clearDisplay();
  display.display();
}

// ========== MAIN LOOP ============
void loop() {
  static bool waitingToStart = true;
  static bool difficultySelected = false;
  static unsigned long countdownStartTime = 0;
  static int countdown = 3;
  static unsigned long lastCountdownUpdate = 0;

  // Show welcome screen once
  if (!welcomeDone) {
    showWelcomeScreen();
    welcomeDone = true;
    return;
  }

  // Start screen: wait for first press
  if (!gameStarted && waitingToStart) {
    display.clearDisplay();
    display.setTextSize(1);
    display.setCursor(0, 30);
    display.print("Press button to start");
    display.display();

    if (digitalRead(BUTTON_PIN) == LOW) {
      delay(200); // debounce
      waitingToStart = false;
      countdownStartTime = millis();
      lastCountdownUpdate = millis();
      countdown = 3;
      inCountdown = true;
    }
    return;
  }

  // Difficulty selection with countdown
  if (!gameStarted && inCountdown) {
    // Handle button press to toggle difficulty
    if (digitalRead(BUTTON_PIN) == LOW) {
      delay(200); // debounce
      hardMode = !hardMode;
      countdown = 3;
      countdownStartTime = millis();
      lastCountdownUpdate = millis();
    }

    // Update countdown every second
    if (millis() - lastCountdownUpdate >= 1000) {
      countdown--;
      lastCountdownUpdate = millis();
    }

    showDifficultyScreen(hardMode, countdown);

    if (countdown <= 0) {
      inCountdown = false;
      gameStarted = true;
      gameSpeed = hardMode ? 10 : 50; //gameSpeed = hardMode ? 30 : 50; // Adjust game speed "for hard mode"
    }
    return;
  }

  // ================= GAME LOOP =================
  if (digitalRead(BUTTON_PIN) == LOW && !isJumping) {
    isJumping = true;
    jumpCounter = 0;
    delay(100);
  }

  if (isJumping) {
    if (jumpCounter < 10) {
      playerY -= 2;
    } else if (jumpCounter < 20) {
      playerY += 2;
    } else {
      isJumping = false;
      playerY = groundLevel - 6;
    }
    jumpCounter++;
  }

  obstacleX -= 2;
  if (obstacleX < -10) {
    obstacleX = SCREEN_WIDTH;
    obstacleType = random(0, 5); // Randomly select obstacle type
    int birdVariant = random(0, 3); // 0 = small, 1 = wide-wing, 2 = perched-looking
    score++;

    if (score % 10 == 0) {
      display.clearDisplay();
      display.setCursor(0, 20);
      display.setTextSize(1);
      if (score % 20 == 0)
        display.print("YOU REALLY HAVE QUICK REFLEXES!");
      else
        display.print("Great job!");
      display.display();
      delay(1500);
    }

    if (hardMode && gameSpeed > 5) gameSpeed -= 5; //if (hardMode && gameSpeed > 25) gameSpeed--; // Adjust game speed for hard mode
  }

  display.clearDisplay();
  display.setTextSize(1);
  display.setCursor(0, 0);
  display.print("Score: ");
  display.print(score);

  display.drawLine(0, groundLevel + 4, SCREEN_WIDTH, groundLevel + 4, SSD1306_WHITE);
  drawPlayer(playerX, playerY, isJumping);
  drawObstacle(obstacleX, obstacleType);

// Collision detection
if (obstacleX <= playerX + 4 && obstacleX + 6 >= playerX) {
  if (obstacleType == 2) {
    // Bird: check if player jumped into it
    if (playerY <= groundLevel - 16) {
      display.setCursor(30, 20);
      display.print("Game Over!");
      display.display();
      delay(2000);

      // Reset game state
      score = 0;
      obstacleX = SCREEN_WIDTH;
      gameStarted = false;
      waitingToStart = true;
      hardMode = false;
      inCountdown = false;
      return;
    }
  } else {
    // Ground obstacle collision
    if (playerY + 6 >= groundLevel - 6) {
      display.setCursor(30, 20);
      display.print("Game Over!");
      display.display();
      delay(2000);

      // Reset game state
      score = 0;
      obstacleX = SCREEN_WIDTH;
      gameStarted = false;
      waitingToStart = true;
      hardMode = false;
      inCountdown = false;
      return;
    }
  }
}


  display.display();
  delay(gameSpeed);
}
```
</details>

## This is the current Best version 0.8

https://github.com/user-attachments/assets/48ad755c-c7cd-465b-91a9-31d1da794d67

- add Moon 🌙
- add stars ✨

```cpp
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64
#define OLED_RESET -1
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, OLED_RESET);

#define BUTTON_PIN 2

int playerX = 5;
int playerY = 50;
bool isJumping = false;
int jumpCounter = 0;
int groundLevel = 56;

int obstacleX = SCREEN_WIDTH;
int obstacleType = 0;
int birdVariant = 0;

int score = 0;
bool gameStarted = false;
bool welcomeDone = false;
bool hardMode = false;
bool inCountdown = false;

int gameSpeed = 50;

// ========== STARS AND CLOUDS ============
#define MAX_STARS 10
#define MAX_CLOUDS 2

int starX[MAX_STARS];
int starY[MAX_STARS];

int cloudX[MAX_CLOUDS];
int cloudY[MAX_CLOUDS];
bool cloudVisible[MAX_CLOUDS];

// ========== WATERMARK, at POINT 8 ============
bool watermarkShown = false;
int watermarkX = SCREEN_WIDTH;
bool watermarkStarted = false;

// ========== PLAYER ============
void drawPlayer(int x, int y, bool jumping) {
  display.fillCircle(x + 2, y - 6, 2, SSD1306_WHITE); // Head
  display.drawLine(x + 2, y - 4, x + 2, y, SSD1306_WHITE); // Body
  display.drawLine(x + 2, y - 3, x, y - 2, SSD1306_WHITE); // Arms
  display.drawLine(x + 2, y - 3, x + 4, y - 2, SSD1306_WHITE);
  if (jumping) {
    display.drawLine(x + 2, y, x, y + 3, SSD1306_WHITE); // Jumping legs
    display.drawLine(x + 2, y, x + 4, y + 3, SSD1306_WHITE);
  } else {
    display.drawLine(x + 2, y, x + 1, y + 4, SSD1306_WHITE); // Standing legs
    display.drawLine(x + 2, y, x + 3, y + 4, SSD1306_WHITE);
  }
}

// ========== OBSTACLES ============
void drawObstacle(int x, int type) {
  switch (type) {
    case 0: // Car
      display.fillRect(x, groundLevel - 8, 16, 8, SSD1306_WHITE); // Body
      display.drawLine(x + 3, groundLevel - 8, x + 6, groundLevel - 12, SSD1306_WHITE); // Windshield
      display.drawLine(x + 6, groundLevel - 12, x + 10, groundLevel - 12, SSD1306_WHITE); // Roof
      display.drawLine(x + 10, groundLevel - 12, x + 13, groundLevel - 8, SSD1306_WHITE); // Back slope
      display.fillCircle(x + 4, groundLevel, 2, SSD1306_WHITE); // Left wheel
      display.fillCircle(x + 12, groundLevel, 2, SSD1306_WHITE); // Right wheel
      break;

    case 1: // Pine Tree
      display.fillRect(x + 3, groundLevel - 6, 2, 6, SSD1306_WHITE); 
      display.fillTriangle(x - 4, groundLevel - 6, x + 4, groundLevel - 14, x + 12, groundLevel - 6, SSD1306_WHITE);
      display.fillTriangle(x - 3, groundLevel - 10, x + 4, groundLevel - 18, x + 11, groundLevel - 10, SSD1306_WHITE);
      break;

    case 2: // Bird Variants
  switch (birdVariant) {
    case 0: // Small Bird
      display.fillCircle(x + 5, groundLevel - 20, 2, SSD1306_WHITE); // Body
      display.drawPixel(x + 3, groundLevel - 20, SSD1306_WHITE);     // Head
      display.drawPixel(x + 2, groundLevel - 20, SSD1306_WHITE);     // Beak
      display.drawLine(x + 5, groundLevel - 20, x + 7, groundLevel - 22, SSD1306_WHITE); // Wing top
      display.drawLine(x + 5, groundLevel - 20, x + 7, groundLevel - 18, SSD1306_WHITE); // Wing bottom
      display.drawPixel(x + 7, groundLevel - 20, SSD1306_WHITE);     // Tail
      break;


    case 1: // Wide-Wing Bird (soaring)
      display.drawLine(x + 3, groundLevel - 20, x + 7, groundLevel - 20, SSD1306_WHITE); // Wingspan
      display.drawPixel(x + 5, groundLevel - 21, SSD1306_WHITE); // Head/body
      display.drawPixel(x + 6, groundLevel - 21, SSD1306_WHITE); // Back
      break;

    case 2: // Perched-Looking Bird (longer body)
      display.fillRect(x + 4, groundLevel - 21, 3, 3, SSD1306_WHITE); // Body
      display.drawPixel(x + 3, groundLevel - 21, SSD1306_WHITE); // Head
      display.drawPixel(x + 2, groundLevel - 21, SSD1306_WHITE); // Beak
      display.drawPixel(x + 5, groundLevel - 24, SSD1306_WHITE); // Wing tip
      display.drawPixel(x + 7, groundLevel - 20, SSD1306_WHITE); // Tail feather
      break;
  }
  break;

    case 3: // Walking dog
      display.fillRect(x + 2, groundLevel - 8, 10, 5, SSD1306_WHITE);   // Body
      display.drawRect(x - 1, groundLevel - 10, 4, 4, SSD1306_WHITE);   // Head
      display.drawPixel(x, groundLevel - 11, SSD1306_WHITE);           // Ear
      display.drawLine(x + 10, groundLevel - 7, x + 12, groundLevel - 6, SSD1306_WHITE); // Tail
      display.drawLine(x + 3, groundLevel - 3, x + 3, groundLevel, SSD1306_WHITE); // Front leg
      display.drawLine(x + 5, groundLevel - 3, x + 5, groundLevel, SSD1306_WHITE); // Mid leg
      display.drawLine(x + 7, groundLevel - 3, x + 7, groundLevel, SSD1306_WHITE); // Mid leg
      display.drawLine(x + 9, groundLevel - 3, x + 9, groundLevel, SSD1306_WHITE); // Back leg
      break;

      case 4: // Cactus with head
      display.drawLine(x + 3, groundLevel - 14, x + 3, groundLevel, SSD1306_WHITE); // Main trunk
      display.drawLine(x + 3, groundLevel - 10, x, groundLevel - 10, SSD1306_WHITE); // Left arm
      display.drawLine(x, groundLevel - 10, x, groundLevel - 6, SSD1306_WHITE);     // Left arm vertical
      display.drawLine(x + 3, groundLevel - 12, x + 6, groundLevel - 12, SSD1306_WHITE); // Right arm
      display.drawLine(x + 6, groundLevel - 12, x + 6, groundLevel - 8, SSD1306_WHITE);  // Right arm vertical

      // 🌵 Head (a circle-like cactus top)
      display.drawPixel(x + 2, groundLevel - 15, SSD1306_WHITE);
      display.drawPixel(x + 3, groundLevel - 16, SSD1306_WHITE);
      display.drawPixel(x + 4, groundLevel - 15, SSD1306_WHITE);
      break;
  }
}

// ========== SCREENS ============
void showWelcomeScreen() {
  display.clearDisplay();
  display.setTextSize(2);
  display.setTextColor(SSD1306_WHITE);
  display.setCursor(0, 15);
  display.println("WELCOME TO");
  display.setCursor(0, 35);
  display.println("THE GAME");
  display.display();
  delay(2000);

  display.clearDisplay();
  display.setTextSize(1.5);
  display.setCursor(0, 15);
  display.println("made by: ");
  display.setCursor(0, 35);
  display.println("ARKADIP MAHAPATRA");
  display.display();
  delay(2000);
  welcomeDone = true;
}

void showDifficultyScreen(bool selectedHard, int countdown) {
  display.clearDisplay();
  display.setTextSize(1);
  display.setCursor(5, 15);
  display.print("Select Difficulty:");
  display.setCursor(22, 35);
  display.print(selectedHard ? "  Easy   >Hard<" : ">Easy<   Hard");

  display.setCursor(20, 50);
  display.print("Starting in: ");
  display.print(countdown);
  display.display();
}

// ========== SETUP ============
void setup() {
  pinMode(BUTTON_PIN, INPUT_PULLUP);
  display.begin(SSD1306_SWITCHCAPVCC, 0x3C);
  display.clearDisplay();
  display.display();

  for (int i = 0; i < MAX_STARS; i++) { 
  starX[i] = random(SCREEN_WIDTH, SCREEN_WIDTH + 100);
  starY[i] = random(0, groundLevel - 20);
  }

  for (int i = 0; i < MAX_CLOUDS; i++) {
    cloudX[i] = SCREEN_WIDTH + random(50, 150);
    cloudY[i] = random(5, groundLevel - 30);
    cloudVisible[i] = false;
  }
}

// ========== STARS ============
void drawStars() {
  for (int i = 0; i < MAX_STARS; i++) {
    display.drawPixel(starX[i], starY[i], SSD1306_WHITE);
    starX[i]--;

    if (starX[i] < 0) {
      starX[i] = SCREEN_WIDTH + random(20, 100);
      starY[i] = random(0, groundLevel - 20);
    }
  }
}

// ========== CLOUDS ============
void drawClouds() {
  for (int i = 0; i < 3; i++) {
    int x = cloudX[i];
    int y = cloudY[i];

    display.fillCircle(x + 8, y, 4, SSD1306_WHITE);
    display.fillCircle(x + 4, y + 2, 4, SSD1306_WHITE);
    display.fillCircle(x + 12, y + 2, 4, SSD1306_WHITE);
  }
}

// ========== MOON ============
void drawMoon() {
  int x = SCREEN_WIDTH - 12;
  int y = 10;
  display.fillCircle(x, y, 6, SSD1306_WHITE); // Full moon
  display.fillCircle(x - 2, y - 1, 5, SSD1306_BLACK); // Shadow for crescent effect
}

// ========== WATERMARK, at POINT 8 ============
void drawWatermark() {
  display.setTextSize(1);
  display.setTextColor(SSD1306_WHITE);
  display.setCursor(watermarkX, SCREEN_HEIGHT - 50); // Bottom of screen
  display.print("   ARKADIP MAHAPATRA");
}


// ========== MAIN LOOP ============
void loop() {
  static bool waitingToStart = true;
  static bool difficultySelected = false;
  static unsigned long countdownStartTime = 0;
  static int countdown = 3;
  static unsigned long lastCountdownUpdate = 0;

  // Show welcome screen once
  if (!welcomeDone) {
    showWelcomeScreen();
    welcomeDone = true;
    return;
  }

  // Start screen: wait for first press
  if (!gameStarted && waitingToStart) {
    display.clearDisplay();
    display.setTextSize(1);
    display.setCursor(0, 30);
    display.print("Press button to start");
    display.display();

    if (digitalRead(BUTTON_PIN) == LOW) {
      delay(200); // debounce
      waitingToStart = false;
      countdownStartTime = millis();
      lastCountdownUpdate = millis();
      countdown = 3;
      inCountdown = true;
    }
    return;
  }

  // Difficulty selection with countdown
  if (!gameStarted && inCountdown) {
    // Handle button press to toggle difficulty
    if (digitalRead(BUTTON_PIN) == LOW) {
      delay(200); // debounce
      hardMode = !hardMode;
      countdown = 3;
      countdownStartTime = millis();
      lastCountdownUpdate = millis();
    }

    // Update countdown every second
    if (millis() - lastCountdownUpdate >= 1000) {
      countdown--;
      lastCountdownUpdate = millis();
    }

    showDifficultyScreen(hardMode, countdown);

    if (countdown <= 0) {
      inCountdown = false;
      gameStarted = true;
      gameSpeed = hardMode ? 10 : 50; //gameSpeed = hardMode ? 30 : 50; // Adjust game speed "for hard mode"
    }
    return;
  }

  // ================= GAME LOOP =================
  if (digitalRead(BUTTON_PIN) == LOW && !isJumping) {
    isJumping = true;
    jumpCounter = 0;
    delay(100);
  }

  if (isJumping) {
    if (jumpCounter < 10) {
      playerY -= 2;
    } else if (jumpCounter < 20) {
      playerY += 2;
    } else {
      isJumping = false;
      playerY = groundLevel - 6;
    }
    jumpCounter++;
  }

  obstacleX -= 2;
  if (obstacleX < -10) {
    obstacleX = SCREEN_WIDTH;
    obstacleType = random(0, 5); // Randomly select obstacle type
    int birdVariant = random(0, 3); // 0 = small, 1 = wide-wing, 2 = perched-looking
    score++;

    if (score % 10 == 0) {
      display.clearDisplay();
      display.setCursor(0, 20);
      display.setTextSize(1);
      if (score % 20 == 0)
        display.print("YOU REALLY HAVE QUICK REFLEXES!");
      else
        display.print("Great job!");
      display.display();
      delay(1500);
    }

    if (hardMode && gameSpeed > 5) gameSpeed -= 5; //if (hardMode && gameSpeed > 25) gameSpeed--; // Adjust game speed for hard mode
  }

  display.clearDisplay();
  display.setTextSize(1);
  display.setCursor(0, 0);
  display.print("Score: ");
  display.print(score);

  display.drawLine(0, groundLevel + 4, SCREEN_WIDTH, groundLevel + 4, SSD1306_WHITE);

  if (score >= 2) {
  drawMoon(); // 🌙 Add this line
  }

// ✅ NEW: Sky elements
if (score >= 5) {
  drawStars();
  drawClouds();
}

// Show watermark one time when score == 8
// Start watermark once when score hits 8
if (score >= 8 && !watermarkStarted && !watermarkShown) {
  watermarkStarted = true;
  watermarkX = SCREEN_WIDTH;
}

// Animate watermark if started and not done
if (watermarkStarted && !watermarkShown) {
  drawWatermark();
  watermarkX -= 2;

  if (watermarkX + 110 < 0) { // Adjust if text is longer or shorter
    watermarkShown = true;
    watermarkStarted = false;
  }
}


  drawPlayer(playerX, playerY, isJumping);
  drawObstacle(obstacleX, obstacleType);

// Collision detection
if (obstacleX <= playerX + 4 && obstacleX + 6 >= playerX) {
  if (obstacleType == 2) {
    // Bird: check if player jumped into it
    if (playerY <= groundLevel - 16) {
      display.setCursor(30, 20);
      display.print("Game Over!");
      display.display();
      delay(2000);

      // Reset game state
      score = 0;
      obstacleX = SCREEN_WIDTH;
      gameStarted = false;
      waitingToStart = true;
      hardMode = false;
      inCountdown = false;
      return;
    }
  } else {
    // Ground obstacle collision
    if (playerY + 6 >= groundLevel - 6) {
      display.setCursor(30, 20);
      display.print("Game Over!");
      display.display();
      delay(2000);

      // Reset game state
      score = 0;
      obstacleX = SCREEN_WIDTH;
      gameStarted = false;
      waitingToStart = true;
      hardMode = false;
      inCountdown = false;
      return;
    }
  }
}


  display.display();
  delay(gameSpeed);
}
```

</br>

</br>
</br>

<div style="display: flex; align-items: center; gap: 10px;" align="center">
  
# ⭐ Next IOT Game Project ⭐
</div>

</br>
</br>

---

[<img src="public/ARDUINO & Display Games.jpg">](https://github.com/Arkadip2007/LCD-with-ARDUINO-Endless-Runner-game)

> click to show the upper Project code.
