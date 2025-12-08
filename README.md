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
        "employee_code": "EMP0015",
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
        "employee_code": "EMP0015",
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
    "message": "Employee Not Active."
}

{
    "status": "failed",
    "message": "The Token Doesn't Match With Current Employee Token."
}

{
    "status": "failed",
    "message": "Unexpected Error During Verify Token, Details: <error details>"
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
    "message": "Employee Not Active."
}

{
    "status": "failed",
    "message": "The Token Doesn't Match With Current Employee Token."
}

{
    "status": "failed",
    "message": "Unexpected Error During Verify Token, Details: <error details>"
}

{
    "status": "failed",
    "message": "Unexpected Error During Change Password, Details: <error details>"
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
    "message": "Employee Not Active."
}

{
    "status": "failed",
    "message": "The Token Doesn't Match With Current Employee Token."
}

{
    "status": "failed",
    "message": "Unexpected Error During Verify Token, Details: <error details>"
}

{
    "status": "failed",
    "message": "Unexpected Error During Logout, Details: <error details>"
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

Returns an employee's profile photo as an image file. This endpoint
validates the JWT token and returns the binary image data with
appropriate headers for caching and content type detection. If no photo
is available, a default image is returned. You can optionally specify an
employee_id to retrieve another employee's photo (useful for managers
viewing their team members).

#### Request – HTTP GET with Query Parameters

```
GET /employee/photo?employee_token=jwt_token_string_here

// Or to get a specific employee's photo:

GET /employee/photo?employee_id=123&employee_token=jwt_token_string_here
                
```

#### Query Parameters

- **employee_token** (required): JWT authentication token
- **employee_id** (optional): ID of the employee whose photo you want to
  retrieve. If not provided, returns the photo of the authenticated
  employee.

#### Success Response

Returns binary image data with headers:

```
Content-Type: image/png (or image/jpeg based on actual image format)
Cache-Control: public, max-age=86400
Content-Length: [size in bytes]

[Binary Image Data]
                
```

#### Usage Examples

```
<!-- Get authenticated employee's photo -->
<img src="https://example.com/employee/photo?employee_token=jwt_token_string_here" alt="My Photo" />

<!-- Get specific employee's photo -->
<img src="https://example.com/employee/photo?employee_id=123&employee_token=jwt_token_string_here" alt="Employee Photo" />
                
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
    "message": "Employee Not Active."
}

{
    "status": "failed",
    "message": "The Token Doesn't Match With Current Employee Token."
}

{
    "status": "failed",
    "message": "Unexpected Error During Verify Token, Details: <error details>"
}

{
    "status": "failed",
    "message": "Unexpected Error During Get Photo, Details: <error details>"
}
                
```

#### Notes

- This is an HTTP GET endpoint (not JSON-RPC)
- The token is passed as a query parameter in the URL
- On success, returns actual binary image data (not JSON)
- Images are cached for 24 hours (86400 seconds)
- Content-Type is automatically detected (PNG, JPEG, etc.)
- If the employee has no photo, a default image is returned
- All token validation rules apply (active, not terminated, etc.)
- When employee_id is provided, the photo of that specific employee is
  returned (no additional permission checks are performed beyond token
  validation)
- If employee_id is not provided, the authenticated employee's photo is
  returned

</div>

</div>

</div>

<div class="oe_row oe_spaced">

<div class="oe_span12">

<div style="border:1px solid #ddd; border-radius:8px; padding:16px;">

### 8. Employee Home

URL: `/employee/home`

Returns basic home-section information for the employee associated with
the authentication token. The token must come from `/employee/login`.

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
        "message": "Employee Home Retrieved Successfully",
        "employee_id": 15,
        "employee_code": "MP0015",
        "preferred_name": "Administrator",
        "general_shift": {
            "id": 6,
            "name": "General Shift A"
        },
        "employee_url_photo": "https://example.com/employee/photo?employee_token=jwt_token_string_here"
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
    "message": "Employee Not Active."
}

