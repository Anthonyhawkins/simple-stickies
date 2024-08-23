# Simple Sticky Note Web App
The definition of a Go-based StickyNote Application Server that uses the net/http package for routing and CRUD operations. Notes are stored as text files, and the html/template package is used to render HTML pages for creating, viewing, updating, and deleting sticky notes. 

## Routes
The routes provided are the actions that the server should perform given a specific HTTP request.

| **Route Name**   | **Route**                                            | **HTTP Method** | **Description**                                             | **Action**                                                                                      |
|------------------|------------------------------------------------------|-----------------|-------------------------------------------------------------|-------------------------------------------------------------------------------------------------|
| **HOME**         | `/`                                                  | GET             | Displays the homepage.                                      | The server should return an HTML template explaining the app and showing the menu.              |
| **INDEX**        | `/notes`                                             | GET             | Lists all sticky notes.                                     | The server should read all files in the notes directory, generate a list, and return an HTML template displaying the titles and contents of all sticky notes. |
| **NEW**          | `/notes/new`                                         | GET             | Displays the form to create a new sticky note.              | The server should return an HTML template with a form for creating a new sticky note.            |
| **CREATE**       | `/notes?name=<name>&text=<text>`                     | POST            | Creates a new sticky note.                                  | The server should create a new file named `<note-name>.txt` in the filesystem, with the content specified in the `text` query parameter.|
| **SHOW**         | `/notes?name=<name>`                                 | GET             | Displays a specific sticky note.                            | The server should read the file `<note-name>.txt` from the filesystem and return an HTML template displaying the note's title and contents. |
| **EDIT**         | `/notes/edit?name=<name>`                            | GET             | Displays the form to update an existing sticky note.        | The server should read the file `<note-name>.txt`, and return an HTML template with a form pre-filled with the note's current contents for editing. |
| **UPDATE**       | `/notes?name=<name>&text=<text>&method=PUT`          | POST            | Updates an existing sticky note.                            | The server should update the file `<note-name>.txt` with the new content specified in the `text` query parameter. |
| **DESTROY**      | `/notes?name=<name>&method=DELETE`                   | POST            | Deletes a sticky note.                                      | The server should delete the file `<note-name>.txt` from the filesystem and redirect the user to the list of all notes. |


## Pages

The pages provided describe what the user interface should be and what the user should be able do on each page.

| **Page Name**    | **Description**                                             | **User Actions**                                            |
|------------------|-------------------------------------------------------------|-------------------------------------------------------------|
| **HOME**         | Explains the application and provides navigation links.     | User can navigate to the ALL page or create a new note.     |
| **ALL (INDEX)**  | Lists all sticky notes in a grid or column layout.           | User can click on a note title to view it, or click a link to create a new note. |
| **NEW**          | Form to create a new sticky note.                            | User enters a title and text, then clicks the submit button to create the note. User can also cancel to return to the ALL page. |
| **SHOW**         | Displays the title and content of a specific sticky note.    | User can delete the note or update it. Deleting redirects to ALL, updating redirects to the UPDATE page. |
| **UPDATE**       | Form to update an existing sticky note.                      | User can edit the title and text, then click the update button to save changes, or click cancel to return to ALL. |


## Basic Validation Requirements
- **Note Length**: 
  - Notes cannot be longer than 250 characters. 
  - If a note exceeds this limit, the server should return an error, and the user should be redirected back to the form with the note content pre-populated.

- **Title Validation**:
  - Titles cannot contain spaces and must only include letters (a-z, A-Z) and numbers (0-9).
  - Titles cannot be longer than 50 characters.
  - If the title does not meet these criteria, the server should return an error, and the user should be redirected back to the form with the title and other submitted values pre-populated.

- **Failed Validation**:
  - If a request fails validation due to note length or title constraints, the server should:
    - Redirect the user back to the form (either for creating or updating a note).
    - Pre-populate the form fields with the submitted values (title and text) so the user can correct the input without losing their work.


# Required Packages, Types, and Functions

The following packages, functions, and types are required to implement the above requirements, along with a basic understanding of HTML and CSS

### Packages

| **Package**      | **Description**                                                                                  |
|------------------|--------------------------------------------------------------------------------------------------|
| `net/http`       | Provides tools for building HTTP servers and clients, including routing, request handling, and serving static files. |
| `html/template`  | Renders HTML templates with dynamic data safely, preventing XSS vulnerabilities.                  |
| `os`             | Handles file and system operations, such as creating, reading, and deleting files.               |
| `io/ioutil`      | Offers utility functions like `ReadFile` and `WriteFile` for file I/O (now merged into `os`/`io`).|

### `net/http` Functions and Types

| **Function/Type**                                                 | **Description**                                                                                           |
|-------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------|
| [`http.HandleFunc`](https://pkg.go.dev/net/http#HandleFunc)        | Registers a route with a handler function to process HTTP requests.                                        |
| [`http.ListenAndServe`](https://pkg.go.dev/net/http#ListenAndServe) | Starts an HTTP server on the specified address and port.                                                   |
| [`http.Request`](https://pkg.go.dev/net/http#Request)              | Represents an HTTP request, including URL, method, headers, and body.                                      |
| [`http.ResponseWriter`](https://pkg.go.dev/net/http#ResponseWriter) | Used to construct HTTP responses, including setting status codes and writing response data.                |
| [`http.ServeMux`](https://pkg.go.dev/net/http#ServeMux)            | Default HTTP request multiplexer that routes requests to registered handlers.                              |
| [`http.Error`](https://pkg.go.dev/net/http#Error)                  | Sends an HTTP error response with a specified status code.                                                 |
| [`http.Redirect`](https://pkg.go.dev/net/http#Redirect)            | Redirects the client to a different URL with a specific status code.                                       |
| [`http.FileServer`](https://pkg.go.dev/net/http#FileServer)        | Serves files from the filesystem via HTTP, often used for serving static files.                            |

### `html/template` Functions and Types

| **Function/Type**                                                 | **Description**                                                                                           |
|-------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------|
| [`template.New`](https://pkg.go.dev/html/template#New)             | Creates a new template instance.                                                                           |
| [`template.ParseFiles`](https://pkg.go.dev/html/template#ParseFiles) | Parses one or more files into templates for rendering HTML.                                                |
| [`template.Template.Execute`](https://pkg.go.dev/html/template#Template.Execute) | Executes a template, rendering it with dynamic data and writing the result to the provided `io.Writer`.    |

### `os` Functions

| **Function/Type**                                                 | **Description**                                                                                           |
|-------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------|
| [`os.Create`](https://pkg.go.dev/os#Create)                       | Creates a new file for writing.                                                                            |
| [`os.Open`](https://pkg.go.dev/os#Open)                           | Opens a file for reading.                                                                                  |
| [`os.Remove`](https://pkg.go.dev/os#Remove)                       | Deletes a file from the filesystem.                                                                        |
| [`os.ReadFile`](https://pkg.go.dev/os#ReadFile)         | Reads the contents of a file into memory.                                          |
| [`os.WriteFile`](https://pkg.go.dev/os#WriteFile)       | Writes data to a file, creating it if it doesn't exist.                            |
| [`os.ReadDir`](https://pkg.go.dev/os#ReadDir)           | Reads the contents of a directory.                                                 |

