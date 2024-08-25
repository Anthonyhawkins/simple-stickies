# Simple Sticky Note Web App
The definition of a Go-based StickyNote Application Server that uses the net/http package for routing and CRUD operations. Notes are stored as text files, and the html/template package is used to render HTML pages for creating, viewing, updating, and deleting sticky notes. 

# Phase 1 - Minimal Viable Product

## Routes
The routes provided are the actions that the server should perform given a specific HTTP request.

Here’s the updated table with the columns ordered as requested:

| **Route Name**   | **Method**       | **Route**                                            | **Body (Form Params)**                                  | **Description**                                             | **Action**                                                                                      |
|------------------|-----------------|------------------------------------------------------|---------------------------------------------------------|-------------------------------------------------------------|-------------------------------------------------------------------------------------------------|
| **HOME**         | GET             | `/`                                                  | N/A                                                     | Displays the homepage.                                      | The server should return an HTML template explaining the app and showing the menu.              |
| **INDEX**        | GET             | `/notes`                                             | N/A                                                     | Lists all sticky notes.                                     | The server should read all files in the notes directory, generate a list, and return an HTML template displaying the titles and contents of all sticky notes. |
| **NEW**          | GET             | `/notes/new`                                         | N/A                                                     | Displays the form to create a new sticky note.              | The server should return an HTML template with a form for creating a new sticky note.            |
| **CREATE**       | POST            | `/notes`                                             | `name=<name>` `text=<text>`                             | Creates a new sticky note.                                  | The server should create a new file named `<note-name>.txt` in the filesystem, with the content specified in the `text` form parameter. |
| **SHOW**         | GET             | `/notes?name=<name>`                                 | N/A                                                     | Displays a specific sticky note.                            | The server should read the file `<note-name>.txt` from the filesystem and return an HTML template displaying the note's title and contents. |
| **EDIT**         | GET             | `/notes/edit?name=<name>`                            | N/A                                                     | Displays the form to update an existing sticky note.        | The server should read the file `<note-name>.txt`, and return an HTML template with a form pre-filled with the note's current contents for editing. |
| **UPDATE**       | POST            | `/notes?method=PUT`                                  | `name=<name>` `text=<text>`                             | Updates an existing sticky note.                            | The server should update the file `<note-name>.txt` with the new content specified in the `text` form parameter. |
| **DESTROY**      | POST            | `/notes?method=DELETE`                               | `name=<name>`                                           | Deletes a sticky note.                                      | The server should delete the file `<note-name>.txt` from the filesystem and redirect the user to the list of all notes. |


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


## Required Packages, Types, and Functions

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



# Phase 2 - Boards

## **1. Implement SQL Database**
- **Description**: Transition from using a filesystem-based storage to a SQL database for storing sticky notes, boards, and tags.
- **Requirements**:
  - **Database Setup**:
    - Implement a SQL database compatible with SQLite.
    - The database should store sticky notes, boards, and tags.

## **2. Add the Concept of Boards**
- **Description**: Introduce the concept of boards, allowing users to organize notes into different boards.
- **Requirements**:
  - **Board Association**:
    - Each note can belong to only one board.
    - A board can have one or more notes.
  - **UI Changes**:
    - Update the user interface to allow users to select a board when creating or editing a note.
    - Include a view to display all notes associated with a particular board.

## **4. Create Database Schema**
- **Description**: Define the schema for the SQLite database, including tables for notes, boards, and tags.
- **Requirements**:
  - **Schema Design**:
    - Design the database schema to support notes, boards, and tags with the necessary relationships.
  - **SQL Commands**:
    - Create the necessary `CREATE TABLE` commands compatible with SQLite.

## **Database Schema Example**

This schema can be used to start, any additional modifications or updates will be for you to design based on the requirements in each phase.
**Note** - Syntax might be different depending on the type of Database you are using, i.e. SQLite vs Postgres etc.

