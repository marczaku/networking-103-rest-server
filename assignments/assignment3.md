# Part 3: Lame Scooter (20 Points)

---

## Goal
To implement your own CLI to read information about available Scooters at different stations. Implement several possible sources of information.

---

## Preparing a Project
Create a folder named `LameScooter`\
Open the Terminal in that Folder
Now, use the command `dotnet new console`\
Add a `.gitignore` in your `LameScooter`-Folder that ignores anything you don't want to commit.\
For C# Console Projects, that's at least the `/bin/` and `/obj/`-Folders.\
Afterwards, you may safely go ahead and create a new commit `adds lame scooter project`

---

## Implementation

### Required Methods (List Incomplete)

- `JsonSerializer.Deserialize<T>(string jsonText, JsonSerializerOptions options)` Can convert a `string` to a C#-class of type `T` for you.
  - I recommend this time, to pass `JsonSerializerOptions` and set
    - `PropertyNamingPolicy = JsonNamingPolicy.CamelCase`
    - This enables you to name Properties with UpperCase-First, even, if the JSON-Field starts with LowerCase.
    - e.g.: `public string Name{get; set;}` vs. `{name: "Marc Zaku}"`
  - It requires, that you create a class that matches the response structure.
  - All fields that are returned should exist as a public property with getter and setter.
  - e.g.: Response: `{"name":"Marc Zaku", "job": "Teacher"}` 
  - class: `public class UserResponse{public string Name {get;set;} public string Job {get; set;}}`
  - use: `var userResponse = JsonSerializer.Deserialize<UserResponse>(responseJsonText, jsonSerializerOptions);`
- The `HttpClient`-class which can be created by using its constructor. It is used for making Http-Requests.
  - `DefaultRequestHeaders.Add` can be used to accept default headers that you want all your requests to have.
  - `Dispose` needs to be called when you are done using the `HttpClient`.
  - `Send` and `SendAsync` can be used to send an `HttpRequestMessage` and receive a `HttpResponseMessage`.
- The `HttpRequestMessage`-class can be created by using its constructor
  - The `HttpMethod`-argument defines the HTTP-Method that you are calling. We will mostly, or exclusively use `HttpMethod.Get`
  - The `requestUri`-argument needs to point at the REST API's endpoint.
  - The `Headers.Add`-Method can be used to add headers.
    - e.g. `request.Headets.Add("Content-Language", "se");` would add a header requesting Swedish Content-Language.
  - The `Content`-Property can be used to assign a Body to your HTTP request.
    - The `StringContent`-class takes a string in its constructor and enables you to add a string as a HTTP request's body.
- The `HttpResponseMessage`-class contains all sorts of information that has been sent as a response.
  - `StatusCode` contains the HTTP-StatusCode, e.g. `200: OK`
  - `Headers` contains all HTTP-Headers as Key-Value-Pairs.
  - `response.Content.ReadAsStream()` can be used to receive a stream for the HTTP-Body of the response.
- The `StreamReader`-class has a constructor that you need to pass a `Stream` into.
  - `ReadToEnd` allows you to read a full string from the stream.

### Routines

