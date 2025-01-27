# **User Registration - Sequence Diagram & Documentation**

## **1. Overview**

This document provides a **UML sequence diagram** and a step-by-step explanation of the **User registration process** in the **BSI.Canteen** system.

---

## **2. UML Sequence Diagram**

### **2.1. Diagram Representation**

**Actors:**

1. **Admin GA** - Initiates registration by scanning RFID and entering details.
2. **WEB Application** - Sends registration request to API.
3. **API (BSI.Canteen)** - Processes user registration.
4. **Database (PostgreSQL)** - Stores user data.

**Process Flow:**

1. User enters details and scans RFID.
2. WEB Application sends `POST /api/users/register` request with user info and RFID.
3. API checks if RFID is already registered in the database.
   - If **RFID exists**, API returns `409 Conflict`.
   - If **RFID does not exist**, API proceeds with registration.
4. API checks if **email is already registered**.
   - If **email exists**, API returns `409 Conflict`.
   - If **email does not exist**, API proceeds with registration.
5. API checks:
   - If **employee ID is entered**, verify if it exists in the system.
     - If **employee ID exists**, API returns `409 Conflict`.
     - If **employee ID does not exist**, API proceeds with registration.
   - If **employee ID is not entered**, ensure **first name, last name, address, and company name (if applicable) are filled**.
6. API generates a **unique username** and **encrypted password**.
7. API inserts new user data into the database.
8. Database confirms user creation.
9. API sends `200 Created` response with user details.
10. WEB Application displays registration success message.

---

## **3. API Endpoint Details**

### **3.1. Register New User API**

- **Method:** `POST`
- **URL:** `/api/users/register`
- **Headers:**
  - `Content-Type: application/json`
  - `Authorization: Bearer <token>`

#### **Request Body**

**Note:** Role ID can have multiple values and Employee ID should be retrieved from `GET api/master/roles` and `api/master/companies`. The Web Application should display these values in a combo box.

```json
{
  "roleIds": [1, 2],
  "firstName": "John",
  "middleName": "M",
  "lastName": "Doe",
  "email": "johndoe@example.com",
  "phoneNumber": "1234567890",
  "address": "123 Main St, City, Country",
  "companyId": 1, // Retrieved from GET api/master/roles
  "employeeId": "EMP12345", // Retrieved from GET api/master/companies
  "guestCompany": "ABC Corp",
  "rfidCardNumber": "A1B2C3D4E5F6",
  "isActive": true
}
```

#### **Response Examples**

✅ **Success (200 Created)**

```json
{
  "userId": 12345,
  "username": "jdoe",
  "password": "encrypted_password",
  "firstName": "John",
  "middleName": "M",
  "lastName": "Doe",
  "email": "johndoe@example.com",
  "phoneNumber": "1234567890",
  "address": "123 Main St, City, Country",
  "companyId": 1,
  "employeeId": "EMP12345",
  "guestCompany": "ABC Corp",
  "rfidCardId": "A1B2C3D4E5F6",
  "linkedEmployeeId": null,
  "isActive": true,
  "createdAt": "2024-01-01T12:00:00Z",
  "createdBy": 1001,
  "updatedAt": "2024-01-02T14:00:00Z",
  "updatedBy": 1002,
  "fullName": "John M Doe",
  "roles": [
    {
      "roleId": 1,
      "roleName": "Admin"
    }
  ],
  "company": {
    "companyId": 1,
    "companyName": "ABC Corp"
  },
  "rfidCard": {
    "rfidCardId": "A1B2C3D4E5F6",
    "assignedTo": 12345
  }
}
```

❌ **Error Responses**

| Status Code                  | Message                                                                                                                                |
| ---------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| **401 Unauthorized**         | `{ "message": "Unauthorized request." }`                                                                                               |
| **409 Conflict**             | `{ "message": "RFID or email already registered." }`                                                                                   |
| **400 Bad Request**          | `{ "message": "Invalid request format." }`                                                                                             |
| **422 Unprocessable Entity** | `{ "message": "Invalid email, missing required fields like first name, last name, address, or company name if employee ID is null." }` |

---

## **4. Process Flow Description**

| **Step** | **Actor**                 | **Action**                                                                                                                                                                                        |
| -------- | ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **1**    | **Admin GA**                  | Enters personal details (First Name, Middle Name, Last Name, Email, Phone Number, Address, Employee ID, RFID Card Number, Company ID or Guest Company, and Active Status) in the WEB Application. |
| **2**    | **WEB Application**       | Sends a `POST` request to the API with the user details.                                                                                                                                          |
| **3**    | **API (BSI.Canteen)**     | Queries the **Database** to check if the RFID tag is already registered.                                                                                                                          |
| **4**    | **Database (PostgreSQL)** | Searches for the RFID tag in the `Users` table.                                                                                                                                                   |
| **5**    | **Database** → **API**    | If the RFID **is not found**, proceed with registration.                                                                                                                                          |
| **6**    | **API**                   | Validates **email, phone number, and employee ID** for correctness.                                                                                                                               |
| **7**    | **API**                   | Checks if **email is already registered**.                                                                                                                                                        |
| **8**    | **API**                   | Checks:                                                                                                                                                                                           |

- If **employee ID is entered**, verify if it exists in the system.
- If **employee ID is not entered**, ensure **first name, last name, address, and company name are filled**. |
  \| **9** | **API** | Generates a **unique username** and **encrypts password** before storing. |
  \| **10** | **API** | Inserts the **new user data** into the database. |
  \| **11** | **Database** | Confirms that the **user has been successfully created**. |
  \| **12** | **API** → **WEB Application** | Sends a **201 Created** response with the user’s ID, username, and details. |
  \| **13** | **WEB Application** | Displays **registration success** message to the user. |

---

## **5. Security Considerations**

✅ **Prevent duplicate RFID and email registration** (`409 Conflict`).\
✅ **Encrypt RFID card number and password** before storing in the database.\
✅ **Validate email, phone number, and employee ID** to prevent incorrect user registrations.\
✅ **Ensure first name, last name, address, and company name are required if employee ID is null**.\
✅ **Use hashing (e.g., PBKDF2) for password security**.

---

## **6. Conclusion**

This document outlines the **User registration process**, including API specifications, sequence flow, and security considerations. The attached **UML sequence diagram** visually represents the registration workflow for developers.
