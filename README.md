# AutoDoorCtrlWebAPI
AutoDoorCtrlWebAPI is the API we use to connect our angular web app to our user database and authentication system. The API is built with node.js and MySQL server. The repository for the website can be found [Here](https://github.com/AutomaticDoorControl/AutoDoorCtrlWeb)

## What to install
  * clone respository `https://github.com/AutomaticDoorControl/AutoDoorCtrlWebAPI.git`
  * navigate to AutoDoorCtrlWebAPI on your machine
  * `npm install`
  * run `node Server.js.js` to start the API
  * API should be running on `localhost:8080`
  * NOTE: This API will not work without the use of a properly setup MySQL database. Point the API to the db by changing `connection` in `Server.js.js`

## API Calls
Users are JSON objects in the form `{"Status": "Active|Request", "RCSid": <RCSid>}`

Admins are JSON objects in the form `{"username": <username>, "password": <bcrypted password>}`

Requests with a star (\*) require admin authentication, otherwise they will return a `401 Unauthorized` error. Authentication is handled by sending the header `Authorization: Bearer <JWT>` as part of the request, where `<JWT>` is the Json Web Token recieved during login. User JWTs are not valid for admin authentication.

### GET requests
* /api/active_user \*
    * Returns an array of all Users where Status is `Active`
* /api/inactive_user \*
    * Returns an array of all Users where Status is `Request`
* /api/addAll \*
    * Changes all Users' Status to `Active`
    * Returns a throwaway value
* /api/get-complaints \*
    * Returns an array of JSON items in the form `{"location": <location>, "message": <message>}`
* /api/get-doors
    * Returns an array of JSON items in the form `{"name": <name>, "location": <location>, "latitude": <latitude>, "longitude": <longitude>}`

### POST requests
* /api/login
    * Supply a JSON object in the form `{"RCSid": <RCSid>}`
    * Returns a JSON object in the form `{"SESSIONID": <JWT>}` where `<JWT>` is a signed JSON web token with `sub` field matching `<RCSID>`
    * If `<RCSid>` does not represent a valid active user, `<JWT>` will be an empty string `""`
* /api/admin/login
    * Supply a JSON object in the form `{"username": <username>, "password": <password>}`
    * Returns a JSON object in the form `{"SESSIONID": <JWT>}` where `<JWT>` is a signed JSON web token with `sub` field matching `<username>`
    * If `<username>` and `<password>` do not together represent a valid active user, `<JWT>` will be an empty string `""`
* /api/request-access
    * Supply a JSON object in the form `{"RCSid": <RCSid>}`
    * Adds a row to Users with the values `{"Status": Request, "RCSid": <RCSid>}`
    * Returns a throwaway value
* /api/addtoActive \*
    * Supply a JSON object in the form `{"RCSid": <RCSid>}`
    * Changes the status of User with RCSid `<RCSid>` to `Active`
    * Returns a throwaway value
* /api/remove \*
    * Supply a JSON object in the form `{"RCSid": <RCSid>}`
    * Deletes all Users with RCSid `<RCSid>`
    * Returns a throwaway value
* /api/submit-complaint
    * Supply a JSON object in the form `{"location": <location>, "message": <message>}`
    * Stores the complaint in the server
    * Returns a throwaway value
