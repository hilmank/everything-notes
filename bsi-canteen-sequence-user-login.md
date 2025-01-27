# **User Login - Sequence Diagram & Documentation**

## **1. Overview**
This document provides a detailed description of the user login process, including both traditional login with credentials and RFID-based login. It includes UML sequence diagrams, API endpoint details, process flow explanations, and security considerations to ensure secure authentication.

## **2. UML Sequence Diagram**

### **2.1. Diagram Representation User Login**
The sequence diagram below illustrates the standard user login flow using credentials (username/email and password):

**Actors:**
1. **User** - Enters credentials (username/email and password) in the login form.
2. **Client Application (WEB Application)** - Sends login request to API.
3. **API (BSI.Canteen)** - Processes user login.
4. **Database (PostgreSQL)** - Verifies user credentials.

**Process:**
1. The user inputs their credentials into the login form.
2. The client application sends the credentials to the API server (`POST /api/auth/login`).
3. The API server validates the credentials against the database:
   - Validate that username/email and password are required.
   - Validate that only active users can log in.
   - If invalid, send an error to the client.
4. If valid, the server generates an authentication token and sends it to the client.
5. If invalid, the server sends an error response (`401 Unauthorized`).
6. The client stores the token for subsequent API requests.

### **2.2. Diagram Representation RFID Login**
The sequence diagram below illustrates the RFID login process for users (used for the ordering process):

**Actors:**
1. **User** - Scans their RFID card.
2. **RFID Reader** - Captures and transmits RFID data.
3. **Client Application (Touchscreen Device)** - Sends RFID authentication request to API.
4. **API (BSI.Canteen)** - Processes RFID login.
5. **Database (PostgreSQL)** - Verifies RFID credentials.

**Process:**
1. The user scans their RFID card using the reader.
2. The RFID reader sends the scanned data to the client application.
3. The client application forwards the RFID data to the API server (`POST /api/auth/rfid-login`).
4. The API server validates the user by RFID:
   - Validate that the RFID card ID is required. If not valid, send an error to the client.
   - Validate that only active users can order.
   - Validate that a user can only place one order per day.
   - If not valid, send an error to the client.
5. The API server verifies the RFID data against the database.
6. If valid, the server generates an authentication response.
7. If invalid, the server sends an error response (`401 Unauthorized`).
8. The client grants access to the user based on the authentication response.

## **3. API Endpoint Details**

❌ **Error Responses**

| Status Code                  | Message                         |
| ---------------------------- | -------------------------------- |
| **401 Unauthorized**         | `{ "message": "Unauthorized request." }` |
| **400 Bad Request**         | `{ "message": "Invalid input parameters." }` |
| **403 Forbidden**           | `{ "message": "Access denied." }` |
| **404 Not Found**           | `{ "message": "Resource not found." }` |
| **500 Internal Server Error** | `{ "message": "An unexpected error occurred." }` |

### **3.1. User Login API**

#### **Endpoint:**
```
POST /api/auth/login
```

#### **Request Body:**
```json
{
  "username": "john_doe",
  "password": "P@ssw0rd!"
}
```

#### **Response Examples:**
**Success (200 OK)**
```json
{
  "token": "eyJhbGciOiJIUzI1NiIs...",
  "expires_in": 3600,
  "user": {
    "userId": 123,
    "username": "john_doe",
    "firstName": "John",
    "lastName": "Doe",
    "email": "john.doe@example.com",
    "roles": [
      { "roleId": 1, "roleName": "Employee" }
    ],
    "company": {
      "companyId": 1,
      "companyName": "ABC Corp"
    },
    "rfidCard": {
      "rfidCardId": "1234567890",
      "cardNumber": "RF12345",
      "assigned": true,
      "assignedAt": "2025-01-27T10:00:00Z"
    }
  }
}
```

**Invalid Credentials (401 Unauthorized)**
```json
{
  "error": "Invalid username or password"
}
```

### **3.2. RFID Login API**

#### **Endpoint:**
```
POST /api/auth/rfid-login
```

#### **Request Body:**
```json
{
  "rfid": "1234567890"
}
```

#### **Response Examples:**
**Success (200 OK)**
```json
{
  "token": "eyJhbGciOiJIUzI1NiIs...",
  "user": {
    "userId": 123,
    "username": "john_doe",
    "firstName": "John",
    "lastName": "Doe",
    "email": "john.doe@example.com",
    "roles": [
      { "roleId": 1, "roleName": "Employee" }
    ],
    "company": {
      "companyId": 1,
      "companyName": "ABC Corp"
    },
    "rfidCard": {
      "rfidCardId": "1234567890",
      "cardNumber": "RF12345",
      "assigned": true,
      "assignedAt": "2025-01-27T10:00:00Z"
    }
  }
}
```

**Invalid RFID (401 Unauthorized)**
```json
{
  "error": "RFID not recognized"
}
```

## **4. Process Flow Description**

### **User Login Process**

| **Step** | **Actor** | **Action** |
| -------- | -------- | ---------------------------------------------------------------- |
| **1** | **User** | Enters credentials in the login form. |
| **2** | **Client Application (WEB Application)** | Sends login request to API. |
| **3** | **API (BSI.Canteen)** | Validates credentials and checks user status. |
| **4** | **API (BSI.Canteen)** | If valid, generates a JWT token and sends it to the client. |
| **5** | **Client Application (WEB Application)** | Stores token for authenticated requests. |

### **RFID Login Process**

| **Step** | **Actor** | **Action** |
| -------- | -------- | ---------------------------------------------------------------- |
| **1** | **User** | Scans their RFID card. |
| **2** | **Client Application (Touchscreen Device)** | Sends RFID data to API. |
| **3** | **API (BSI.Canteen)** | Validates RFID and user status. |
| **4** | **API (BSI.Canteen)** | If valid, grants access. |


## **5. Security Considerations**
- **Password Hashing:** All user passwords are stored securely using PBKDF2 encryption.
- **Token Security:** JWT tokens are signed and expire after a predefined duration.
- **RFID Validation:** RFID authentication is restricted to registered users only.
- **Rate Limiting:** Prevents brute-force attacks by limiting failed login attempts.
- **HTTPS Enforcement:** Ensures encrypted communication between client and server.

## **6. Conclusion**
This document outlines the authentication mechanisms available in the system, detailing both traditional credential-based login and RFID login. By implementing strong security measures, we ensure the authentication process remains robust and reliable.

