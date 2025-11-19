# Mobile Integration APIs – Postman Guide

Secure Mobile Integration for Employee Using JWT Tokens and JSON‑RPC Protocol..

---

### 1. Base URL & Headers

Replace `https://your-domain.com` with your actual Odoo server URL.

<div style="display:flex; flex-wrap:wrap; gap:16px; font-size:13px;">

<div>

<span style="display:inline-block; padding:2px 8px; border-radius:999px; background:#e3f2fd; color:#1565c0;">
POST </span>
<span style="margin-left:8px;">`https://your-domain.com/employee/<endpoint>`</span>

</div>

<div>

<span style="display:inline-block; padding:2px 8px; border-radius:999px; background:#e8f5e9; color:#2e7d32;">
Content-Type </span>
<span style="margin-left:8px;">`application/json`</span>

</div>

</div>

**JSON‑RPC envelope (for all endpoints):**

```json
{
    "jsonrpc": "2.0",
    "method": "call",
    "params": {
        // put your fields here
    }
}
                
```

</div>

</div>

</div>

<div class="oe_row oe_spaced">

<div class="oe_span12">

<div style="border:1px solid #ddd; border-radius:8px; padding:16px;">

### 2. Employee Registration

URL: `/employee/register`

One‑time registration for an existing employee. The record is matched by
`employee_code` + `national_id`. On success, the **employee_pass** is
securely hashed and stored.

#### Request – Postman Body (raw → JSON)

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

#### Failure Responses (inside `result`)

```json
{
    "status": "failed",
    "message": "All Fields are Required."
}

{
    "status": "failed",
    "message": "Passwords do Not Match."
}

{
    "status": "failed",
    "message": "Employee Code Not Found With this National ID."
}

{
    "status": "failed",
    "message": "Employee Code Already Registered."
}

{
    "status": "failed",
    "message": "Employee Code Already Terminated."
}

{
    "status": "failed",
    "message": "Employee Code Already Resigned."
}

{
    "status": "failed",
    "message": "Employee Code Not Active."
}

{
    "status": "failed",
    "message": "Unexpected Error During Registration, Details: <error details>"
}
                
```

</div>

</div>

</div>

<div class="oe_row oe_spaced">

<div class="oe_span12">

<div style="border:1px solid #ddd; border-radius:8px; padding:16px;">

### 3. Employee Login

URL: `/employee/login`

Validates the employee password and returns a signed JWT token. The
token is saved in `hr.employee.token_key` with `token_date` for
auditing.

#### Request – Postman Body (raw → JSON)

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

#### Failure Responses (inside `result`)

```json
{
    "status": "failed",
    "message": "Employee Code and Password Required."
}

{
    "status": "failed",
    "message": "Employee Code Not Found."
}

{
    "status": "failed",
    "message": "Employee Code Not Registered."
}

{
    "status": "failed",
    "message": "Employee Code Already Terminated."
}

{
    "status": "failed",
    "message": "Employee Code Already Resigned."
}

{
    "status": "failed",
    "message": "Employee Code Not Active."
}

{
    "status": "failed",
    "message": "Invalid Password."
}

{
    "status": "failed",
    "message": "Unexpected Error During Login, Details: <error details>"
}
                
```

</div>

</div>

</div>

<div class="oe_row oe_spaced">

<div class="oe_span12">

<div style="border:1px solid #ddd; border-radius:8px; padding:16px;">

### 4. Employee Verify

URL: `/employee/verify`

Verifies if a JWT token is correctly signed and still valid (not
expired).

#### Request – Postman Body (raw → JSON)

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
        "employee_id": 15,
        "employee_code": "EMP0015",
        "exp_time": "2025-01-05T12:30:00Z"
    }
}
                
```

#### Failure Responses (inside `result`)

```json
{
    "status": "failed",
    "message": "Authentication Token is Required."
}

{
    "status": "failed",
    "message": "Expired Token."
}