```sql
CREATE TABLE boards (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT NOT NULL
);

CREATE TABLE notes (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    title TEXT NOT NULL,
    content TEXT NOT NULL,
    board_id INTEGER,
    FOREIGN KEY (board_id) REFERENCES boards(id)
);

```

## New Packages to Consider
| **Package Name**           | **Description**                                                                                       | **Link**                                                                                       |
|----------------------------|-------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------|
| `database/sql`             | Provides a generic interface for SQL databases. It allows you to work with different SQL drivers.      | [database/sql](https://pkg.go.dev/database/sql)                                                |
| `github.com/mattn/go-sqlite3` | A SQLite driver for Go that implements the `database/sql` interface, enabling interaction with SQLite databases. | [go-sqlite3](https://pkg.go.dev/github.com/mattn/go-sqlite3)                                   |
| `github.com/lib/pq`        | A pure Go Postgres driver for the `database/sql` package.                                              | [pq](https://pkg.go.dev/github.com/lib/pq)                                                     |


## New Routes

Additional routes table based on the specified requirements for managing boards:

| **Route Name**        | **Method**       | **Route**                    | **Body (Form Params)**                                  | **Description**                                                  | **Action**                                                                                                      |
|-----------------------|-----------------|------------------------------|---------------------------------------------------------|------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| **NEW BOARD**      | GET             | `/boards/new`            | N/A                                                     | Returns a form to create a new board.                         | The server should return an HTML template with a form for creating a new board.                              |
| **CREATE BOARD**   | POST            | `/boards`                | `name=<name>`                                            | Creates a new board and redirects to the board index.      | The server should create a new board in the database and redirect to the board index page.                |
| **INDEX BOARD**    | GET             | `/boards`                | N/A                                                     | Returns a list of all boards and the number of notes in each.| The server should retrieve and display all boards with a count of associated notes for each board.        |
| **SHOW BOARD**     | GET             | `/boards?name=<name>`    | N/A                                                     | Returns a single board and the notes within it.               | The server should display the selected board and list all notes associated with it.                          |
| **UPDATE BOARD**   | POST            | `/boards?method=PUT`     | `name=<name>`                                            | Updates the name of a board and redirects to the show page.   | The server should update the board name in the database and redirect to the board’s detail page.           |
| **DELETE BOARD**   | POST            | `/boards?method=DELETE`  | `name=<name>`                                            | Deletes a board only if no notes are present, redirects to the index. | The server should delete the board if it has no notes associated, and then redirect to the board index page. |

## New Pages

| **Page Name**        | **Description**                                             | **User Actions**                                            |
|----------------------|-------------------------------------------------------------|-------------------------------------------------------------|
| **NEW BOARD**     | Displays a form to create a new board.                   | User can enter a name for the new board and submit the form to create it. There should also be a cancel button to return to the board index. |
| **INDEX BOARD**   | Lists all boards and the number of notes in each one.   | User can see a list of all boards with a count of associated notes. Each board name should be a clickable link that takes the user to the SHOW BOARD page. There should also be a link to create a new board. |
| **SHOW BOARD**    | Displays the details of a single board and lists all notes within that board. | User can see the board's name and the list of notes within that board. There should be options to edit the board's name, delete the board (if no notes are associated), or view individual notes. |
| **EDIT BOARD**    | Displays a form to update an existing board.             | User can update the name of the board and submit the form to save changes. There should also be a cancel button to return to the SHOW BOARD page. |
| **DELETE BOARD**  | Confirms the deletion of a board.                        | User is prompted to confirm the deletion of the board. If confirmed, the board is deleted if no notes are associated, and the user is redirected to the INDEX BOARD page. If not confirmed, the user is returned to the SHOW BOARD page. |

## Updated Routes

| **Route Name**   | **Method**       | **Route**                                            | **Body (Form Params)**                                  | **Description**                                             | **Action**                                                                                      |
|------------------|-----------------|------------------------------------------------------|---------------------------------------------------------|-------------------------------------------------------------|-------------------------------------------------------------------------------------------------|
| **NEW**          | GET             | `/notes/new`                                         | N/A                                                     | Displays the form to create a new sticky note.              | The server should return an HTML template with a form for creating a new sticky note, including a dropdown or selection input for choosing a board. |
| **CREATE**       | POST            | `/notes`                                             | `name=<name>`<br>`text=<text>`<br>`board=<board>` | Creates a new sticky note.                                  | The server should create a new file named `<note-name>.txt` in the filesystem, with the content specified in the `text` form parameter, and associate it with the selected board. |
| **EDIT**         | GET             | `/notes/edit?name=<name>`                            | N/A                                                     | Displays the form to update an existing sticky note.        | The server should return an HTML template with a form pre-filled with the note's current contents and board for editing. The user should be able to update the board. |
| **UPDATE**       | POST            | `/notes?method=PUT`                                  | `name=<name>`<br>`text=<text>`<br>`board=<board>` | Updates an existing sticky note.                            | The server should update the file `<note-name>.txt` with the new content specified in the `text` form parameter, and update the associated board. |

### Modifications Summary:
- **NEW Note Page**: The form for creating a new note should include an option to select a board.
- **CREATE Route**: The request body should include a `board` parameter to associate the note with a specific board when it is created.
- **EDIT Note Page**: The form for editing a note should allow the user to update the board.
- **UPDATE Route**: The request body should include an updated `board` parameter, allowing the board association to be modified during note updates.

## Updates Pages

| **Page Name**    | **Description**                                             | **User Actions**                                            | **Modifications Needed**                                        |
|------------------|-------------------------------------------------------------|-------------------------------------------------------------|-----------------------------------------------------------------|
| **ALL (INDEX)**  | Lists all sticky notes in a grid or column layout.           | User can click on a note title to view it, or click a link to create a new note. | The list should display the board name alongside each note's title. |
| **SHOW**         | Displays the title and content of a specific sticky note.    | User can delete the note or update it. Deleting redirects to ALL, updating redirects to the UPDATE page. | The note's board should be displayed prominently along with the title and content. |
| **NEW**          | Form to create a new sticky note.                            | User enters a title and text, selects a board, and clicks the submit button to create the note. User can also cancel to return to the ALL page. | Add a dropdown or selection input for choosing a board when creating a new note. |
| **EDIT**         | Form to update an existing sticky note.                      | User can edit the title, text, and board, then click the update button to save changes, or click cancel to return to SHOW. | Display the current board and allow the user to update it with a dropdown or selection input. |

### Modifications Summary:
- **ALL (INDEX) Page**: Update the list to include the board for each note, displaying it next to the note's title.
- **SHOW Page**: Display the board of the note along with its title and content on the note's detail view.
- **NEW Page**: Include a board selection input in the form when creating a new note.
- **EDIT Page**: Include a board selection input in the form, pre-filled with the current board, allowing the user to change it.

Here’s the updated phase for implementing the search feature with the term "boards" instead of "categories":


# Phase 3 - Search

## New Routes

| **Route Name**        | **Method**       | **Route**                    | **Body (Form Params)**   | **Description**                                                  | **Action**                                                                                                      |
|-----------------------|-----------------|------------------------------|--------------------------|------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| **SEARCH PAGE**       | GET             | `/search`                    | N/A                      | Returns the search page with a form to submit a query.           | The server should return an HTML template with a form for entering search terms.                               |
| **SEARCH RESULTS**    | GET             | `/search/results?query=<query>` | N/A                   | Returns the search results based on the submitted query.         | The server should process the search query, retrieve matching notes, and return an HTML template displaying the results. |

## New Pages

| **Page Name**         | **Description**                                             | **User Actions**                                            | **Modifications Needed**                                        |
|-----------------------|-------------------------------------------------------------|-------------------------------------------------------------|-----------------------------------------------------------------|
| **SEARCH PAGE**       | Displays a form to submit a search query.                   | User can enter search terms and submit the form to search for notes. | Create a simple form with an input field for the search query and a submit button. |
| **SEARCH RESULTS**    | Displays the results of a search query.                     | User can view the list of notes that match the search criteria, with links to view individual notes. | Display the titles and boards of the matching notes, with each title linked to the note’s detail page. |

## Summary

- **SEARCH PAGE**: 
  - A new page where users can enter search terms to find specific notes.
  - The form submits the search query to the server via a GET request.

- **SEARCH RESULTS**:
  - A results page that displays a list of notes matching the search criteria.
  - The results include the note titles, boards, and links to view each note in detail.

# Phase 4 - Expose REST API 

Implement a JSON REST API

### JSON API Routes Table

| **Route Name**        | **Method**       | **Route**                         | **JSON Object Type**                                    | **Description**                                                  | **Action**                                                                                                      |
|-----------------------|-----------------|-----------------------------------|---------------------------------------------------------|------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| **GET ALL NOTES**     | GET             | `/api/notes`                      | Array of `Note` objects                                  | Retrieve a list of all sticky notes in JSON format.              | The server should return a JSON array of all notes, including their titles, content, and associated boards.     |
| **GET NOTE**          | GET             | `/api/notes/{id}`                 | `Note` object                                            | Retrieve a specific sticky note by its ID in JSON format.        | The server should return a JSON object of the specified note, including its title, content, and associated board.|
| **CREATE NOTE**       | POST            | `/api/notes`                      | `Note` object                                            | Create a new sticky note from a JSON payload.                    | The server should create a new note based on the JSON payload and return the created note with its ID.          |
| **UPDATE NOTE**       | PUT             | `/api/notes/{id}`                 | `Note` object                                            | Update an existing sticky note by its ID from a JSON payload.    | The server should update the specified note with the data provided in the JSON payload.                         |
| **DELETE NOTE**       | DELETE          | `/api/notes/{id}`                 | N/A                                                      | Delete a specific sticky note by its ID.                         | The server should delete the specified note and return a confirmation message in JSON.                          |
| **GET ALL BOARDS**    | GET             | `/api/boards`                     | Array of `Board` objects                                 | Retrieve a list of all boards in JSON format.                    | The server should return a JSON array of all boards, including their names and note counts.                     |
| **GET BOARD**         | GET             | `/api/boards/{id}`                | `Board` object                                           | Retrieve a specific board by its ID in JSON format.              | The server should return a JSON object of the specified board, including its name and associated notes.         |
| **CREATE BOARD**      | POST            | `/api/boards`                     | `Board` object                                           | Create a new board from a JSON payload.                          | The server should create a new board based on the JSON payload and return the created board with its ID.        |
| **UPDATE BOARD**      | PUT             | `/api/boards/{id}`                | `Board` object                                           | Update an existing board by its ID from a JSON payload.          | The server should update the specified board with the data provided in the JSON payload.                        |
| **DELETE BOARD**      | DELETE          | `/api/boards/{id}`                | N/A                                                      | Delete a specific board by its ID.                               | The server should delete the specified board and return a confirmation message in JSON.                         |
| **GET NOTE BOARD**    | GET             | `/api/boards/{id}/notes`          | Array of `Note` objects                                  | Retrieve all notes within a specific board in JSON format.       | The server should return a JSON array of all notes associated with the specified board.                         |

### JSON Object Types Overview

- **Note Object**: Represents a sticky note, including fields such as `id`, `title`, `content`, and `board_id`.
- **Board Object**: Represents a board, including fields such as `id`, `name`, and `note_count`.

The `GET NOTE BOARD` route allows users to retrieve all notes within a specific board, making it easier to organize and manage notes by board.
### JSON Objects Overview

- **Note Object**: Represents a sticky note with the following structure:
  ```json
  {
    "id": 1,
    "title": "Sample Note",
    "content": "Content of the note",
    "board_id": 1
  }
  ```

- **Board Object**: Represents a board with the following structure:
  ```json
  {
    "id": 1,
    "name": "Work",
    "note_count": 5
  }
  ```

Here’s the updated phase for "Authentication and Authorization," incorporating session cookies for HTML routes and JWT for API routes:

# Phase 5 - Authentication and Authorization

## **1. User Registration and Login (Authn)**
Implement user authentication functionality, allowing users to register, log in, and change their passwords using a username and password.

- **Requirements**:
  - **User Registration**:
    - Create a registration form for new users to sign up with a username and password.
    - Store user credentials securely in the database, using hashed passwords.
  - **User Login (HTML Routes)**:
    - Implement a login form where users can authenticate with their username and password.
    - Establish user sessions using cookies for HTML routes upon successful login.
  - **User Login (API Routes)**:
    - Implement JWT-based authentication for API routes, issuing a token upon successful login.
    - Secure API routes by requiring a valid JWT for access.
  - **Change Password**:
    - Provide functionality for users to change their password after logging in.
    - Validate the current password before allowing the change.

## **2. Board Access Control (Authz)**
Restrict board-related actions (create, update, delete) to authenticated users only.
- **Requirements**:
  - **Board Permissions (HTML Routes)**:
    - Ensure only logged-in users with valid session cookies can create, update, or delete boards via HTML routes.
    - Implement authorization checks for each board-related action to verify user authentication.
  - **Board Permissions (API Routes)**:
    - Require a valid JWT for creating, updating, or deleting boards via API routes.
    - Implement JWT-based authorization checks for each board-related action.

## **3. Note Ownership and Visibility (Authz)**
Associate notes with their creator (user) and control visibility based on the note's privacy setting.
- **Requirements**:
  - **Note Ownership**:
    - When a note is created, it should be associated with the logged-in user.
    - Add a privacy setting (public or private) to each note, defaulting to private.
  - **Note Visibility (HTML Routes)**:
    - Public notes should be visible to all users via HTML routes.
    - Private notes should only be visible to the user who created them via session-based authentication.
  - **Note Visibility (API Routes)**:
    - Public notes should be accessible via API routes without authentication.
    - Private notes should require a valid JWT and should only be accessible by the note's owner.


## New Packages to Consider

| **Package Name**                | **Description**                                                                 | **Link**                                                                                       |
|---------------------------------|---------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------|
| `github.com/gorilla/sessions`   | Provides cookie and filesystem-based session storage for Go web applications.    | [gorilla/sessions](https://pkg.go.dev/github.com/gorilla/sessions)                             |
| `github.com/gorilla/securecookie` | Used for encoding and decoding secure cookies, often used with sessions.       | [gorilla/securecookie](https://pkg.go.dev/github.com/gorilla/securecookie)                     |
| `github.com/golang-jwt/jwt`     | A maintained fork of `jwt-go` for working with JWT tokens in Go applications.    | [golang-jwt/jwt](https://pkg.go.dev/github.com/golang-jwt/jwt)                                 |


## New HTML Routes Table

Here’s the updated HTML routes table with the new "Form Params" column:

### New HTML Routes Table

| **Route Name**            | **Method**       | **Route**                  | **Form Params**                               | **Description**                                                  | **Action**                                                                                                      |
|---------------------------|-----------------|----------------------------|-----------------------------------------------|------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| **REGISTER**              | GET             | `/register`                | N/A                                           | Displays the registration form.                                  | The server should return an HTML form for users to create a new account with a username and password.           |
| **REGISTER SUBMIT**       | POST            | `/register`                | `username=<username>`<br>`password=<password>`| Handles the registration form submission.                        | The server should validate the form data, create the new user in the database, and start a session for the user.|
| **LOGIN**                 | GET             | `/login`                   | N/A                                           | Displays the login form.                                         | The server should return an HTML form for users to log in with their username and password.                     |
| **LOGIN SUBMIT**          | POST            | `/login`                   | `username=<username>`<br>`password=<password>`| Handles the login form submission.                               | The server should validate the credentials, log the user in, and establish a session using cookies.             |
| **LOGOUT**                | POST            | `/logout`                  | N/A                                           | Logs out the current user.                                       | The server should terminate the user’s session and redirect to the login page.                                  |
| **CHANGE PASSWORD**       | GET             | `/change-password`         | N/A                                           | Displays the change password form.                               | The server should return an HTML form for users to enter their current and new passwords.                       |
| **CHANGE PASSWORD SUBMIT**| POST            | `/change-password`         | `current_password=<current_password>`<br>`new_password=<new_password>` | Handles the change password form submission.                     | The server should validate the current password, update the password in the database, and confirm the change.   |


## Updated HTML Routes Table

| **Route Name**    | **Method**       | **Route**                  | **Form Params**                                | **Description**                                                  | **Action**                                                                                                      |
|-------------------|-----------------|----------------------------|------------------------------------------------|------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| **CREATE BOARD**  | POST            | `/boards`                  | `name=<name>`                                  | Creates a new board (restricted to authenticated users).         | The server should check the session, validate the request, create the board, and associate it with the user.    |
| **UPDATE BOARD**  | POST            | `/boards?method=PUT`       | `name=<name>`                                  | Updates an existing board (restricted to the board's owner).     | The server should check the session, validate the request, and update the board information.                    |
| **DELETE BOARD**  | POST            | `/boards?method=DELETE`    | N/A                                            | Deletes an existing board (restricted to the board's owner).     | The server should check the session, validate the request, and delete the board if no notes are associated.     |
| **CREATE NOTE**   | POST            | `/notes`                   | `title=<title>`<br>`content=<content>`<br>`board_id=<board_id>` | Creates a new note and associates it with the user.              | The server should validate the request, create the note, associate it with the user, and set the privacy setting.|
| **UPDATE NOTE**   | POST            | `/notes?method=PUT`        | `title=<title>`<br>`content=<content>`<br>`board_id=<board_id>` | Updates an existing note (restricted to the note’s owner).       | The server should check the session, validate the request, and update the note’s content or privacy setting.    |
| **DELETE NOTE**   | POST            | `/notes?method=DELETE`     | N/A                                            | Deletes an existing note (restricted to the note’s owner).       | The server should check the session, validate the request, and delete the note if it belongs to the user.       |


### New HTML Pages Table

| **Page Name**            | **Description**                                                  | **User Actions**                                                                                                      |
|--------------------------|------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------|
| **REGISTER**             | Displays the registration form.                                  | Users can enter a username and password to create a new account.                                                      |
| **LOGIN**                | Displays the login form.                                         | Users can enter their username and password to log in.                                                                |
| **CHANGE PASSWORD**      | Displays the change password form.                               | Users can enter their current password and a new password to update their credentials.                                 |

### Updated HTML Pages Table

| **Page Name**            | **Description**                                                  | **Updated Features**                                                                                                  |
|--------------------------|------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------|
| **CREATE BOARD**         | Displays the form to create a new board.                         | The page should now only be accessible to authenticated users, showing an error if accessed without proper authentication. |
| **UPDATE BOARD**         | Displays the form to update an existing board.                   | The page should now only be accessible to the board's owner                                                           |
| **CREATE NOTE**          | Displays the form to create a new note.                          | The page should allow users to select a board for the note, and set the privacy of the note (public or private).      |
| **UPDATE NOTE**          | Displays the form to update an existing note.                    | The page should show the current privacy setting and board, and allow the user to update these along with the note content. |
| **ALL NOTES (INDEX)**    | Lists all sticky notes in a grid or column layout.               | The page should now distinguish between public notes and private notes visible only to the note's owner.              |
| **SHOW NOTE**            | Displays the details of a single sticky note.                    | The page should indicate whether the note is public or private, and only allow the owner to see private notes.        |


## New API Routes Table

| **Route Name**           | **Method**       | **Route**                           | **JSON Object Type**                       | **Description**                                                  | **Action**                                                                                                      |
|--------------------------|-----------------|-------------------------------------|--------------------------------------------|------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| **LOGIN**                | POST            | `/api/login`                        | `LoginRequest`                             | Handles user login via API.                                       | The server should validate the credentials and return a JWT for authenticated access to API routes.             |
| **LOGOUT**               | POST            | `/api/logout`                       | N/A                                        | Handles user logout via API.                                       | The server should invalidate the JWT and end the session.                                                       |

## Updated API Routes Table

| **Route Name**           | **Method**       | **Route**                           | **JSON Object Type**                       | **Description**                                                  | **Action**                                                                                                      |
|--------------------------|-----------------|-------------------------------------|--------------------------------------------|------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| **GET NOTES**            | GET             | `/api/notes`                        | N/A                                        | Retrieves public notes and notes owned by the authenticated user, if JWT is valid. | The server should validate the JWT if present. If valid, return public notes and notes owned by the user. If the JWT is invalid or not present, return only public notes. |
| **CREATE BOARD**         | POST            | `/api/boards`                       | `BoardRequest`                             | Creates a new board (restricted to authenticated users).          | The server should validate the JWT, create the board, and associate it with the user.                           |
| **UPDATE BOARD**         | PUT             | `/api/boards/{id}`                  | `BoardRequest`                             | Updates an existing board (restricted to the board's owner).      | The server should validate the JWT, and update the specified board.                                             |
| **DELETE BOARD**         | DELETE          | `/api/boards/{id}`                  | N/A                                        | Deletes an existing board (restricted to the board's owner).      | The server should validate the JWT and delete the specified board if no notes are associated.                   |
| **CREATE NOTE**          | POST            | `/api/notes`                        | `NoteRequest`                              | Creates a new note and associates it with the user.               | The server should validate the JWT, create the note, associate it with the user, and set the privacy setting.   |
| **UPDATE NOTE**          | PUT             | `/api/notes/{id}`                   | `NoteRequest`                              | Updates an existing note (restricted to the note’s owner).        | The server should validate the JWT, and update the specified note’s content or privacy setting.                 |
| **DELETE NOTE**          | DELETE          | `/api/notes/{id}`                   | N/A                                        | Deletes an existing note (restricted to the note’s owner).        | The server should validate the JWT and delete the specified note if it belongs to the user.                     |

## JSON Object Types Overview

- **LoginRequest**: Used for login, containing `username` and `password`.
  ```json
  {
    "username": "user123",
    "password": "pass123"
  }
  ```

- **BoardRequest**: Used for creating or updating a board, containing `name`.
  ```json
  {
    "name": "Work"
  }
  ```

- **NoteRequest**: Used for creating or updating a note, containing `title`, `content`, and `board_id`.
  ```json
  {
    "title": "Meeting Notes",
    "content": "Discuss project timeline and deliverables.",
    "board_id": 1
  }
  ```


# Phase 6 - Vulnerability Management

Account for the following vulnerabilities and scenarios.

| **Vulnerability**                         | **Description**                                                                                                                                                          | **Recommended Go Packages/Links**                                                                                          |
|-------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------|
| **SQL Injection (SQLi)**                  | Attackers inject malicious SQL code into queries, compromising databases and exposing or altering sensitive data.                                                         | [sqlx](https://pkg.go.dev/github.com/jmoiron/sqlx) (for prepared statements), [database/sql](https://pkg.go.dev/database/sql) (with parameterized queries)   |
| **Cross-Site Scripting (XSS)**            | Malicious scripts are injected into web pages, stealing user data or performing actions on behalf of the user.                                                            | [html/template](https://pkg.go.dev/html/template) (auto-escapes HTML), [bluemonday](https://pkg.go.dev/github.com/microcosm-cc/bluemonday) (for sanitizing HTML input) |
| **Cross-Site Request Forgery (CSRF)**     | Attackers trick users into performing actions without their knowledge using their authenticated session.                                                                  | [gorilla/csrf](https://pkg.go.dev/github.com/gorilla/csrf) (CSRF protection middleware)                                      |
| **Insecure Direct Object Reference (IDOR)** | Unauthorized access to internal objects, like files or records, due to improper authorization checks.                                                                     | Implement access control checks manually, the above section on authorization should have addressed this. Verify.       |