- Read the requested station name from the command line arguments.
- Load the [scooters.json-File](https://raw.githubusercontent.com/marczaku/GP20-2021-0426-Rest-Gameserver/main/assignments/scooters.json)
- Look at the Format of the JSON-Response. Create classes that resemble the structure of the response.
- Use the `JsonSerializer.Deserialize<T>`-Method to deserialize the JSON-`string` to a C# object.
- Search the stations for a station with a matching name.
- Return the number of available scooters at this station.
- Print the result to the console: `Number of Scooters Available at this Station: 1234`

---

## Step By Step:

---

### 1. Print Console Arguments and Test the Output:

Change the Main Method to this:

```cs
static async Task Main(string[] args)
{
    Console.WriteLine(args[0]);
}
```

It will print the first command line argument passed to the application to the console. Try running the following commands in the Terminal:
- `dotnet run Hello`
- `dotnet run Hello-World`
- `dotnet run Hello World`
- `dotnet run Hello\ World`
- `dotnet run "Hello World" Planet`

What have you learned?

What happens, if you try Debugging your Application?

If you want to debug your application with arguments, follow the steps on the [Rider Documentation](https://www.jetbrains.com/help/rider/Get_Started_with_Run_Debug_Configurations.html). To sum things up: On the Top right, click on your configuration (the dropdown with your Project Name), click on Edit Configuration and put the arguments that you want to pass into the `Program Arguments`-Field. Are you able to print "Hello World" this way?

---

### 2. Create an Interface

In order to be able to easily exchange our Database-Implementations later, create the following interface:

```cs
public interface ILameScooterRental
{
    Task<int> GetScooterCountInStation(string stationName);
}
```

And use the interface in your Main-Method:

```cs
static async Task Main(string[] args)
{
    ILameScooterRental rental = null; // Replace with new XXX() later.
    // The await keyword makes sure that we wait for the Task to complete.
    // and makes the result of the task available. Task<int> => int.
    var count = await rental.GetScooterCountInStation(null); // Replace with command line argument.
    Console.WriteLine("Number of Scooters Available at this Station: "); // Add the count that is returned above to the output.
}
```

---

### 3. Create a class implementing the interface



<img width="564" alt="image" src="https://user-images.githubusercontent.com/7360266/117221072-d4514780-ae08-11eb-91e9-cfc6a680c4d8.png">

To prepare this part, Download [scooters.json](https://raw.githubusercontent.com/marczaku/GP20-2021-0426-Rest-Gameserver/main/assignments/scooters.json) from this repository and copy it into your LameScooter-Project, right next to `LameScooter.csproj` and `Program.cs`
Create a class named `OfflineLameScooterRental` which implements `ILameScooterRental` and loads the text form the file `scooters.json` and parses the text into a C# Object.
In order for this to work, you will have to make sure, that the json file gets copied to your built application as well. To do so, right-click the `scooters.json`-file in Rider and open its Properties. Change `Copy to Output Directory` to `Copy Always`.

Now, when you have parsed the information into a C#-Object, find the right station for the given name, and return the amount of bikes available at this station.

More Details:
- Remember to use `async` and `await` keywords in your implementation of asynchronous methods.
- Next, you need to define your own `LameScooterStationList`-class that corresponds to the `scooters.json`-contents. It should contain Properties with same Names and Types as found in the Json.
- Deserialize the string to a C# object using `JsonSerializer.Deserialize<LameScooterStationList>`
- Find the information you are looking for from the `LameScooterStationList` (scooter count in a certain station) and return it as the result from the method

Samples:
- `dotnet run Linnanmäki`
- `dotnet run Sepänkatu`
- `dotnet run Pohjolankatu`

---

### 4. Handle Argument Errors

Throw an `ArgumentException` (provided in `System`) if the user calls `GetScooterCountInStation` with a string which contains numbers.
Catch the exception in the calling code (your Main Method) and print "Invalid Argument: " and the `Message`-Property of the exception.

---

### 5. Create and throw your own Exception
Create your own `Exception` called `NotFoundException`. Throw it, if the station can not be found. 
Catch it in the calling code and print "Could not find: " and the Message Property of the exception.

---

### 6. Create an alternative implementation
Create a class called `DeprecatedLameScooterRental` which also implements the `ILameScooterRental`-Interface. Download [scooters.txt](https://raw.githubusercontent.com/marczaku/GP20-2021-0426-Rest-Gameserver/main/assignments/scooters.txt) and also put it into your project and again make sure, that it gets copied when Building. Find a way to read the required data from the file.

How much code can you share? How much code do you have to duplicate?

Samples:
- `dotnet run Linnanmäki`
- `dotnet run Sepänkatu`
- `dotnet run Pohjolankatu`

---

### 7. Implement more Command Line Arguments
Make the console application accept an additional, optional string argument, `offline` or `deprecated` and decide the implementation of `ILameScooterRental` based on that.

Samples:
- `dotnet run Linnanmäki offline`
- `dotnet run Sepänkatu deprecated`
- `dotnet run Pohjolankatu offline`

---

### 8. RealTime-Bike-Data
Create a class called `RealTimeLameScooterRental` which also implements the `ILameScooterRental`-Interface. Make it load the BikeData using a HTTP-GET-Request on the URL `https://raw.githubusercontent.com/marczaku/GP20-2021-0426-Rest-Gameserver/main/assignments/scooters.json` and use the result of that request. Use the argument `realtime` to decide the implementation of `ILameScooterRental` with this new class.

Samples:
- `dotnet run Linnanmäki realtime`
- `dotnet run Sepänkatu realtime`
- `dotnet run Pohjolankatu realtime`

---

### BONUS: 9. MongoDB Database

Documentation of MongoDB Server:
- [Install MongoDB](https://docs.mongodb.com/manual/installation/)
- [Getting Started](https://docs.mongodb.com/manual/tutorial/getting-started/)

Documentation of MongoDB .NET:
- [Install MongoDB.Driver](http://mongodb.github.io/mongo-csharp-driver/2.7/getting_started/installation/)
- [Quick Tour](http://mongodb.github.io/mongo-csharp-driver/2.7/getting_started/quick_tour/)

Create a class called `MongoDBLameScooterRental` which also implements the `ILameScooterRental`-Interface. Make it load the BikeData from a Mongo-Collection named `lamescooters`. Use the command line argument `mongodb` to decide the implementation of `ILameScooterRental` with this new class.

You need to create and populate the collection first.
Open the Terminal and connect to your local MongoDB-Server.
If you haven't yet, follow your OS' `Install MongoDB`-Tutorial and make sure to also launch the Server.
- In the Terminal, enter `mongo` to connect to your Database.
- Use `db` to see the current database's name.
- Use `use lamescooters` to switch to / create a collection named lamescooters.
- Use `db.lamescooters.insertMany([.......]);` but replace `[.....]` with the part that you find in the `scooters.json`-file. Make sure, to select the WHOLE CONTENT between `[` and `]`.
- Try querying a station by using `db.lamescooters.find({"name":"Lammasrinne"});`. It should return exactly one station with that name.

Add MongoDB.Driver as a package to your Project, so you can use MongoDB in C#:
`dotnet add package MongoDB.Driver`

Now, follow the QuickTour of the Mongo C# Driver to find out, how to query a station from that database and try to return the available bike count.

Samples:
- `dotnet run Linnanmäki mongodb`
- `dotnet run Sepänkatu mongodb`
- `dotnet run Pohjolankatu mongodb`

---

### 10. GOOD JOB!

You have completed the whole introduction course! Part 4 will contain the actual assignment. But I've figured, that it'll do, if I hand out that part in the beginning 