{
    "status": "failed",
    "message": "Invalid Token."
}

{
    "status": "failed",
    "message": "Employee Not Found."
}

{
    "status": "failed",
    "message": "Employee Not Registered."
}

{
    "status": "failed",
    "message": "Employee Already Terminated."
}

{
    "status": "failed",
    "message": "Employee Already Resigned."
}

{
    "status": "failed",
    "message": "Employee Not Active."
}

{
    "status": "failed",
    "message": "The Token Doesn't Match With Current Employee Token."
}

{
    "status": "failed",
    "message": "Unexpected Error, Details: <error details>"
}
                
```

</div>

</div>

</div>

<div class="oe_row oe_spaced">

<div class="oe_span12">

<div style="border:1px solid #ddd; border-radius:8px; padding:16px;">

### 5. Employee Change

URL: `/employee/change`

Allows an authenticated employee to change their password. Requires a
valid JWT token, the current password, and the new password with
confirmation.

#### Request – Postman Body (raw → JSON)

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

#### Failure Responses (inside `result`)

```json
{
    "status": "failed",
    "message": "Authentication Token is Required."
}

{
    "status": "failed",
    "message": "All Fields are Required."
}

{
    "status": "failed",
    "message": "Passwords do Not Match."
}

{
    "status": "failed",
    "message": "Old and New Passwords are Same."
}

{
    "status": "failed",
    "message": "Invalid Old Password."
}

{
    "status": "failed",
    "message": "Expired Token."
}

{
    "status": "failed",
    "message": "Invalid Token."
}

{
    "status": "failed",
    "message": "Employee Not Found."
}

{
    "status": "failed",
    "message": "Employee Not Registered."
}

{
    "status": "failed",
    "message": "Employee Already Terminated."
}

{
    "status": "failed",
    "message": "Employee Already Resigned."
}

{
    "status": "failed",
    "message": "Employee Not Active."
}

{
    "status": "failed",
    "message": "The Token Doesn't Match With Current Employee Token."
}

{
    "status": "failed",
    "message": "Unexpected Error, Details: <error details>"
}
                
```

</div>

</div>

</div>

<div class="oe_row oe_spaced">

<div class="oe_span12">

<div style="border:1px solid #ddd; border-radius:8px; padding:16px;">

### 6. Employee Logout

URL: `/employee/logout`

Logs out the employee by invalidating the stored JWT token (`token_key`
and `token_date` are cleared).

#### Request – Postman Body (raw → JSON)

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

#### Failure Responses (inside `result`)

```json
{
    "status": "failed",
    "message": "Authentication Token is Required."
}

{
    "status": "failed",
    "message": "Expired Token."
}

{
    "status": "failed",
    "message": "Invalid Token."
}

{
    "status": "failed",
    "message": "Employee Not Found."
}

{
    "status": "failed",
    "message": "Employee Not Registered."
}

{
    "status": "failed",
    "message": "Employee Already Terminated."
}

{
    "status": "failed",
    "message": "Employee Already Resigned."
}

{
    "status": "failed",
    "message": "Employee Not Active."
}

{
    "status": "failed",
    "message": "The Token Doesn't Match With Current Employee Token."
}

{
    "status": "failed",
    "message": "Unexpected Error, Details: <error details>"
}
                
```

</div>

</div>

</div>

<div class="oe_row oe_spaced">

<div class="oe_span12">

<div style="border:1px solid #ddd; border-radius:8px; padding:16px;">

### 7. Employee Photo

URL: `/employee/photo`

Returns the employee's profile photo as an image file. This endpoint
validates the JWT token and returns the binary image data with
appropriate headers for caching and content type detection. If no photo
is available, a default image is returned.

#### Request – HTTP GET with Query Parameter

```
GET /employee/photo?employee_token=jwt_token_string_here
                
```

#### Success Response

Returns binary image data with headers:

```
Content-Type: image/png (or image/jpeg based on actual image format)
Cache-Control: public, max-age=86400
Content-Length: [size in bytes]

