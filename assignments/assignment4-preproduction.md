# Part 4 Pre-Production: MMO-RPG (Mass Multiplayer REST Playing Game)

All of these Tasks are completely optional and not graded, but very recommended, as the actual game will be based on these exercises.

---

## Goal
To implement your own REST-Server using ASP .NET Core Webserver-Technology combined with MongoDB as a database. Learn to CRUD documents.

---

## Preparing a Project
- Create a folder named `MMORPG`
- Open the Terminal in that Folder
- Now, use the command `dotnet new webapi`
- Add a `.gitignore` in your `MMORPG`-Folder that ignores anything you don't want to commit.
- For C# Console Projects, that's at least the `/bin/` and `/obj/`-Folders.
- Go to the newly created `Startup.cs` class. Remove the line that says `app.UseHttpsRedirection();`. This will make testing easier for now.
- Afterwards, you may safely go ahead and create a new commit `adds mmorpg project`

---

## 1. Create Model classes

Create the following classes in separate files inside the project folder:

`Player` class is used to save the player's save data.

```cs
public class Player
{
    public Guid Id { get; set; }
    public string Name { get; set; }
    public int Score { get; set; }
    public int Level { get; set; }
    public bool IsDeleted { get; set; }
    public DateTime CreationTime { get; set; }
}
```

`NewPlayer` class is used to define object that contains the properties that are defined by the client when creating new player. `Id` and `CreationDate` should be set by the server when the player is created.

```cs
public class NewPlayer
{
    public string Name { get; set; }
}
```

`ModifiedPlayer` class contains the properties that can be modified on a player.

```cs
public class ModifiedPlayer
{
    public int Score { get; set; }
}
```

---


## 2. Create a FileRepository class and IRepository interface

The responsibility of the `Repository` is to handle accessing and persisting player objects.

Create the following interface in a new file:

```cs
public interface IRepository
{
    Task<Player> Get(Guid id);
    Task<Player[]> GetAll();
    Task<Player> Create(Player player);
    Task<Player> Modify(Guid id, ModifiedPlayer player);
    Task<Player> Delete(Guid id);
}
```

Create a class called `FileRepository` which implements the interface. The purpose of the class is to persist and manipulate the `Player` objects in a text file. One possible solution is to serialize the players as JSON to the text file. The text file name should be `game-dev.txt`. You can use, for example, `File.ReadAllTextAsync` and `File.WriteAllTextAsync` methods for the implementation.

Usually, you'd have to worry about asynchronous access to that file (what happens, if two players connect to your web server at the same time?).  But we'll skip on this for now.

---

## 3. Create a PlayersController class

The first responsibility of the controller is to define the routes for the API. Define the routes using attributes. There is `[HttpPost]`, `[HttpPut]` and a few more.

The second responsibility is to handle the business logic. Business logic is a term for the core of your application. Everything that creates transactions that change your data / model. This can include things such as generating IDs when creating a player, and deciding which properties to change when modifying a player.

Create a class called `PlayersController`. Add and implement the following methods:

`PlayersController` should get `IRepository` through its constructor. We will learn later, how we can provide this information through Dependency Injection (the DI-Container)

```C#
public Task<Player> Get(Guid id);
public Task<Player[]> GetAll();
public Task<Player> Create(NewPlayer player);
public Task<Player> Modify(Guid id, ModifiedPlayer player);
public Task<Player> Delete(Guid id);
```

---

## 4. Register IRepository to DI-container

Register `FileRepository` to the DI-container in `Startup.ConfigureServices()`-Method.

Registering the `FileRepository` as `IRepository` into the dependency injection container enables changing the implementation later on when we start using `MongoDB` as the database.

```cs
// This Method is part of your Startup.cs:

        public void ConfigureServices(IServiceCollection services)
        {
            services.AddControllers();

            // Register IRepository implementation to the DI-container
            services.AddSingleton<IRepository, FileRepository>();
        }
```

This will make ASP.Net pass the `FileRepository` to the constructor of our `PlayersController`.

---

## 5. Test

