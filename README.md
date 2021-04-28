# GP20-2021-0426-Rest-Gameserver

## Goal of this Assignment
The goal of this assignment is to introduce you to developing REST-APIs using `C#`, `HTTP`, `JSON` and `ASP .NET Core`\
In total, our steps will include:
- An Acme.com Weblink Browser
- Exploring an existing REST-API through HTTP
- Building a Bikesharing Console Application
- Building a REST Game App

## Grading
|Grade  |  Requirement |
|-------|:-------------|
|Failed (F)| Everyone\* |
-------------------------------
\*Just kidding, of course. I'm still working on this :P


## Prerequisites / Requirements
- Make sure, that .NET Core 5 SDK is installed from https://www.microsoft.com/net/download
- I recommend to use Jetbrains Rider as an IDE.\
- Install Unity Hub & Unity.

## Remarks
- In the first Exercise, we are not using any HTTP-Classes, but manually using the HTTP-Protocol with a TCP-Client for educational purposes.


## Part 1 - Tiny Browser:

<img width="579" alt="image" src="https://user-images.githubusercontent.com/7360266/116148852-bcc7df80-a6e1-11eb-9282-370e37c97fc6.png">



### Goal
To have a Acme.com Weblink Browser that prints the current page title as well as a navigatable list of all Links that can be found on the page.

### Preparing a Project
Create a folder named `TinyBrowser`\
Open the Terminal in that Folder
Now, use the command `dotnet new console`\
If it says `dotnet not found`, you have probably not installed .NET Core 5 SDK, yet.\
Else, this command should have created a new C# Project for you. You can go ahead and open the `.csproj`-File in your IDE.\

Add a `.gitignore` in your `TinyBrowser`-Folder that ignores anything you don't want to commit.\
For C# Console Projects, that's at least the `/bin/` and `/obj/`-Folders.\
Afterwards, you may safely go ahead and create a new commit `adds tiny browser project`

### Implementation
- You will need: 
- The `TcpClient`-class which can be created by using its constructor together with arguments for the host name as well as the port number.
  - `GetStream` again gets you the current stream used for the client. It returns a `Stream`.
  - `Close` needs to be called when you are done using the `TcpClient`.
- The `Stream`-class is returned by `GetStream`
  - `Write` allows you to send Bytes over the socket. (Consider using `StreamWriter` though)
  - `Read` allows you to read Bytes over the socket. (Consider using `StreamReader` though)
  - `Close` needs to be called when you are done sending bytes over the stream.
- The `StreamWriter`-class has a constructor that you need to pass a `Stream` into.
  - `Write` allows you to send a string or any other data over the socket.
- The `StreamReader`-class has a constructor that you need to pass a `Stream` into.
  - `ReadToEnd` allows you to read a full string from the socket.
- `Encoding.ASCII.GetBytes` Can convert a `string` to ASCII-`byte[]` for you.
- `Encoding.ASCII.GetString` Can convert a `byte[]` to a `string`.
- `string`
  - `IndexOf(string value, int startIndex)` 
    - Can Find the Index at which a `string` can be found within another. 
    - Returns `-1` if no results were found.
    - e.g.: "Hello World".IndexOf("ll") will return 2.
    - e.g.: "Hello World".IndexOf("Planet") will return -1.
  - `Substring(int startIndex, int length)`
    - Returns the substring of a string between character number `startIndex` and `statIndex + length`.
    - e.g.: "Hello World".Substring(3, 4) will return "lo W";
  - `Path.Combine(string a, string b)`
    - Returns a combined path of a and b. handles dot-notiation `.` and `..` correctly automatically.

So, what is our server supposed to do?
- Send a TCP Request to acme.com using Port 80
- Using the HTTP Protocol
  - I recommend using HTTP 1.1.
  - Make sure to follow the Exact guidelines.
  - Every line is supposed to end with `CRLF` (carriage return). In C# that's `"\r\n"`
  - This is, what a HTTP/1.1-Request might look like:
```
GET / HTTP/1.1
Host: google.com

```
  - Especially don't forget the empty line at the end of your request and the Host-Header :)
- Use a TCP Client.
- Get the Stream.
- Write a valid HTTP-Request to the Stream.
- Fetch the response from the Website
- Search the respone for an occurence of `<title>
    - `<title>` is the start tag of an HTML `title`-Element used for page titles (visible on tabs) in browsers
    - `</title>` is the end tag of an HTML `title`-Element
    - Everything inbetween is the HTML-Content of the Element
    - And in this case, the title of the website
    - Print that string (between `<title>` and `</title>`) to the console.
- Search the response for all occurences of `<a href ="`
  - One sample: `<a href="auxprogs.html">auxiliary programs</a>`
  - Without going into too much detail:
    - `<a>` is the start tag of an HTML `hyperlink`-Element used for clickable links in browsers
    - `href="..."` is an HTML url-Attribute used to give the URL to the Hyperlink
    - `</a>` is the end tag of an HTML `hyperlink`-Element
    - Everything inbetween is the HTML-Content of the Element
    - And in this case, describes the Display Text of the Hyperlink
- For each occurence:
  - Find all letters until the next `"`-symbol.
  - These letters define the local URL to the destination
  - Remember this, so you can navigate to that URL, if the User decides to follow this link
  - Navigate to the next `>`-symbol, so you find the end of the start tag.
  - Every letter until the next occurence of `</a>` are part of the display text.
