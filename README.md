# cit281-p5 
![image](https://github.com/Ruichen11/cit281-p5/blob/9716a778aeed06a0b957eb666085f4d7fda4e4ec/wallpaperCIT.jpg)
Purpose of this Project:
- Creating a Monster Game program using classes. This game will create monsters with initial life values, and minimum life values for the monster to be alive. Once the game is running, each monster's life will be drained by a random amount, and the game will continue until all of the monsters have died. The game will include a delay before each life drain.

By completing this project, you will:
- gain experience working with classes, including class constructors and method 
- gain experience interpreting existing code to design a new class
- gain experience working with the concept of a gaming loop. 
- Gain more experience working with objects and arrays
- Gain more experience working with Array map() and forEach() methods

## Project Codes:
p5.js
```
const MonsterGame = require("./p5-monster-game.js");

// Game monsters setup information
const monsterList = [
  {
    monsterName: "King Kong",
    minimumLife: 10,
    currentLife: 150,
  },
  {
    monsterName: "Godzilla",
    minimumLife: 10,
    currentLife: 200,
  },
];
// Game configuration information
const minimumLifeDrain = 10;
const maximumLifeDrain = 50;
const gameDelayInMilliseconds = 5000; // 5 second game delay

// Create Monster Game instance
const monsterGame = new MonsterGame(
  {
    monsterList, gameDelayInMilliseconds, minimumLifeDrain, maximumLifeDrain
  }
);

// List monsters
monsterGame.listMonsters();

// Start game
monsterGame.playGame();
```

p5-monster-game.js 
```
// Required code modules
const Monster = require("./p5-monster.js");
// const Food = require("./p5-food.js");

/*** Game Class ***/
/* Constructor expects an object */
// IMPORTANT: When declaring a class within a module,
// the class is the ONLY export!
module.exports = class MonsterGame {
  constructor({
    monsterList = [],
    gameDelayInMilliseconds = 5000,
    minimumLifeDrain = 1,
    maximumLifeDrain = 30,
  }) {
    console.log("Initializing monsters...");
    this.gameDelayInMilliseconds = gameDelayInMilliseconds;
    this.minimumLifeDrain = minimumLifeDrain;
    this.maximumLifeDrain = maximumLifeDrain;
    this.createMonsters(monsterList);
    console.log("Monsters initialized, ready to play!");
  }

  // List monsters
  listMonsters = () =>
    this.monsters.forEach((monster) => 
      console.log(
        `Monster: ${monster.monsterName}, ` +
        `Minimum Life: ${monster.minimumLife}, ` +
        `Current Life: ${monster.currentLife}, ` +
        `Status: ${monster.isAlive ? 'Alive' : 'Dead'}`
      )
    );

  // Create monsters from monster information
  createMonsters = (monsterList = []) => {
    this.monsters = monsterList.map((monster) => new Monster(monster));
  };

  // Update monster life value
  /*
  updateLife = (lifeChange = 0) =>
    this.monsters.forEach((monster) => monster.updateLife(lifeChange));
  */

  // Main game playing method, will exit when all monsters have died
  async playGame(GameDelay) {
    console.log("Starting game...");
    let monstersAreAlive = true;
    do {
      // Sleep game
      console.log(
        `Sleeping for ${this.gameDelayInMilliseconds} milliseconds...`
      );
      await sleep(this.gameDelayInMilliseconds);

      // Call each monster's random life drain method
      this.monsters.forEach((monster) => {
          if (monster.isAlive) {
            monster.randomLifeDrain(this.minimumLifeDrain, this.maximumLifeDrain)
          }
        }
      );

      // List monsters
      this.listMonsters();

      // Check if any monsters are alive and set Boolean
      monstersAreAlive =
        this.monsters.filter((monster) => monster.isAlive).length > 0;

      // See if any monsters are still alive
      if (!monstersAreAlive) {
        console.log('All monsters have died, game over!');
      }
    } while (monstersAreAlive);
  }
};

// Game loop

/*** Utility Functions ***/
// https://www.sitepoint.com/delay-sleep-pause-wait/
function sleep(ms) {
  return new Promise((resolve) => setTimeout(resolve, ms));
}
```
p5-monster.js
```
module.exports = class Monster {
  constructor({ monsterName = "Unknown", minimumLife = 0, currentLife = 100 }) {
    this.monsterName = monsterName;
    this.minimumLife = minimumLife;
    this.currentLife = currentLife;
    this.isAlive = currentLife >= minimumLife;
  }
  updateLife = (lifeChangeAmount) => {
    this.currentLife += lifeChangeAmount;
    this.currentLife = this.currentLife < 0 ? 0 : this.currentLife;
    this.isAlive = this.currentLife >= this.minimumLife;
  };
  randomLifeDrain = (minimumLifeDrain, maximumLifeDrain) => {
    if (minimumLifeDrain < maximumLifeDrain) {
      const lifeDrain = getRandomInteger(minimumLifeDrain, maximumLifeDrain + 1);
      console.log(`${this.monsterName} random power drain of ${lifeDrain}`);
      this.updateLife(-lifeDrain);
    }
  };
};

/*** Utility Functions ***/
// Returns a random number between min (inclusive) and max (exclusive)
function getRandomInteger(min, max) {
  return Math.floor(Math.random() * (max - min) + min);
}
```
![Image](https://github.com/Ruichen11/cit281-p5/blob/be56a96c0917f8c3a7659da8ba124854193c6be5/Monster%20game.JPG)