{
    "status": "failed",
    "message": "The Token Doesn't Match With Current Employee Token."
}

{
    "status": "failed",
    "message": "Unexpected Error During Verify Token, Details: <error details>"
}

{
    "status": "failed",
    "message": "Unexpected Error During Get Home, Details: <error details>"
}
                
```

</div>

</div>

</div>

<div class="oe_row oe_spaced">

<div class="oe_span12">

<div style="border:1px solid #ddd; border-radius:8px; padding:16px;">

### 9. Employee Profile

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
            "employee_id": 15,
            "employee_code": "MP0015",
            "preferred_name": "Administrator",
            "fullEnglishName": "A B C D",
            "fullArabicName": "أ ب ت ث",
            "employee_url_photo": "https://example.com/employee/photo?employee_token=jwt_token_string_here"
        },
        "employee_personal_information": {
            "employee_id": 15,
            "employee_code": "MP0015",
            "maritalStatus": "Married",
            "currentDetailedAddressArabic": "العنوان الحالي بالتفصيل",
            "nationalIDDetailedAddressArabic": "عنوان البطاقة الوطنية",
            "personalMobile": "01234567890",
            "businessMobile": "01234567891",
            "militaryStatus": "Completed",
            "emergencyContactName": "John Doe",
            "emergencyContactMobile": "01234567892",
            "emergencyContactRelation": "Brother",
            "gender": "Male",
            "number_of_husbands": 0,
            "husband_ids": [],
            "number_of_wives": 1,
            "wife_ids": [
                {
                    "id": 1,
                    "name": "Jane Smith",
                    "national_id": "29801012233446",
                    "gender": "Female",
                    "si_status": "Yes",
                    "relation": "Spouse"
                }
            ],
            "number_of_children": 2,
            "children_ids": [
                {
                    "id": 2,
                    "name": "Child One",
                    "national_id": "31501012233447",
                    "gender": "Male",
                    "si_status": "Yes",
                    "relation": "child"
                },
                {
                    "id": 3,
                    "name": "Child Two",
                    "national_id": "31701012233448",
                    "gender": "Female",
                    "si_status": "No",
                    "relation": "child"
                }
            ]
        },
        "employee_hierarchical": {
            "employee_id": 15,
            "employee_code": "MP0015",
            "managers_list": [
                {
                    "employee_id": 5,
                    "employee_code": "MP0005",
                    "employee_name": "John Manager",
                    "employee_position": "Department Head",
                    "employee_url_photo": "https://example.com/employee/photo?employee_id=5&employee_token=jwt_token_string_here"
                },
                {
                    "employee_id": 3,
                    "employee_code": "MP0003",
                    "employee_name": "Sarah Director",
                    "employee_position": "IT Director",
                    "employee_url_photo": "https://example.com/employee/photo?employee_id=3&employee_token=jwt_token_string_here"
                }
            ],
            "the_employee": {
                "employee_id": 15,
                "employee_code": "MP0015",
                "employee_name": "A B C D",
                "employee_position": "Senior Software Developer",
                "employee_url_photo": "https://example.com/employee/photo?employee_id=15&employee_token=jwt_token_string_here"
            },
            "children_list": [
                {
                    "employee_id": 28,
                    "employee_code": "MP0028",
                    "employee_name": "Bob Junior",
                    "employee_position": "Junior Developer",
                    "employee_url_photo": "https://example.com/employee/photo?employee_id=28&employee_token=jwt_token_string_here"
                },
                {
                    "employee_id": 31,
                    "employee_code": "MP0031",
                    "employee_name": "Alice Intern",
                    "employee_position": "Software Intern",
                    "employee_url_photo": "https://example.com/employee/photo?employee_id=31&employee_token=jwt_token_string_here"
                }
            ]
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
    "message": "Employee Not Active."
}

{
    "status": "failed",
    "message": "The Token Doesn't Match With Current Employee Token."
}

{
    "status": "failed",
    "message": "Unexpected Error During Verify Token, Details: <error details>"
}

{
    "status": "failed",
    "message": "Unexpected Error During Get Profile, Details: <error details>"
}
                
```

