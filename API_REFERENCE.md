# Hospital Dashboard - API Reference Guide

## Overview
All endpoints return JSON and support pagination. Errors include descriptive messages with appropriate HTTP status codes.

---

## 1. PATIENTS API

### Create Patient
```
POST /api/patients
Content-Type: application/json
```

**Request Body:**
```json
{
  "mrn": "MRN-123456",
  "firstName": "John",
  "lastName": "Doe",
  "age": 45,
  "gender": "Male",
  "email": "john@hospital.com",
  "phone": "555-0123",
  "address": "123 Main St",
  "city": "Springfield",
  "state": "IL",
  "zipCode": "62701",
  "bloodType": "O+",
  "allergies": "Penicillin",
  "chronicConditions": "Diabetes",
  "emergencyContact": "Jane Doe",
  "emergencyPhone": "555-0456",
  "insuranceId": "INS-789012",
  "departmentId": "dept-uuid"
}
```

**Response (201 Created):**
```json
{
  "id": "patient-uuid",
  "mrn": "MRN-123456",
  "firstName": "John",
  "lastName": "Doe",
  "age": 45,
  "gender": "Male",
  "email": "john@hospital.com",
  "department": {
    "id": "dept-uuid",
    "name": "Cardiology"
  },
  "createdAt": "2025-02-28T10:30:00.000Z",
  "updatedAt": "2025-02-28T10:30:00.000Z"
}
```

### Get Patients
```
GET /api/patients?page=1&limit=50&search=john
```

**Query Parameters:**
- `page` (int, default: 1) - Page number
- `limit` (int, default: 50) - Records per page
- `search` (string, optional) - Search by name, MRN, or email

**Response (200 OK):**
```json
{
  "patients": [
    {
      "id": "patient-uuid",
      "mrn": "MRN-123456",
      "firstName": "John",
      "lastName": "Doe",
      "age": 45,
      "gender": "Male",
      "email": "john@hospital.com",
      "phone": "555-0123",
      "department": {
        "id": "dept-uuid",
        "name": "Cardiology"
      },
      "createdAt": "2025-02-28T10:30:00.000Z"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 50,
    "total": 150,
    "pages": 3
  }
}
```

---

## 2. ADMISSIONS API

### Create Admission
```
POST /api/admissions
Content-Type: application/json
```

**Request Body:**
```json
{
  "patientId": "patient-uuid",
  "departmentId": "dept-uuid",
  "reason": "Emergency surgery",
  "notes": "Post-operative monitoring required",
  "priorityLevel": "High",
  "waitTime": 25,
  "status": "Active",
  "admissionDate": "2025-02-28T14:30:00Z"
}
```

**Response (201 Created):**
```json
{
  "id": "admission-uuid",
  "patientId": "patient-uuid",
  "departmentId": "dept-uuid",
  "bedId": "bed-uuid",
  "admissionDate": "2025-02-28T14:30:00.000Z",
  "dischargeDate": null,
  "status": "Active",
  "reason": "Emergency surgery",
  "notes": "Post-operative monitoring required",
  "priorityLevel": "High",
  "waitTime": 25,
  "patient": {
    "id": "patient-uuid",
    "firstName": "John",
    "lastName": "Doe",
    "mrn": "MRN-123456"
  },
  "department": {
    "id": "dept-uuid",
    "name": "General Surgery"
  },
  "bed": {
    "id": "bed-uuid",
    "bedNumber": "SUR-001",
    "status": "Occupied"
  },
  "createdAt": "2025-02-28T14:30:00.000Z"
}
```

### Get Admissions
```
GET /api/admissions?page=1&limit=50&status=Active
```

**Query Parameters:**
- `page` (int, default: 1) - Page number
- `limit` (int, default: 50) - Records per page
- `status` (string, optional) - Filter by status: Active, Discharged, Cancelled

**Response (200 OK):**
```json
{
  "admissions": [
    {
      "id": "admission-uuid",
      "patient": {
        "firstName": "John",
        "lastName": "Doe",
        "mrn": "MRN-123456"
      },
      "department": {
        "name": "General Surgery"
      },
      "admissionDate": "2025-02-28T14:30:00.000Z",
      "status": "Active",
      "priorityLevel": "High",
      "waitTime": 25
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 50,
    "total": 45,
    "pages": 1
  }
}
```

---

## 3. APPOINTMENTS API

### Create Appointment
```
POST /api/appointments
Content-Type: application/json
```

**Request Body:**
```json
{
  "patientId": "patient-uuid",
  "departmentId": "dept-uuid",
  "appointmentDate": "2025-03-15T10:30:00Z",
  "type": "Consultation",
  "duration": 30,
  "status": "Scheduled",
  "notes": "Annual checkup"
}
```

**Response (201 Created):**
```json
{
  "id": "appointment-uuid",
  "patientId": "patient-uuid",
  "departmentId": "dept-uuid",
  "appointmentDate": "2025-03-15T10:30:00.000Z",
  "type": "Consultation",
  "duration": 30,
  "status": "Scheduled",
  "notes": "Annual checkup",
  "patient": {
    "firstName": "John",
    "lastName": "Doe"
  },
  "department": {
    "name": "Cardiology"
  },
  "createdAt": "2025-02-28T10:30:00.000Z"
}
```

### Get Appointments
```
GET /api/appointments?page=1&limit=50&status=Scheduled
```

**Query Parameters:**
- `page` (int, default: 1) - Page number
- `limit` (int, default: 50) - Records per page
- `status` (string, optional) - Filter by status: Scheduled, Confirmed, Completed, Cancelled, NoShow

---

## 4. DEPARTMENTS API

### Get All Departments
```
GET /api/departments
```