[Binary Image Data]
                
```

#### Usage Example

```
<img src="https://example.com/employee/photo?employee_token=jwt_token_string_here" alt="Employee Photo" />
                
```

#### Failure Responses (JSON)

```json
{
    "status": "failed",
    "message": "Authentication Token is Required."
}

{
    "status": "failed",
    "message": "Expired Token."
}

{
    "status": "failed",
    "message": "Invalid Token."
}

{
    "status": "failed",
    "message": "Employee Not Found."
}

{
    "status": "failed",
    "message": "Employee Not Registered."
}

{
    "status": "failed",
    "message": "Employee Already Terminated."
}

{
    "status": "failed",
    "message": "Employee Already Resigned."
}

{
    "status": "failed",
    "message": "Employee Not Active."
}

{
    "status": "failed",
    "message": "The Token Doesn't Match With Current Employee Token."
}

{
    "status": "failed",
    "message": "Unexpected Error, Details: <error details>"
}
                
```

#### Notes

- This is an HTTP GET endpoint (not JSON-RPC)
- The token is passed as a query parameter in the URL
- On success, returns actual binary image data (not JSON)
- Images are cached for 24 hours (86400 seconds)
- Content-Type is automatically detected (PNG, JPEG, etc.)
- If the employee has no photo, a default image is returned
- All token validation rules apply (active, not terminated, not
  resigned, etc.)

</div>

</div>

</div>

<div class="oe_row oe_spaced">

<div class="oe_span12">

<div style="border:1px solid #ddd; border-radius:8px; padding:16px; margin-bottom:24px;">

### 8. Employee Profile

URL: `/employee/profile`

Returns the employee profile associated with the token. The token must
come from `/employee/login`.

#### Request – Postman Body (raw → JSON)

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
            "employee_url_photo": "https://example.com/employee/photo?employee_token=jwt_token_string_here"
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
        },
        "employee_professional_information": {
            "hiringDate": "2020-01-15",
            "contractStartDate": "2020-01-15",
            "country": "Egypt",
            "sector": "Technology",
            "company": "Example Company Ltd.",
            "department": "Information Technology",
            "division": "Software Development",
            "section": "Backend Development",
            "unit": "API Development",
            "subUnit": "Core Services",
            "position": "Senior Software Developer",
            "location": "Cairo Office"
        }
    }
}
                
```

#### Failure Responses (inside `result`)

```json
{
    "status": "failed",
    "message": "Authentication Token is Required."
}

{
    "status": "failed",
    "message": "Expired Token."
}

{
    "status": "failed",
    "message": "Invalid Token."
}

{
    "status": "failed",
    "message": "Employee Not Found."
}

{
    "status": "failed",
    "message": "Employee Not Registered."
}

{
    "status": "failed",
    "message": "Employee Already Terminated."
}

{
    "status": "failed",
    "message": "Employee Already Resigned."
}

{
    "status": "failed",
    "message": "Employee Not Active."
}

{
    "status": "failed",
    "message": "The Token Doesn't Match With Current Employee Token."
}

{
    "status": "failed",
    "message": "Unexpected Error, Details: <error details>"
}
                
```

#### Notes

- The endpoint `/employee/profile` returns the full combined profile,
  including:
  - **employee_general_information**
  - **employee_personal_information**
  - **employee_professional_information**
- Three additional endpoints are available to retrieve individual
  sections separately:
  - `/employee/general` → returns only **employee_general_information**
  - `/employee/personal` → returns only
    **employee_personal_information**
  - `/employee/professional` → returns only
    **employee_professional_information**
- All three endpoints follow the same JSON-RPC structure and the same
  validation rules: (token required, active employee, not resigned, not
  terminated, etc.).
- All single-section endpoints return success and failure responses with
  the **same JSON format** used by `/employee/profile`.
- The token is validated exactly the same way as all other secured
  endpoints.

</div>

</div>

</div>

</div>