</div>

</div>

</div>

<div class="oe_row oe_spaced">

<div class="oe_span12">

<div style="border:1px solid #ddd; border-radius:8px; padding:16px;">

### 10. Employee General

URL: `/employee/general`

Returns the general information section of the employee profile.

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
        "message": "Employee General Retrieved Successfully",
        "employee_id": 15,
        "employee_code": "MP0015",
        "preferred_name": "Administrator",
        "fullEnglishName": "A B C D",
        "fullArabicName": "أ ب ت ث",
        "employee_url_photo": "https://example.com/employee/photo?employee_token=jwt_token_string_here"
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
    "message": "Employee Not Active."
}

{
    "status": "failed",
    "message": "The Token Doesn't Match With Current Employee Token."
}

{
    "status": "failed",
    "message": "Unexpected Error During Verify Token, Details: <error details>"
}

{
    "status": "failed",
    "message": "Unexpected Error During Get General, Details: <error details>"
}
                
```

</div>

</div>

</div>

<div class="oe_row oe_spaced">

<div class="oe_span12">

<div style="border:1px solid #ddd; border-radius:8px; padding:16px;">

### 11. Employee Personal

URL: `/employee/personal`

Returns the personal information section of the employee profile.

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
        "message": "Employee Personal Retrieved Successfully",
        "employee_id": 15,
        "employee_code": "MP0015",
        "maritalStatus": "Married",
        "currentDetailedAddressArabic": "العنوان الحالي بالتفصيل",
        "nationalIDDetailedAddressArabic": "عنوان البطاقة الوطنية",
        "personalMobile": "01234567890",
        "businessMobile": "01234567891",
        "militaryStatus": "Completed",
        "emergencyContactName": "John Doe",
        "emergencyContactMobile": "01234567892",
        "emergencyContactRelation": "Brother",
        "gender": "Male",
        "number_of_husbands": 0,
        "husband_ids": [],
        "number_of_wives": 1,
        "wife_ids": [
            {
                "id": 1,
                "name": "Jane Smith",
                "national_id": "29801012233446",
                "gender": "Female",
                "si_status": "Yes",
                "relation": "Spouse"
            }
        ],
        "number_of_children": 2,
        "children_ids": [
            {
                "id": 2,
                "name": "Child One",
                "national_id": "31501012233447",
                "gender": "Male",
                "si_status": "Yes",
                "relation": "child"
            },
            {
                "id": 3,
                "name": "Child Two",
                "national_id": "31701012233448",
                "gender": "Female",
                "si_status": "No",
                "relation": "child"
            }
        ]
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
    "message": "Employee Not Active."
}

{
    "status": "failed",
    "message": "The Token Doesn't Match With Current Employee Token."
}

{
    "status": "failed",
    "message": "Unexpected Error During Verify Token, Details: <error details>"
}

{
    "status": "failed",
    "message": "Unexpected Error During Get Personal, Details: <error details>"
}
                
```

</div>

</div>

</div>

<div class="oe_row oe_spaced">

<div class="oe_span12">

<div style="border:1px solid #ddd; border-radius:8px; padding:16px;">

### 12. Employee Professional

URL: `/employee/professional`

Returns the professional information section of the employee profile.

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
        "message": "Employee Professional Retrieved Successfully",
        "employee_id": 15,
        "employee_code": "MP0015",
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
    "message": "Employee Not Active."
}

{
    "status": "failed",
    "message": "The Token Doesn't Match With Current Employee Token."
}

{
    "status": "failed",
    "message": "Unexpected Error During Verify Token, Details: <error details>"
}