Use either [PostMan](https://learning.postman.com/docs/sending-requests/requests/) or your own .NET-Code to test that the requests to all routes are processed successfully.\
How about adding a Console-Project to the solution for testing some Client-Code using `HttpClient`?

---

## 6. Bonus: Automated Tests

Can you use wither [Automated Tests in PostMan](https://learning.postman.com/docs/writing-scripts/test-scripts/) or your own Unit-Test-Project to validate that all end points work as expected?

---

## 7. Implement CRUD routes and data classes for `Item`

The idea is that items are always owned by players (there is no item not owned by a player).\
Define the Endpoints for Creating, Reading, Updating and Deleting an Item.

- Extend the `IRepository`-Interface and `FileRepository`-Class.
- Add an `Item`-Class and all other classes needed for the operations.
  - Use the `Player`-Classes as a reference.
- Add an `ItemsController` and define the endpoints for `Item`-CRUD-Operations
  - All item routes should start with `players/{playerId}/items`.

---

## 7. MongoDb

Now it's time to use a real MongoDb database. Create a class `MongoDbRepository` which has the responsibility to do everything that is related to accessing data in MongoDb. It will replace your existing `FileRepository`.

`MongoDbRepository` should also implement the `IRepository` interface - just like the `FileRepository` does.

When it's time to run your application with MongoDb, remember to replace the `FileRepository` registration with the new `MongoDbRepository` in the DI-Container! (in `Startup.cs`)

Your data should follow this format:

- You can name your database to `game`
- Players should be stored in a collection called `players`
- `Items` should be stored in a list inside the `Player` document

---

## 8. Error handling

Create your own middleware for handling errors called `ErrorHandlingMiddleware`.

Create your own exception class called `NotFoundException`.

Throw the `NotFoundException` when `Player` is not found (incorrect {playerId} passed in the route) when trying to add new `Item`.

Catch the `NotFoundException` in the `ErrorHandlingMiddleware`. And then on the catch block: set the HTTP status code to 404 (not found) to the `HttpContext` that is passed to the middleware.

---

## 9. Model validation using attributes

`NewItem` and `Item` models should have the following properties:

- int Level
- ItemType Type (define the `ItemType` enum yourself with values SWORD, POTION and SHIELD)
- DateTime CreationDate

Define the following validations for the model using attributes:

- `Level` can be only within the range from 1 to 99
- `Type` is one of the types defined in the `ItemType` enum
- `CreationDate` is a date from the past (Create custom validation attribute)

---

## 10. Implement a game rule validation in Controller

This is an extra exercise. You will get bonus points for completing this.

Implement a game rule validation for the `POST`-Route (the one that creates a new item) in the `ItemsController`:

The rule should be: an item of type of `Sword` should not be allowed for a `Player` below level 3.

If the rule is not followed, throw your own custom exception (create the exception class) and catch the exception in an `exception filter`. The `exception filter` should write a response to the client with a _suitable error code_ and a _descriptive error message_. The `exception filter` should be only applied to that specific route.

---

## 11. Queries

### 1. Ranges

Get `Players` who have more than x score

**hints:**

Specify the x in the query like this: `...api/players?minScore=x`

### 2. Selector matching

Get `Player` with a specified name

**hints:**

Make sure the API can handle routes `...api/players/{name}` _and_ `..api/players/{id}`

You can use attribute constraints or use a query parameter like this: `...api/players?name={name}`

### 3. Set operators

Add `Tags` for the `Player` model ( `Tags` can be a list of strings) and create a query that returns the `Players` that have a certain tag.

### 4. Sub documents queries

Find `Players` who have `Item` with certain property

### 5. Size

Get `Players` with certain amount of `Items` by using size method

### 6. Update

Update `Player` name without fetching the `Player`

### 7. Increment

Increment `Player` score without fetching the `Player`

### 8. Push

Add `Item` to the item list property on the `Player`

### 9. Pop and increment as an atomic operation

Remove from `Item` from `Player` and add some score for the `Player`. You can think of this as a `Player` selling an `Item` and getting score as a reward.

**hints:**

The route should be `..api/players/{playerId}/items/` with DELETE Http verb.

### 10. Sorting

Get top 10 `Players` by score in descending order

---

## 12. Aggregation

### 1. Aggregation exercise based on the example in the slides

Find out what is the most common level for a player (example in the slides).

### 2. Intermediate aggregation exercise

Get the item counts for different prices for items.

### 3. Difficult aggregation exercise

Get the average score for players who were created between two dates using aggregation.
