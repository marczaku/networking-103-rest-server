# Part 1 - Tiny Browser (50 Points):

<img width="579" alt="image" src="https://user-images.githubusercontent.com/7360266/116148852-bcc7df80-a6e1-11eb-9282-370e37c97fc6.png">

---

## Goal
To have a Acme.com Weblink Browser that prints the current page title as well as a navigatable list of all Links that can be found on the page.

---

## Preparing a Project
Create a folder named `TinyBrowser`\
Open the Terminal in that Folder
Now, use the command `dotnet new console`\
If it says `dotnet not found`, you have probably not installed .NET Core 5 SDK, yet.\
Else, this command should have created a new C# Project for you. You can go ahead and open the `.csproj`-File in your IDE.\

Add a `.gitignore` in your `TinyBrowser`-Folder that ignores anything you don't want to commit.\
For C# Console Projects, that's at least the `/bin/` and `/obj/`-Folders.\
Afterwards, you may safely go ahead and create a new commit `adds tiny browser project`

---

## Implementation

### Required Methods

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

### Routines

- Send a TCP Request to acme.com using Port 80
- Using the HTTP Protocol
  - I recommend using HTTP 1.1.
  - Make sure to follow the Exact guidelines.
  - Every line is supposed to end with `CRLF` (carriage return). In C# that's `"\r\n"`
  - This is, what a HTTP/1.1-Request might look like:
```http
GET / HTTP/1.1
Host: google.com

```
  - Especially don't forget the empty line at the end of your request and the Host-Header :)
- Use a TCP Client.
- Get the Stream.
- Write a valid HTTP-Request to the Stream.
- Fetch the response from the Website
- Search the respone for an occurence of `<title>`
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

---

## Bonus:
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