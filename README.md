# Node.js Microservices: JWT Authentication System Documentation

Welcome to the documentation for the Node.js Microservices JWT Authentication System. This system provides a secure and scalable authentication mechanism for your microservices architecture using JSON Web Tokens (JWT).

For the most up-to-date information, and detailed instructions and for a better view, please refer to the [README](https://github.com/AryanBarsaiyan/Microservices-JWT-Authentication-System-Documentation).

## Overview

The goal of this project is to implement a JWT (JSON Web Token) authentication system using Node.js, comprising two microservices:

1. **Main Service (User Server):** Handles user authentication and authorization using JWT.
2. **Public API Microservice:** Offers a public API key for accessing routes without requiring login credentials.

## Installation

Before starting, ensure you have Node.js and npm installed.

### Node.js Installation

If Node.js is not installed on your system, follow these steps:

1. **Download Node.js:**
   - Visit the official Node.js website: [Node.js Downloads](https://nodejs.org/)
   - Download the recommended version for your operating system.

2. **Install Node.js:**
   - Follow the installation instructions for your operating system provided on the Node.js website.

3. **Verify Installation:**
   - Open a terminal or command prompt.
   - Run the following commands to check if Node.js and npm are installed:
     ```bash
     node -v
     npm -v
     ```
     If installed correctly, these commands will display the versions of Node.js and npm.

### Project Setup Using GitHub

If you prefer using GitHub to set up the project, follow these steps:

1. **Clone the Repository:**
   ```bash
   git clone https://github.com/AryanBarsaiyan/nodejs-microservices-jwt-authentication.git
   ```

2. **Navigate to the Project Directory:**
   ```bash
   cd nodejs-microservices-jwt-authentication
   ```

3. **Install Dependencies:**
   ```bash
   npm install
   ```

4. **Create a `.env` file:**
   - In the root directory, create a file named `.env`.
   - Add the following environment variables to the `.env` file:
     ```env
     # Gateway
     MONGO_URI=MongoDB_URI
     GATEWAY_PORT=5000
     NODE_ENV=development
     # User
     USER_APP_URL=http://localhost:5001
     JWT_SECRET_KEY=secret
     USER_APP_PORT=5001
     # Public
     PUBLIC_APP_URL=http://localhost:5002
     PUBLIC_APP_PORT=5002
     ```

5. **Run the Project:**
   ```bash
   npm start
   ```

### Project Setup Using Zip File

If you prefer using a Zip file to set up the project, follow these steps:

1. **Extract the Zip File:**
   - Extract the contents of the Zip file to your desired project location.

2. **Navigate to the Project Directory:**
   ```bash
   cd path/to/extracted/folder/nodejs-microservices-jwt-authentication
   ```

3. **Install Dependencies:**
   ```bash
   npm install
   ```
   
4. **Run the Project:**
   ```bash
   npm start
   ```

### API Endpoints

For using the API endpoints, you can use Postman or any other API testing tool of your choice. The base URL for the API is `http://localhost:5000`.

## Database Design

### 1. ApiKey Model

```json
{
  "apiKey": {
    "type": "String",
    "required": true,
    "unique": true
  },
  "userId": {
    "type": "mongoose.Schema.Types.ObjectId",
    "ref": "User"
  }
}
```

**Explanation:**
- Represents the API key associated with a user.
- Each API key is unique and linked to a specific user through the `userId` field.

### 2. Candidate Model

```json
{
  "firstName": {
    "type": "String",
    "required": true
  },
  "lastName": {
    "type": "String",
    "required": true
  },
  "email": {
    "type": "String",
    "required": true
  },
  "userId": {
    "type": "mongoose.Schema.Types.ObjectId",
    "ref": "User"
  }
}
```

**Explanation:**
- Represents the details of a candidate.
- Each candidate is associated with a user through the `userId` field.

### 3. User Model

```json
{
  "firstName": {
    "type": "String",
    "required": true
  },
  "lastName": {
    "type": "String",
    "required": true
  },
  "email": {
    "type": "String",
    "required": true,
    "unique": true
  },
  "password": {
    "type": "String",
    "required": true,
    "minlength": 8
  },
  "apiKey": {
    "type": "String",
    "required": true,
    "unique": true
  },
  "candidates": [{
    "type": "mongoose.Schema.Types.ObjectId",
    "ref": "Candidate"
  }]
}
```

**Explanation:**
- Represents the user of the system.
- Each user has a unique email and password for authentication.
- An API key is generated for each user to access the public API.
- The `candidates` array contains references to the candidates associated with the user.


## Project Components

### Main Service (User Server)

#### UserApp.js

##### User Authentication using JWT

- **Endpoint 1:** `/api/user/register`
  - **Method:** POST
    - **Description:** Registers the user.
    - **Request:**
      - Body:
        ```json
        {
          "firstName": "User's First Name",
          "lastName": "User's Last Name",
          "email": "user@example.com",
          "password": "user_password"
        }
        ```
    - **Response:**
        ```json
        {
           "success": "User created successfully",
           "userCreated": {
           "_id": "object id",
           "firstName": "User's First Name",
           "lastName": "User's Last Name",
            "email": "user@example.com",
            "apiKey": "user_api_key"
            }
        }
        ```
      - Status 200
      - JSON object with a success message and the created user's details.

![Register](https://github.com/AryanBarsaiyan/nodejs-microservices-jwt-authentication/assets/82722871/fccdd872-61e9-4d6b-baca-93ae6792ed20)


- **Endpoint 2:** `/api/user/login`
  - **Method:** POST
    - **Description:** Logs in the user.
    - **Request:**
      - Body:
        ```json
        {
            "email": "user@example.com",
            "password": "user_password"
        }
        ```
    - **Response:**
      ```json
        {
          "success": "user logged in",
          "userLoggedIn": {
              "_id": "user_object_id",
              "name": "User's First Name",
              "lastName": "User's Last Name",
              "email": "user@example.com",
              "apiKey": "user_api_key"
          }
        }
      ```
![Login](https://github.com/AryanBarsaiyan/nodejs-microservices-jwt-authentication/assets/82722871/1dff4140-1d98-4e0e-9e6c-cf91c3aa3d0d)

![jwt token](https://github.com/AryanBarsaiyan/nodejs-microservices-jwt-authentication/assets/82722871/a155d69b-c355-4dec-929e-a8a9b5804b2a)


- **Endpoint 3:** `/api/user/candidate`
  - **Method:** 
    - `POST`: Add a new candidate (protected route)
      - **Description:** Adds a new candidate to the database corresponding to the user logged in.
      - Requires a valid JWT token in the Authorization header.
      - Body:
      ```json
        {
          "firstName": "Candidate's First Name",
          "lastName": "Candidate's Last Name",
          "email": "candidate@example.com"
        }
      ```
      - Response:
      ```json
        {
          "success": "Candidate created successfully",
          "candidateCreated": {
              "_id": "candidate_object_id",
              "firstName": "Candidate's First Name",
              "lastName": "Candidate's Last Name",
              "email": "candidate@example.com"
          }
        }
      ```
    - `GET`: Retrieve candidates (protected route)
      - **Description:** Retrieves candidates for whom the current user is the owner.
      - Requires a valid JWT token in the Authorization header.
      - Response:
      ```json
        {
          "success": "Candidates fetched successfully",
          "userCandidates": [
              {
                  "firstName": "Candidate1 First Name",
                  "lastName": "Candidate1 Last Name",
                  "email": "candidate1@example.com"
              },
              {
                  "firstName": "Candidate2 First Name",
                  "lastName": "Candidate2 Last Name",
                  "email": "candidate2@example.com"
              },
              {
                  "firstName": "Candidate3 First Name",
                  "lastName": "Candidate3 Last Name",
                  "email": "candidate3@example.com"
              }
          ]
        }
      ```
##### Notes:
  - The POST endpoint adds a new candidate to the database. It requires a valid JWT token in the Authorization header for authentication.
  - The GET endpoint retrieves candidates created by the logged-in user. It also requires a valid JWT token in the Authorization header.
  - The response includes a success message and the details of the candidate(s) for the respective operation.
    
![createCandidate](https://github.com/AryanBarsaiyan/nodejs-microservices-jwt-authentication/assets/82722871/37c6582c-92d3-4993-a075-cf6012839442)

![getCandidate](https://github.com/AryanBarsaiyan/nodejs-microservices-jwt-authentication/assets/82722871/f0e81376-03dd-42f9-9844-ce685e0f268a)


- **Endpoint 4:** `/api/user/logout`
    - **Method:** 
        - `GET`: Logout (protected route)
        - **Description:** Logs out the user.
        - Requires a valid JWT token in the Authorization header.
        - Response:
            - "Access token cleared"
              
![logout](https://github.com/AryanBarsaiyan/nodejs-microservices-jwt-authentication/assets/82722871/5965d9fa-289d-4195-959a-49793ff12a11)

### Public API Microservice (Public ApiKey Server)

#### PublicApp.js

##### Public API Endpoints

- **Endpoint 1:** `/api/public/profile`
  - **Method:** POST
    - **Description:** Retrieves the profile information in JSON format of the user corresponding to whose API key is being used.
    - **Functionality:** This endpoint allows external applications to fetch the profile information of a user by providing the user's API key.
    - Body:
      ```json
      {
        "apiKey": "user_api_key"
      }
      ```
    - Response:
      ```json
      {
        "success": "User profile fetched successfully",
        "userProfile": {
            "_id": "user_object_id",
            "firstName": "User's First Name",
            "lastName": "User's Last Name",
            "email": "User's Email",
            "apiKey": "user_api_key"
        }
      }
      ```
      
![getUserProfile](https://github.com/AryanBarsaiyan/nodejs-microservices-jwt-authentication/assets/82722871/49e85c21-ada8-4468-982a-55e66bef80ee)

- **Endpoint 2:** `/api/public/candidate`
  - **Method:** GET
    - **Description:** Retrieves all candidates respective to the user whose API key is being used.
    - **Functionality:** External applications can retrieve a list of candidates associated with a specific user by providing the user's API key.
    - Query Parameters:
      - `apiKey`: User's API key
    - Response:
      ```json
      {
        "success": "Candidates fetched successfully",
        "userCandidates": [
            {
                "firstName": "Candidate1 First Name",
                "lastName": "Candidate1 Last Name",
                "email": "Candidate1 Email"
            },
            {
                "firstName": "Candidate2 First Name",
                "lastName": "Candidate2 Last Name",
                "email": "Candidate2 Email"
            },
            {
                "firstName": "Candidate3 First Name",
                "lastName": "Candidate3 Last Name",
                "email": "Candidate3 Email"
            }
        ]
      }
      ```
      
![getListOfCandidates](https://github.com/AryanBarsaiyan/nodejs-microservices-jwt-authentication/assets/82722871/f22b58ca-b8f2-48ef-bc3f-dea110d99066)


## Connection Between Main Service and Public API Microservice

### Gateway (`gate_way_server/index.js`)

- **Functionality:**
  - Efficiently manages routing and communication between the main service and public API microservice.
  - Serves as a crucial intermediary for requests and responses, ensuring a seamless flow of data between microservices.
  - Implements robust authorization checks before forwarding requests to the public API microservice, enhancing system security.
  - Utilizes proxy middleware to transparently direct requests to the appropriate microservices.

- **Proxy Middleware for Forwarding Requests:**
  ```javascript
  // Proxy middleware for forwarding requests
  const userAppProxy = createProxyMiddleware('/api/', {
    target: `${process.env.USER_APP_URL}`,
    changeOrigin: true,
  });

  const publicAppProxy = createProxyMiddleware('/api/public', {
    target: `${process.env.PUBLIC_APP_URL}`,
    changeOrigin: true,
  });

  // Apply proxy middleware to specific routes
  app.use('/api/public', publicAppProxy);
  app.use('/api/user', userAppProxy);
  ```
  - `userAppProxy`: Directs requests with paths starting with `/api/` to the User Service at `${process.env.USER_APP_URL}`.
  - `publicAppProxy`: Channels requests with paths starting with `/api/public` to the Public API Service at `${process.env.PUBLIC_APP_URL}`.

This mechanism ensures efficient communication between microservices, optimizing request handling and enhancing the overall system's reliability. The use of proxy middleware enables a clean separation of concerns, allowing the Gateway to manage and route requests effectively.

## Conclusion

This comprehensive documentation provides an overview of the JWT authentication system project, detailing the main components, their functionalities, and the connection between the main service and the public API microservice through the gateway. Additionally, it covers the MongoDB database design, describing the schema for the ApiKey, Candidate, and User models. The outlined structure serves as a guide for implementing and understanding the different parts of the project.
