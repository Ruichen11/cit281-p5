# cit281-p5 
![wallpaperCIT](https://user-images.githubusercontent.com/84296093/120629913-2a6ed600-c41b-11eb-99f2-8a5f56329ffb.jpg)
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

- The file is a code module with a MonsterGame class.
- The class constructor accepts an object with initial game values, and creates the initial list of monsters.
- The listMonsters() method will list each of the monsters to the console (see screen capture below). You will write this method, and must use forEach() Array method, and template literal.
- The createMonsters() method creates an array of monster objects using the Monster class from the p5-monster.js code module.
- The playGame() method plays the game, allowing each monster to randomly drain life after a specific interval of time has elapsed, and repeats until all monsters have died. This method uses concepts that will be discussed later, including Promises and async/await.

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

- This file contains the Monster class.
- Tip: Look at the p5-monster-game.js file to see how to export a class from a code module, and the p5.js file to see how to import a class from a code module.
- You will need to look at the use of the Monster class in the p5-monster-game.js file to see how you will need to code your class, including the class constructor and methods, and will include the following class methods:

constructor():
 - Accepts an object parameter, deconstructs the object into the following variables and matching this class properties, with the specified default values 
 - monsterName, defaults to "Unknown".
- minimumLife, defaults to 0, represents the minimum life value for the monster to be considered alive.
- currentLife, defaults to 100, represents the current life value that when decreases below minimumLife indicates the monster has died.
- The constructor MUST accept an object, and use parameter object deconstruction to extract the parameter variables, and default object values, as specified
- Sets the this class property isAlive to either true or false, depending on if the monster's currentLife >= minimumLife.
- The setting of isAlive MUST use a single line of code to set the initial Boolean value based on currentLife >= minimumLife.


updateLife():
- Accepts an integer, lifeChangeAmount, that represents the amount of life change, either positive or negative. If the monster currentLife amount drops below 0, set the - --currentLife to 0. If currentLife < minimumLife, set isAlive to true.
- Your code MUST not use  any if() statements, using one line statement constructions, such as the ternary operator ( ? : ).


randomLifeDrain():
- Accepts two integers, minimumLifeDrain and maximumLifeDrain, where mimimumLifeDrain must be < maximumLifeDrain
- Calls updateLife() class method to deduct a randomly generated integer between minimumLifeDrain and maximumLifeDrain using getRandomInteger() method used in previous labs and - projects. Don't forget to add one to maximumLifeDrain before calling getRandomInteger().
- Your code must use a template literal for any console.log() statements.
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
![Monster game](https://user-images.githubusercontent.com/84296093/120629944-33f83e00-c41b-11eb-9523-a7dc953e8b36.JPG)

