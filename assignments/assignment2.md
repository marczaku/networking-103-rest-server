# Part 2: GitHub Explorer (50 Points)

<img width="703" alt="image" src="https://user-images.githubusercontent.com/7360266/116456208-46062000-a862-11eb-8bd0-566e7939c265.png">

---

## Goal
To have a small GitHub Repository Browser by accessing GitHub's public REST API to receive information.

---

## Preparing a Project
Create a folder named `GitHubExplorer`\
Open the Terminal in that Folder
Now, use the command `dotnet new console`\
Add a `.gitignore` in your `GitHubExplorer`-Folder that ignores anything you don't want to commit.\
For C# Console Projects, that's at least the `/bin/` and `/obj/`-Folders.\
Afterwards, you may safely go ahead and create a new commit `adds github explorer project`

---

## Preparing REST-API Access

<img width="813" alt="image" src="https://user-images.githubusercontent.com/7360266/116474404-bc158180-a878-11eb-8368-729a863c06bc.png">

In your user-settings (https://github.com/settings/tokens/new), you'll have to create a Personal Access Token. This is the easiest way to access your APIs later on.

---

## Implementation

### Required Methods

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
- `JsonSerializer.Deserialize<T>(string jsonText)` Can convert a `string` to a C#-class of type `T` for you.
  - It requires, that you create a class that matches the response structure.
  - All fields that are returned should exist as a public property with getter and setter.
  - e.g.: Response: `{"name":"Marc Zaku", "job": "Teacher"}` 
  -       Class: `public class UserResponse{public string name{get;set;} public string job {get; set;}}
  -       Use: `var userResponse = JsonSerializer.Deserialize<UserResponse>(responseJsonText);`

### Routines

- Ask the User for a User Name that he'd like to explore.
- Send a HTTP Request to `https://api.github.com/users/{username}` (replace {username} with the user input).
- You can read on how to authenticate over REST API over here: https://docs.github.com/en/rest/guides/getting-started-with-the-rest-api
  - In short: you will need a Header with the key `Authorization` and the value `token {yourtoken}`
  - You want to be authenticated with all Requests. Consider using DefaultRequestHeaders :)
- You can read on the API over here: https://docs.github.com/en/rest/reference/users#get-a-user
- The Response-Object is defined there as well.
- Among others, it contains the fields `name` and `company` which you are able to parse using the JSON-Parse.
  - As a reference, check out above sample on `JsonSerializer`.
- You will also find all other APIs documented over there :)

---

## Suggested Features:

### Social Features:
These features should allow you to inspect your own as well as other's user's GitHub profiles, view their repositories (with a few stats on their repositories), as well as their oganizations. And look at an organization's members, to view their profiles and repositories and so forth. They teach you how to fetch and visualize information and how data is linked in REST APIs.
- Showing a User's Profile
- Listing a User's Repositories
- Listing a User's Organizations
  - Listing an Organization's Members (make them visitable)
  - Listing a User's Repositories

### Issues & Commenting:
These features should allow you to create, inspect and close an issue and to view an issue's comments and add, update and delete them. They teach you how different HTTP-Methods are used for getting, creating, updating and deleting data on a REST API.
- Listing a Repository's Issues
- Creating an Issue
- Closing an Issue (PATCH)
    - Listing all Comments on an Issue
    - Commenting on an Issue
    - Deleting a Comment on an Issue
    - Editing a Comment on an Issue

---

## Bonus:
Develop a proper interface for all of your interactions with GitHub's Rest API.
Do not make any HTTP-Requests outside of those interfaces.
Imagine something like this:

```cs
public interface IGitHubAPI {
   List<Issue> GetIssues(string userName, string repositoryName);
   Issue CreateIssue(string userName, string repositoryName, string title, string description);
}

void Main(){
   IGitHubAPI gitHubAPI = new GitHubAPI();
   var issues = gitHubAPI.GetIssues("marczaku","GP20-2021-0426-Rest-Gameserver");
}
```

Now, take your API even one step further. 
Wrap all your API's return values into objects that do not only contain data.
But also methods to call the RESTful API.
Imagine something like this:

```cs
public interface IGitHubAPI {
   IUser GetUser(string userName);
}

public interface IUser {
   IRepository GetRepository(string repositoryName);
   string Name {get;}
   string Location {get;}
}

public interface IRepository {
   List<IIssue> GetIssues();
   string Name {get;}
   string Description {get;}
}

void Main(){
   IGitHubAPI gitHubAPI = new GitHubAPI();
   var user = gitHubAPI.GetUser("marczaku");
   var repository = user.GetRepository("GP20-2021-0426-Rest-Gameserver");
   var issues = repository.GetIssues();
}
```

Do you see the differences and improvements in these implementations?
Do you only see Advantages, or are there also disadvantages?