{
    "status": "failed",
    "message": "Unexpected Error During Get Professional, Details: <error details>"
}
                
```

</div>

</div>

</div>

<div class="oe_row oe_spaced">

<div class="oe_span12">

<div style="border:1px solid #ddd; border-radius:8px; padding:16px;">

### 13. Employee Hierarchical

URL: `/employee/hierarchical`

Returns the organizational hierarchy information for the employee,
including their managers (reporting chain upwards), their own employee
card, and their direct reports (subordinates). Each employee card
includes ID, code, name, position, and photo URL.

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
        "message": "Employee Hierarchical Retrieved Successfully",
        "employee_id": 15,
        "employee_code": "MP0015",
        "managers_list": [
            {
                "employee_id": 5,
                "employee_code": "MP0005",
                "employee_name": "John Manager",
                "employee_position": "Department Head",
                "employee_url_photo": "https://example.com/employee/photo?employee_id=5&employee_token=jwt_token_string_here"
            },
            {
                "employee_id": 3,
                "employee_code": "MP0003",
                "employee_name": "Sarah Director",
                "employee_position": "IT Director",
                "employee_url_photo": "https://example.com/employee/photo?employee_id=3&employee_token=jwt_token_string_here"
            }
        ],
        "the_employee": {
            "employee_id": 15,
            "employee_code": "MP0015",
            "employee_name": "A B C D",
            "employee_position": "Senior Software Developer",
            "employee_url_photo": "https://example.com/employee/photo?employee_id=15&employee_token=jwt_token_string_here"
        },
        "children_list": [
            {
                "employee_id": 28,
                "employee_code": "MP0028",
                "employee_name": "Bob Junior",
                "employee_position": "Junior Developer",
                "employee_url_photo": "https://example.com/employee/photo?employee_id=28&employee_token=jwt_token_string_here"
            },
            {
                "employee_id": 31,
                "employee_code": "MP0031",
                "employee_name": "Alice Intern",
                "employee_position": "Software Intern",
                "employee_url_photo": "https://example.com/employee/photo?employee_id=31&employee_token=jwt_token_string_here"
            }
        ]
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
    "message": "Employee Not Active."
}

{
    "status": "failed",
    "message": "The Token Doesn't Match With Current Employee Token."
}

{
    "status": "failed",
    "message": "Unexpected Error During Verify Token, Details: <error details>"
}

{
    "status": "failed",
    "message": "Unexpected Error During Get Hierarchical, Details: <error details>"
}
                
```

#### Notes

- **managers_list**: Contains all managers in the reporting chain from
  direct manager to top-level
- **the_employee**: The authenticated employee's own card
- **children_list**: Contains all direct reports (subordinates) of the
  employee
- Photo URLs include the `employee_id` parameter for fetching specific
  employee photos
- Empty arrays are returned if the employee has no managers or no direct
  reports

</div>

</div>

</div>

<div class="oe_row oe_spaced">

<div class="oe_span12">

<div style="border:1px solid #ddd; border-radius:8px; padding:16px;">

### 14. Employee GEO Location

URL: `/employee/geo_location`

Returns the employee's valid geographic location information for
attendance purposes based on a specific date. This endpoint checks if
geo attendance is enabled for the employee and retrieves all valid
location assignments for the provided today date for location details.

#### Request – Postman Body (raw → JSON)

