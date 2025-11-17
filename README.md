# Employee Mobile API – Postman & Configuration Guide

Secure mobile integration for employee login and profile access using JWT and JSON-RPC. Use this guide to configure system parameters, understand security, and test all endpoints from Postman.

---

## 📋 Quick Summary

### 🔒 Security Overview
- JWT tokens signed with a configurable **SECRET KEY**
- Token lifetime controlled by **TOKEN EXP HOURS**
- Integration settings and sensitive employee token fields are visible only to **Integration Admin** group

### 🚀 Postman Flow
1. **Register** employee (one time per employee)
2. **Login** to receive JWT token
3. **Verify** token if needed
4. **Get Profile** using the token
5. **Change Password** (optional)
6. **Logout** to invalidate token

---

## 🌐 Base URL & Headers

Replace `https://your-domain.com` with your actual Odoo server URL.

- **Method**: `POST`
- **Base URL**: `https://your-domain.com/employee/<endpoint>`
- **Content-Type**: `application/json`

### JSON-RPC Envelope

All endpoints use the following JSON-RPC envelope:

```json
{
    "jsonrpc": "2.0",
    "method": "call",
    "params": {
        // put your fields here
    }
}
```

---

## 📍 API Endpoints

### 1. Employee Registration

**Endpoint**: `/employee/register`

One-time registration for an existing employee. The record is matched by `employee_code` + `national_id`. On success, the **employee_pass** is securely hashed and stored.

#### Request Body

```json
{
    "jsonrpc": "2.0",
    "method": "call",
    "params": {
        "employee_code": "EMP0015",
        "employee_pass": "123456",
        "confirm_employee_pass": "123456",
        "national_id": "29901012233445"
    }
}
```

#### Success Response

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "success",
        "message": "Registration Successfully.",
        "employee_id": 15,
        "national_id": "29901012233445"
    }
}
```

#### Possible Error Responses

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "All Fields are Required."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Passwords do Not Match."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Employee Code Not Found With this National ID."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Employee Code Already Registered."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Employee Code Already Terminated."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Employee Code Already Resigned."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Employee Code Not Active."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Unexpected Error During Registration, Details: <error details>"
    }
}
```

---

### 2. Employee Login

**Endpoint**: `/employee/login`

Validates the employee password and returns a signed JWT token. The token is saved in `hr.employee.token_key` with `token_date` for auditing.

#### Request Body

```json
{
    "jsonrpc": "2.0",
    "method": "call",
    "params": {
        "employee_code": "EMP0015",
        "employee_pass": "123456"
    }
}
```

#### Success Response

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "success",
        "message": "Login Successfully.",
        "employee_id": 15,
        "employee_token": "jwt_token_string_here"
    }
}
```

#### Possible Error Responses

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Employee Code and Password Required."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Employee Code Not Found."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Employee Code Not Registered."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Employee Code Already Terminated."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Employee Code Already Resigned."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Employee Code Not Active."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Invalid Password."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Unexpected Error During Login, Details: <error details>"
    }
}
```

---

### 3. Employee Verify

**Endpoint**: `/employee/verify`

Verifies if a JWT token is correctly signed and still valid (not expired).

#### Request Body

```json
{
    "jsonrpc": "2.0",
    "method": "call",
    "params": {
        "employee_token": "jwt_token_string_here"
    }
}
```

#### Success Response

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "success",
        "message": "Valid Token.",
        "employee": "hr.employee(15,)",
        "employee_id": 15,
        "employee_code": "EMP0015",
        "exp_time": "2025-01-05T12:30:00Z"
    }
}
```

#### Possible Error Responses

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Authentication Token is Required."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Expired Token."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Invalid Token."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Employee Not Found."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Employee Not Registered."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Employee Already Terminated."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Employee Already Resigned."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Employee Not Active."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "The Token Doesn't Match With Current Employee Token."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Unexpected Error, Details: <error details>"
    }
}
```

---

### 4. Employee Change Password

**Endpoint**: `/employee/change`

Allows an authenticated employee to change their password. Requires a valid JWT token, the current password, and the new password with confirmation.

#### Request Body

```json
{
    "jsonrpc": "2.0",
    "method": "call",
    "params": {
        "employee_token": "jwt_token_string_here",
        "old_employee_pass": "123456",
        "new_employee_pass": "newpassword123",
        "confirm_employee_pass": "newpassword123"
    }
}
```

#### Success Response

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "success",
        "message": "Change Successfully."
    }
}
```

#### Possible Error Responses

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Authentication Token is Required."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "All Fields are Required."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Passwords do Not Match."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Old and New Passwords are Same."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Invalid Old Password."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Expired Token."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Invalid Token."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Employee Not Found."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Employee Not Registered."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Employee Already Terminated."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Employee Already Resigned."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Employee Not Active."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "The Token Doesn't Match With Current Employee Token."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Unexpected Error, Details: <error details>"
    }
}
```

