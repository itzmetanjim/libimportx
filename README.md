# libimportx 

Call functions written in other languages as if they were the same
language.  **This repository documents the API. For implementations of the
library, see the specific language implementations in
[implementations](#Implementations).**

# API

libimportx-compatible libraries must implement the following:


## `exportx()` function

This function is used to allow other languages to use `importx` on the file.
This should be called after defining all the functions.

### Inputs

No parameters.

### Action

First, check the `LIBIMPORTX` environment variable. If it is not `true`, return
False (the library is not being importx'ed). Read the `LIBIMPORTX_HOST` (this
will be a filepath to a unix socket) and `LIBIMPORTX_TOKEN` environment
variables. Then, create a connection to the socket and send the token then a
newline character and wait for a response. The response should be `OK\n`. If it
isn't, throw an error (token incorrect, should not happen). If it is correct,
recieve a response with no timeout. If the response is an empty string
(connection closed), exit the program. Otherwise, parse the response as JSON
(keep reading until newline) and check the `type` field. It can be one of:
- `read`: The `identifier` field will be the name of the variable or function to
    read. Parse the variable/function as JSON (functions should be represented
    as a dict like so: `{"__function__":"<name>"}`) and send it.
- `set`: Set the variable with the name in the `identifier` field to the value
    in the `value` field. Send back `"OK"\n`
- `call`: Call the function with the name in the `identifier` field with the
    arguments in the `args` and `kwargs` fields, and send back the return value
    as JSON. If there is an error, send a JSON object with the following format:
    ```json
    ERROR{"type":"<error type>","message":"<error message>"}
    ```
    (newline at the end).

It is required to make sure the sent JSON is in one line and add a newline at
the end.

### `importx(filepath)`

This function is used to import a file.

### Inputs

- `filepath`: The path to the file to import.

### Action

Create a unix socket somewhere and generate a random token. Then, run the file
in a subprocess with the following environment variables set:
- `LIBIMPORTX=true`
- `LIBIMPORTX_HOST=<path to the socket>`
- `LIBIMPORTX_TOKEN=<the generated token>`

Once the process connects to the socket and sends the token, check that it is
correct and then send back `OK\n`. Then, return an `ImportxModule` object linked
to the socket to access the functions and variables in the file.

## `ImportxModule` object

This is returned by `importx()` and is used to access the functions and
variables in the file.

### Initialization

When initialized, it should take in a unix socket and store it in a variable. It
should close the socket when the object is destroyed.

### getattr method

When an attribute is accessed, send a `read` request to the socket. If the
response is a function, return a function that sends a `call` request to the
socket with the arguments and keyword arguments. If it is a dictionary, return
an `ImportxNamespace` object with the dictionary as its data. In both
dictionaries and lists, replace all JSON notated functions with actual
functions. For all other types, return the value directly.

### setattr method

When an attribute is set, send a `set` request to the socket.


## `ImportxNamespace` object

This is a simple object similar to a dictionary whose keys can be accessed with
either `.name` or `["name"]` for consistency (you do not want to need to do
`module["function"]()`).