```json
{
    "jsonrpc": "2.0",
    "method": "call",
    "params": {
        "employee_token": "jwt_token_string_here",
        "today_date": "2024-01-15"
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
        "message": "Employee GEO Location Retrieved Successfully",
        "employee_id": 15,
        "employee_code": "MP0015",
        "locations_list": [
            {
                "location_free": false,
                "geo_enabled": true,
                "date_from": "2024-01-01",
                "date_to": "2024-12-31",
                "working_location": {
                    "location_id": 7,
                    "location_name": "Cairo Main Office",
                    "location_code": "CMO-001",
                    "location_address": "123 Tahrir Square, Cairo, Egypt",
                    "location_geo_enabled": true,
                    "latitude": "30.0444",
                    "longitude": "31.2357",
                    "range": "100"
                }
            },
            {
                "location_free": true,
                "geo_enabled": false,
                "date_from": "2024-01-01",
                "date_to": "2024-12-31",
                "working_location": {
                    "location_id": 12,
                    "location_name": "Remote Work",
                    "location_code": "RMT-001",
                    "location_address": "Remote Location",
                    "location_geo_enabled": false,
                    "latitude": "",
                    "longitude": "",
                    "range": ""
                }
            }
        ]
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
    "message": "Today Date is Required."
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
    "message": "Employee Not Active."
}

{
    "status": "failed",
    "message": "The Token Doesn't Match With Current Employee Token."
}

{
    "status": "failed",
    "message": "Unexpected Error During Verify Token, Details: <error details>"
}

{
    "status": "failed",
    "message": "Geo Attendance Currently Not Allowed for this Employee."
}

{
    "status": "failed",
    "message": "Invalid Date Format, Must be in format YYYY-MM-DD with valid month (01–12) and day (01–31)."
}

{
    "status": "failed",
    "message": "No Valid Location Found for Today's Date."
}

{
    "status": "failed",
    "message": "Unexpected Error During Get GEO Location, Details: <error details>"
}
                
```

#### Notes

- This endpoint validates that geo attendance is enabled for the
  employee before returning location data
- Multiple locations may be returned if the employee has multiple valid
  location assignments for the date
- The date must fall within the `date_from` and `date_to` (or be
  open-ended) range of the location assignment and must be GEO
- If `location_geo_enabled` is false, latitude, longitude, and range
  fields will be empty strings
- The `range` value is in meters and defines the geofence radius
- Use this endpoint before allowing attendance check-in to validate the
  employee's location

</div>

</div>

</div>

<div class="oe_row oe_spaced">

<div class="oe_span12">

<div style="border:1px solid #ddd; border-radius:8px; padding:16px;">

### 15. Employee Get Punch

URL: `/employee/get_punch`

Creates a new attendance punch record for an employee with geographic
location information. This endpoint validates the employee's
credentials, location data, and creates a punch integration record that
can be processed for attendance tracking. Supports both location-free
and location-based punches with IN/OUT punch types.

#### Request – Postman Body (raw → JSON)

```json
{
    "jsonrpc": "2.0",
    "method": "call",
    "params": {
        "employee_token": "jwt_token_string_here",
        "date": "2024-01-15",
        "time": "08:30",
        "punch_type": "IN",
        "location_free": "false",
        "location_id": 7,
        "longitude": 31.2357,
        "latitude": 30.0444
    }
}
                
```

#### Request Parameters

- **employee_token** (required): JWT authentication token
- **date** (required): Punch date in YYYY-MM-DD format
- **time** (required): Punch time in HH:MM format (24-hour, 00:00–23:59)
- **punch_type** (required): Type of punch - 'IN', 'In', 'in' (check-in)
  or 'OUT', 'Out', 'out' (check-out)
- **location_free** (required): Boolean indicating if the punch is
  location-free - 'true', 'True', '1' or 'false', 'False', '0'
- **location_id** (required if location_free=false): location ID, Must
  be an Integer or 0 for null
- **longitude** (required): Geographic longitude coordinate, Must be
  Float
- **latitude** (required): Geographic latitude coordinate, Must be Float

#### Success Response Example

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "success",
        "message": "Employee Get Punch Successfully",
        "employee_id": 15,
        "employee_code": "MP0015",
        "punch_integration_id": 1250
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
    "message": "Date is Required."
}

{
    "status": "failed",
    "message": "Time is Required."
}

{
    "status": "failed",
    "message": "Punch Type is Required."
}

