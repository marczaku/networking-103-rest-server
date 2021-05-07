# Part 4: MMO-RPG (Mass Multiplayer REST Playing Game) (70 Points)

All of these Tasks are completely optional but very recommended, as the actual game will be based on these exercises.

---

## Goal
To implement your own Game Client & REST-Server using ASP .NET Core Webserver-Technology combined with MongoDB as a database and either a Console or Unity Application as a Client.\
I do not care about how exactly the player interacts with the game / backend. But it is important, that:
- All data is stored in MongoDB
- All operations are available through a REST-API implemented in ASP .NET Core
  - The Rest Endpoints are following REST Standards (CRUD, HTTP-Methods, Query-Syntax, ...)
- Every Feature is accessible through a Game Client.
  - Suggestion: Build a Console Client (similar to GitHub Browser) that asks you what you want to do next. 1: See Quests 2: See Items 3: See Leaderboard etc.
  - Alternatively: Build a Unity-Client, where you visualize everything with UI Elements. Warning: This probably takes much more time!


---

## Feature-List

### Players
- Players can create and delete their players.
- They can find other players by their names.
- They can access their player through their id.
- They should not be able to see other players' ids.

### Quests
- Players can see and do quests to receive rewards.
- Players get a new random quest every minute.
- They can have up to 5 different quests at a time.
- Any new Quest then removes / invalidates the previous Quest.
- A quest has a Level Requirement and a Reward.
- A player can only do a quest, if he fulfils the Level Requirement.
- It grants Experience to the player.
- It grants the Reward to the player.
- A Reward can be 1-X Gold, or a Random Item.

### Level
- Players start with 0 Levels and 0 Experience.
- Every level requires N*100 Experience to level-up.
  - e.g: Level 0 -> Level 1 (100XP)  Level 1 -> Level 2 (200XP)
- Experience is always capped at the Level's Requirement.
- In order to level up, the player needs to purchase a Level-Up. It costs N*100 Gold.
  - e.g: Level 0 -> Level 1 (100 Gold)  Level 1 -> Level 2 (200 Gold)

### Gold
- Gold can be a Reward through a Quest.
- It can be spent for Level-Ups.
- Through Level-Ups, the Player is able to fulfil Quests with higher Level Requirements.

### Item
- Items can be Quest Rewards.
- They have Rarity, Type, Price, Level Requirement, Level Bonus.
  - Rarity: Common, Uncommon, Rare, Epic
  - Type: Weapon, Armor, Helmet
- An Item can be Sold to receive its Price in Gold.
- It can be Equipped, if the Player fulfils the Level Requirement.
- While Equipped, the Item's Level Bonus is Added to the Player's Level.
- The player can only have one Item of each Kind Equipped at the same Time.
- If the player sells an equipped item, it is not Equipped anymore.
- An Item's Level Bonus also helps with Equipping Items with high Level Requirements.
  - e.g: A Level 2 Player an Equip a Helmet with 1 Level Bonus.
  - which enables him to equip a Sword with a Level 3 Requirement.

### Leaderboard
- Players can see the top 10 players by level.
- And by Gold.

### Statistics
- Players can see the total count of players.
- And the total amount of Gold owned by all players.
- And the total amount of Items owned by all players.
- And the total amount of Levels of all players.
- Players can inspect other players.
  - They can only see their equipped items.
  - And they can see the Level described as:
    - Difference in Level smaller than 3 ? "EQUAL"
    - Player 3 or more levels higher? "STRONGER"
    - Player 3 or more levels lower? "WEAKER"
  - And yes, if players are in the Top 10 Leaderboard, their information is a little more public :-)