---

### 5. Employee Logout

**Endpoint**: `/employee/logout`

Logs out the employee by invalidating the stored JWT token (`token_key` and `token_date` are cleared).

#### Request Body

```json
{
    "jsonrpc": "2.0",
    "method": "call",
    "params": {
        "employee_token": "jwt_token_string_here"
    }
}
```

#### Success Response

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "success",
        "message": "Logout Successfully."
    }
}
```

#### Possible Error Responses

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Authentication Token is Required."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Expired Token."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Invalid Token."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Employee Not Found."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Employee Not Registered."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Employee Already Terminated."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Employee Already Resigned."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Employee Not Active."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "The Token Doesn't Match With Current Employee Token."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Unexpected Error, Details: <error details>"
    }
}
```

---

### 6. Employee Profile

**Endpoint**: `/employee/profile`

Returns the employee profile associated with the token. The token must come from `/employee/login`.

#### Request Body

```json
{
    "jsonrpc": "2.0",
    "method": "call",
    "params": {
        "employee_token": "jwt_token_string_here"
    }
}
```

#### Success Response Example

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "success",
        "message": "Employee Profile Retrieved Successfully",
        "employee_general_information": {
            "preferred_name": "Administrator",
            "employee_Code": "MP0015",
            "employee_url_photo": "https://example.com/web/image/hr.employee/1/image_1920"
        },
        "employee_personal_information": {
            "maritalStatus": "Married",
            "currentDetailedAddressArabic": "العنوان الحالي بالتفصيل",
            "nationalIDDetailedAddressArabic": "عنوان البطاقة الوطنية",
            "personalMobile": "01234567890",
            "businessMobile": "01234567891",
            "militaryStatus": "Completed",
            "emergencyContactName": "John Doe",
            "emergencyContactMobile": "01234567892",
            "emergencyContactRelation": "Brother",
            "numberOfWives": 1,
            "wives": [
                {
                    "wife": "1",
                    "name": "Jane Smith",
                    "si_status": "Yes",
                    "national_id": "29801012233446"
                }
            ],
            "numberOfChildren": 2,
            "children": [
                {
                    "child": "1",
                    "name": "Child One",
                    "gender": "Male",
                    "si_status": "Yes",
                    "national_id": "31501012233447"
                },
                {
                    "child": "2",
                    "name": "Child Two",
                    "gender": "Female",
                    "si_status": "No",
                    "national_id": "31701012233448"
                }
            ]
        }
    }
}
```

#### Possible Error Responses

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Authentication Token is Required."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Expired Token."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Invalid Token."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Employee Not Found."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Employee Not Registered."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Employee Already Terminated."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Employee Already Resigned."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Employee Not Active."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "The Token Doesn't Match With Current Employee Token."
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "failed",
        "message": "Unexpected Error, Details: <error details>"
    }
}
```

---

## ⚙️ Configuration & Security (Integration Admin Only)

All configuration and internal token fields are protected. Only users in group **Integration Admin** can see and edit them.

### Settings → Integration Parameters

In **Settings → Employees** a block is added with two fields:

- **SECRET KEY** – Used to sign/verify JWT tokens. Changing it will immediately invalidate all existing tokens.
- **TOKEN EXP HOURS** – Controls for how many hours a token remains valid (e.g., `24` hours).

### Employee Form → "Mobile Integration" Tab

The employee form includes an additional tab to inspect the mobile integration state. This tab is also restricted to **Integration Admin**.

- **Mobile Active** – Indicates whether the employee has completed mobile registration. If disabled, the employee is considered not registered and cannot log into the mobile application.
- **Employee Code** – A unique identifier assigned to the employee and used as the primary reference for authentication through the API.
- **Employee Password** – A securely hashed credential stored for mobile login. The system never saves the plain-text password.
- **Token Key** – The most recently issued JWT access token associated with the employee. This value is updated each time the employee logs in.
- **Token Date** – The UTC timestamp indicating when the current access token was created. Useful for tracking token validity and activity.

> **Note**: With this setup, developers and admins in the Integration Admin group can fully control and audit the JWT behavior, while regular users and HR officers see none of the sensitive token details.

---

## 🧪 Testing with Postman

1. **Import Collection**: Create a new Postman collection with the endpoints listed above
2. **Set Environment Variables**:
   - `base_url`: Your Odoo instance URL
   - `employee_token`: Store the token from login response
3. **Test Flow**:
   - Register a new employee
   - Login to get the token
   - Use the token to verify, get profile, change password, or logout

---

## 🔐 Security Best Practices

- Store the **SECRET KEY** securely and never commit it to version control
- Use HTTPS in production to protect tokens in transit
- Set appropriate **TOKEN EXP HOURS** based on your security requirements
- Regularly audit token usage through the **Token Date** field
- Implement rate limiting on authentication endpoints
- Monitor failed login attempts