- Now, when you have all the information (display text & url for each link)
- Print them all to the console
  - Recommendation: Use an iterator i, starting at 0.
  - Iterate over a list of all information that you have stored before.
  - Print: `%INDEX%: %DISPLAYNAME% (%URL%)`, e.g.: `3: auxiliary programs (auxprogs.html)`
- Ask the user for Input
  - it should be a Number between 0 and the number of options
  - Follow the link that the user wants to follow and start at the beginning of the application again
  - (Send a TCP Request to acme.com...)
  - There is a few cases of URLs to consider. Some of them might be links, but...:
    - not to another web page, e.g. `<a href="image.png">` might be a link to an image.
      - i suggest skipping these links
    - to another host, e.g. `<a href="http://google.com/search/settings">`
      - replace the host with `google.com` and the path with `/search/settings/`
    - to a local url, e.g. `<a href="search"> when currently being at host `acme.com` and the path `/hello/world/`
      - keep the host and replace the path with `/hello/world/search/`
    - to a parent url, e.g. `<a href="../another"> when currently being at host `acme.com` and the path `/hello/world/`
      - keep the host and simply replace the path with `/hello/world/../another/` or `/hello/another/`




### Bonus:
- Prettify the Output: Replace any link description that's longer than 15 chars with a shorter version of the first and last 6 chars and ... in the middle.
  - e.g.: `"HelloMyPrettyWorld"` becomes `"HelloM..yWorld"`
- Implement a Back-Button: If the User inputs 'b' for Back, go back (to the previously visited Website.
  - Make sure, to not go forward, when going back twice :)
- Implement a Forward-Button: If the User inputs 'f' for Forward, go forward.
  - Make sure, that there is a website to go forward to :)
- Implement a Refresh-Button: If the User inputs 'r' for Refresh, refresh the page.
  - Make sure, that this won't spam the 'go back' history.
- Implement a History-Button: If the User inputs 'h' for History, he can see websites that he has visited.
  - As well as the date, when the page was opened.
  - If the User visits Website A, then B, then goes back to A, the History should show A, B, A. Not only A.
  - In other words, this history has to be separate from the Back-History.
- Implement a Goto-Button: If the User inputs 'g' for Goto, he can afterwards enter a URL of his own.
- Investigate options of using `XMLReader` instead of searching the `HTML`-Response manually.
  - Do this optional (as in replacable with interfaces)
  - So that I can see, that you also got a solution working
  - Where you manually search the string


## Part 2: GitHub Explorer

<img width="703" alt="image" src="https://user-images.githubusercontent.com/7360266/116456208-46062000-a862-11eb-8bd0-566e7939c265.png">


### Goal
To have a small GitHub Repository Browser by accessing GitHub's public REST API to receive information.

### Preparing a Project
Create a folder named `GitHubExplorer`\
Open the Terminal in that Folder
Now, use the command `dotnet new console`\
Add a `.gitignore` in your `GitHubExplorer`-Folder that ignores anything you don't want to commit.\
For C# Console Projects, that's at least the `/bin/` and `/obj/`-Folders.\
Afterwards, you may safely go ahead and create a new commit `adds github explorer project`

### Preparing REST-API Access

<img width="813" alt="image" src="https://user-images.githubusercontent.com/7360266/116474404-bc158180-a878-11eb-8368-729a863c06bc.png">

In your user-settings (https://github.com/settings/tokens/new), you'll have to create a Personal Access Token. This is the easiest way to access your APIs later on.


### Implementation
- You will need: 
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

So, what is our GitHub Explorer supposed to do?
- Ask the User for a User Name that he'd like to explore.
- Send a HTTP Request to `https://api.github.com/users/{username}` (replace {username} with the user input).
- You can read on the API over here: https://docs.github.com/en/rest/reference/users#get-a-user
- You can read on how to authenticate over REST API over here: https://docs.github.com/en/rest/guides/getting-started-with-the-rest-api
  - In short: you will need a Header with the key `Authorization` and the value `token {yourtoken}`
  - You want to be authenticated with all Requests. Consider using DefaultRequestHeaders :)
- The Response-Object is defined there as well.
- Among others, it contains the fields `name` and `company` which you are able to parse using the JSON-Parse.
  - As a reference, check out above sample on `JsonSerializer`.
- You will also find all other APIs documented over there :)


### Suggested Features:
#### Social Features:
These features should allow you to inspect your own as well as other's user's GitHub profiles, view their repositories (with a few stats on their repositories), as well as their oganizations. And look at an organization's members, to view their profiles and repositories and so forth. They teach you how to fetch and visualize information and how data is linked in REST APIs.
- Showing a User's Profile
- Listing a User's Repositories
- Listing a User's Organizations
  - Listing an Organization's Members (make them visitable)
  - Listing a User's Repositories

#### Issues & Commenting:
These features should allow you to create, inspect and close an issue and to view an issue's comments and add, update and delete them. They teach you how different HTTP-Methods are used for getting, creating, updating and deleting data on a REST API.
- Listing a Repository's Issues
- Creating an Issue
- Closing an Issue (PATCH)
    - Listing all Comments on an Issue
    - Commenting on an Issue
    - Deleting a Comment on an Issue
    - Editing a Comment on an Issue


### Bonus:
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
}

public interface IRepository {
   List<IIssue> GetIssues();
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



## Part 3: Lame-Scooter

- TBD

## Part 4: 

- TBD