{
    "status": "failed",
    "message": "Longitude and Latitude is Required."
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
    "message": "Employee Not Active."
}

{
    "status": "failed",
    "message": "The Token Doesn't Match With Current Employee Token."
}

{
    "status": "failed",
    "message": "Unexpected Error During Verify Token, Details: <error details>"
}

{
    "status": "failed",
    "message": "Invalid Date Format, Must be in format YYYY-MM-DD with valid month (01–12) and day (01–31)."
}

{
    "status": "failed",
    "message": "Invalid Time Format, Must be in format HH:MM ('00:00' — '23:59')."
}

{
    "status": "failed",
    "message": "Invalid Punch Type, Must be in ('IN', 'In', 'in') or ('OUT', 'Out', 'out')."
}

{
    "status": "failed",
    "message": "Invalid Location Free Value, Must be in ('True', 'true', '1') or ('False', 'false', '0')."
}

{
    "status": "failed",
    "message": "Location is Required."
}

{
    "status": "failed",
    "message": "Invalid Location ID Not Found."
}

{
    "status": "failed",
    "message": "Unexpected Error During Get Punch, Details: <error details>"
}
                
```

#### Notes

- The punch record is created with `status='draft'` and can be processed
  later by the system
- Punch type is internally stored as 'F1' (IN) or 'F2' (OUT)
- Time is converted from HH:MM format to float (hours + minutes/60) for
  internal storage
- The `source` field is automatically set to 'geo' for geographic-based
  punches
- When `location_free=true`, the `location_id` parameter is optional and
  will be ignored
- When `location_free=false`, a valid `location_id` must be provided
- Longitude and latitude are always required regardless of location_free
  setting
- The returned `punch_integration_id` can be used to track or reference
  this punch record
- Use the `/employee/geo_location` endpoint first to validate allowed
  locations before creating a punch
- Multiple punches can be created for the same employee on the same day
  (IN/OUT pairs)

</div>

</div>

</div>

<div class="oe_row oe_spaced">

<div class="oe_span12">

<div style="border:1px solid #ddd; border-radius:8px; padding:16px;">

### 16. Payrolls Get Current Month

URL: `/payroll/current_month`

Retrieves payroll information for an employee for a specific month or
the most recent month. This endpoint allows filtering by month and year,
or automatically fetches the latest month's payslips. Returns detailed
information including total paid amount, number of runs, and individual
payslip details with net amounts.

#### Request – Postman Body (raw → JSON)

```json
{
    "jsonrpc": "2.0",
    "method": "call",
    "params": {
        "employee_token": "jwt_token_string_here",
        "month": "3",
        "year": "2024"
    }
}
                
```

#### Request Parameters

- **employee_token** (required): JWT authentication token
- **month** (optional): Month number as string (1-12). If not provided,
  returns the most recent month
- **year** (optional): Year as string (e.g., "2024"). If not provided,
  returns the most recent month

#### Success Response Example (with month/year specified)

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "success",
        "message": "Payroll Selected Month Successfully",
        "month": "3",
        "year": "2024",
        "total_paid": 15000.50,
        "no_runs": 2,
        "payslips": [
            {
                "id": 101,
                "run_number": "SLIP/2024/03/0001",
                "net": 7500.25
            },
            {
                "id": 102,
                "run_number": "SLIP/2024/03/0002",
                "net": 7500.25
            }
        ]
    }
}
                
```

#### Success Response Example (without month/year - latest month)

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "success",
        "message": "Payroll Current Month Successfully",
        "month": "4",
        "year": "2024",
        "total_paid": 8000.00,
        "no_runs": 1,
        "payslips": [
            {
                "id": 150,
                "run_number": "SLIP/2024/12/0001",
                "net": 8000.00
            }
        ]
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
    "message": "Employee Not Active."
}

{
    "status": "failed",
    "message": "The Token Doesn't Match With Current Employee Token."
}

