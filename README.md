# Simple-Game
#include <iostream>
#include <conio.h> // For _kbhit() and _getch()

class MonkeyGame {
public:
    MonkeyGame() : monkeyX(10), score(0), gameover(false) {}

    void start() {
        while (!gameover) {
            updateGame();
            displayGame();
        }
        std::cout << "Game Over! Your score: " << score << std::endl;
    }

private:
    int monkeyX; // Monkey's position
    int score;
    bool gameover;

    // Update the game state
    void updateGame() {
        if (_kbhit()) {
            char key = _getch();
            if (key == 'a' && monkeyX > 0) {
                monkeyX--;
            }
            else if (key == 'd' && monkeyX < 20) {
                monkeyX++;
            }
        }

        // Simulate object falling
        if (rand() % 15 == 0) {
            int objectX = rand() % 21; // Random position for object
            if (objectX == monkeyX) {
                gameover = true; // Object hits the monkey
            }
            else {
                score++;
            }
        }
    }

    // Display the game
    void displayGame() {
        system("cls"); // Clear the console
        for (int i = 0; i < 21; i++) {
            if (i == monkeyX) {
                std::cout << "M";
            }
            else {
                std::cout << " ";
            }
        }
        std::cout << std::endl;

        std::cout << "Score: " << score << std::endl;
    }
};

int main() {
    MonkeyGame game;
    game.start();
    return 0;
}