**Response (200 OK):**
```json
[
  {
    "id": "dept-uuid",
    "name": "Emergency",
    "capacity": 80,
    "staffCount": 25,
    "currentLoad": 32,
    "occupancyRate": 40,
    "avgWaitTime": 28,
    "occupiedBeds": 32,
    "totalBeds": 80
  },
  {
    "id": "dept-uuid-2",
    "name": "Cardiology",
    "capacity": 50,
    "staffCount": 15,
    "currentLoad": 38,
    "occupancyRate": 76,
    "avgWaitTime": 45,
    "occupiedBeds": 38,
    "totalBeds": 50
  }
]
```

### Create Department
```
POST /api/departments
Content-Type: application/json
```

**Request Body:**
```json
{
  "name": "Pediatrics",
  "capacity": 60,
  "staffCount": 18,
  "description": "Children's healthcare department"
}
```

**Response (201 Created):**
```json
{
  "id": "dept-uuid",
  "name": "Pediatrics",
  "capacity": 60,
  "staffCount": 18,
  "description": "Children's healthcare department",
  "createdAt": "2025-02-28T10:30:00.000Z"
}
```

---

## 5. DASHBOARD STATS API

### Get Dashboard Statistics
```
GET /api/dashboard/stats
```

**Response (200 OK):**
```json
{
  "totalPatients": 150,
  "activeAdmissions": 45,
  "avgWaitTime": 32,
  "bedOccupancyRate": 68,
  "monthlyRevenue": 1250000,
  "upcomingAppointments": 18,
  "departmentLoad": [
    {
      "department": "Emergency",
      "currentLoad": 25,
      "capacity": 80,
      "occupancyRate": 31,
      "waitingPatients": 25,
      "avgWaitTime": 28,
      "staffCount": 25,
      "activeCases": 25,
      "resolvedToday": 0,
      "riskLevel": "Stable",
      "occupiedBeds": 25,
      "totalBeds": 80
    },
    {
      "department": "ICU",
      "currentLoad": 22,
      "capacity": 40,
      "occupancyRate": 55,
      "waitingPatients": 22,
      "avgWaitTime": 5,
      "staffCount": 20,
      "activeCases": 22,
      "resolvedToday": 0,
      "riskLevel": "Stable",
      "occupiedBeds": 22,
      "totalBeds": 40
    }
  ],
  "timestamp": "2025-02-28T15:45:30.000Z"
}
```

---

## Error Responses

### 400 Bad Request
```json
{
  "error": "Missing required fields: mrn, firstName, lastName, age"
}
```

### 404 Not Found
```json
{
  "error": "Patient not found"
}
```

### 409 Conflict
```json
{
  "error": "MRN already exists"
}
```

### 500 Internal Server Error
```json
{
  "error": "Failed to create patient"
}
```

---

## HTTP Status Codes

| Code | Meaning | Example |
|------|---------|---------|
| 200 | OK | GET successful |
| 201 | Created | POST successful |
| 400 | Bad Request | Invalid input |
| 404 | Not Found | Record doesn't exist |
| 409 | Conflict | Duplicate record |
| 500 | Server Error | Database error |

---

## Data Validation Rules

### Patients
- **MRN** - Required, unique, max 50 chars
- **FIrst/Last Name** - Required, max 100 chars
- **Age** - Required, between 0 and 150
- **Gender** - Optional, values: Male, Female, Other
- **Email** - Optional, valid email format

### Admissions
- **Patient ID** - Required, must exist
- **Department ID** - Required, must exist
- **Priority Level** - Values: Low, Medium, High, Critical
- **Status** - Values: Active, Discharged, Cancelled
- **Wait Time** - Integer, non-negative

### Appointments
- **Patient ID** - Required, must exist
- **Department ID** - Required, must exist
- **Appointment Date** - Required, ISO 8601 format
- **Type** - Values: Consultation, Follow-up, Procedure, Emergency
- **Duration** - Integer, > 0

### Departments
- **Name** - Required, unique, max 100 chars
- **Capacity** - Required, positive integer
- **Staff Count** - Optional, non-negative integer

---

## Example JavaScript Usage

### Fetching Patients
```javascript
const fetchPatients = async () => {
  const response = await fetch('/api/patients?page=1&limit=20')
  const data = await response.json()
  console.log(data.patients)
  console.log(data.pagination)
}
```

### Creating a Patient
```javascript
const createPatient = async () => {
  const response = await fetch('/api/patients', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
      mrn: 'MRN-NEW-001',
      firstName: 'Jane',
      lastName: 'Smith',
      age: 35,
      gender: 'Female'
    })
  })
  
  if (response.ok) {
    const patient = await response.json()
    console.log('Patient created:', patient)
  } else {
    const error = await response.json()
    console.error('Error:', error.error)
  }
}
```

### Getting Dashboard Stats
```javascript
const getStats = async () => {
  const response = await fetch('/api/dashboard/stats')
  const stats = await response.json()
  
  console.log(`Total Patients: ${stats.totalPatients}`)
  console.log(`Bed Occupancy: ${stats.bedOccupancyRate}%`)
  console.log(`Monthly Revenue: $${stats.monthlyRevenue}`)
}
```

---

## Rate Limiting (Future Enhancement)
Consider adding rate limiting for production:
- Per-endpoint limits
- Per-IP address limits
- Per-user limits for authenticated users

---

## Pagination Best Practices
- Default limit: 50 records
- Maximum limit: 100 records (for performance)
- Always check `pagination.pages` to know total pages
- Store `page` state in component for navigation

---

## Search Tips
- Case-insensitive
- Searches multiple fields
- Works with partial matches
- Combine with pagination for large result sets

---

**Need help?** Check DATABASE_SETUP.md or MIGRATION_COMPLETE.md for more information.