{
    "status": "failed",
    "message": "Unexpected Error During Verify Token, Details: <error details>"
}

{
    "status": "failed",
    "message": "Payslip is not found."
}

{
    "status": "failed",
    "message": "Unexpected Error During Current Month, Details: <error details>"
}
                
```

#### Notes

- Both `month` and `year` must be provided together, or both omitted
- If month/year are omitted, the endpoint returns payslips from the most
  recent month
- The endpoint searches the last 5 payslips and filters for the most
  recent month
- The `net` amount is the absolute value of the NET salary line
- `total_paid` is the sum of all net amounts for the returned payslips
- `no_runs` indicates how many separate payslip runs occurred in the
  month
- Payslips are ordered by month and year in descending order
- Month should be provided as a numeric string (e.g., "1" for January,
  "12" for December)

</div>

</div>

</div>

<div class="oe_row oe_spaced">

<div class="oe_span12">

<div style="border:1px solid #ddd; border-radius:8px; padding:16px;">

### 17. Payroll Get PDF

URL: `/payroll/get_pdf`

Generates and returns a PDF URL for one or multiple payslips. This
endpoint validates the employee's credentials and payslip IDs, then
constructs a URL that can be used to download or view the payslip PDF
document(s). The PDF includes all salary details, deductions, and net
pay information. Supports generating combined PDFs for multiple payslips
in a single request.

#### Request – Postman Body (raw → JSON)

```json
{
    "jsonrpc": "2.0",
    "method": "call",
    "params": {
        "employee_token": "jwt_token_string_here",
        "payroll_ids": [101, 102, 103]
    }
}
                
```

#### Request Parameters

- **employee_token** (required): JWT authentication token
- **payroll_ids** (required): List/Array of payslip IDs to generate PDF
  for. Must be in list format (e.g., \[101\] or \[101, 102, 103\])

#### Success Response Example (Single Payslip)

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "success",
        "message": "Payroll Get PDF Successfully",
        "ids": [101],
        "url": "https://yourdomain.com/print/payslips?list_ids=101"
    }
}
                
```

#### Success Response Example (Multiple Payslips)

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "status": "success",
        "message": "Payroll Get PDF Successfully",
        "ids": [101, 102, 103],
        "url": "https://yourdomain.com/print/payslips?list_ids=101,102,103"
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
    "message": "Payslip IDs is Required."
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
    "message": "Employee Not Active."
}

{
    "status": "failed",
    "message": "The Token Doesn't Match With Current Employee Token."
}

{
    "status": "failed",
    "message": "Unexpected Error During Verify Token, Details: <error details>"
}

{
    "status": "failed",
    "message": "Invalid Payroll IDs Format, Must be in list format."
}

{
    "status": "failed",
    "message": "Payslip is not found."
}

{
    "status": "failed",
    "message": "Unexpected Error During Get PDF, Details: <error details>"
}
                
```

#### Notes

- The `payroll_ids` parameter **must** be provided as a list/array, even
  for a single payslip (e.g., \[101\])
- Passing a non-list format will return an error: "Invalid Payroll IDs
  Format, Must be in list format."
- The response returns `ids` containing the list of found payslip IDs
  (may differ from requested if some IDs don't exist)
- The response returns `url` instead of `pdf_url` for the PDF download
  link
- When multiple IDs are provided, the system generates a combined PDF
  containing all found payslips
- The search uses the `in` operator to find all matching payslips
- If none of the provided IDs are found, the request returns "Payslip is
  not found."
- The endpoint does not verify if the payslips belong to the
  authenticated employee
- The PDF URL uses the system's configured base URL from `web.base.url`
- All found payslip IDs are included in the URL as comma-separated
  values
- The PDF includes formatted payslip information as configured in the
  system
- The PDF URL may require additional authentication depending on system
  configuration
- Use the `/payroll/current_month` endpoint first to get valid payslip
  IDs

</div>

</div>

</div>
