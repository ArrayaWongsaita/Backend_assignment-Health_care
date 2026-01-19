# 🔐 Authentication API

## Base URL

```
/auth
```

---

## 📋 **Endpoints**

### **POST /auth/register**

<details>
<summary>สมัครสมาชิกใหม่ (Register)</summary>

#### Request Body

```json
{
  "username": "john_doe",
  "password": "securePassword123",
  "role": "PATIENT"
}
```

#### Request Fields

| Field      | Type     | Required | Description                                 |
| ---------- | -------- | -------- | ------------------------------------------- |
| `username` | `string` | ✅       | ชื่อผู้ใช้ (ต้องไม่ซ้ำในระบบ)               |
| `password` | `string` | ✅       | รหัสผ่าน (ควรมีความยาวอย่างน้อย 6 ตัวอักษร) |
| `role`     | `string` | ✅       | บทบาท: `DOCTOR` หรือ `PATIENT`              |

#### Success Response (201 Created)

```json
{
  "success": true,
  "message": "User registered successfully",
  "data": {
    "id": 1,
    "username": "john_doe",
    "role": "PATIENT",
    "createdAt": "2026-01-19T10:30:00.000Z"
  }
}
```

#### Error Responses

**400 Bad Request** - ข้อมูลไม่ครบถ้วนหรือไม่ถูกต้อง

```json
{
  "success": false,
  "error": "Validation failed",
  "details": {
    "fieldErrors": {
      "username": ["Required"],
      "password": ["String must contain at least 6 character(s)"],
      "role": ["Invalid enum value. Expected 'DOCTOR' | 'PATIENT'"]
    }
  }
}
```

**409 Conflict** - ชื่อผู้ใช้ซ้ำ

```json
{
  "success": false,
  "error": "Username already exists"
}
```

---

### 📊 **คะแนน: 15 คะแนน**

| เกณฑ์การให้คะแนน                                       | คะแนน |
| ------------------------------------------------------ | ----- |
| ✅ สร้าง endpoint `/auth/register` ที่รับ POST request | 3     |
| ✅ Validate input ด้วย Zod และส่ง error แบบ flatten    | 4     |
| ✅ ตรวจสอบ username ซ้ำและส่ง 409 Conflict             | 3     |
| ✅ Hash password ด้วย bcrypt (cost factor ≥ 10)        | 3     |
| ✅ บันทึกข้อมูลลง database และส่ง response ตามรูปแบบ   | 2     |

</details>

---

### **POST /auth/login**

<details>
<summary>เข้าสู่ระบบ (Login)</summary>

#### Request Body

```json
{
  "username": "john_doe",
  "password": "securePassword123"
}
```

#### Request Fields

| Field      | Type     | Required | Description |
| ---------- | -------- | -------- | ----------- |
| `username` | `string` | ✅       | ชื่อผู้ใช้  |
| `password` | `string` | ✅       | รหัสผ่าน    |

#### Success Response (200 OK)

```json
{
  "success": true,
  "message": "Login successful",
  "data": {
    "id": 1,
    "username": "john_doe",
    "role": "PATIENT",
    "createdAt": "2026-01-19T10:30:00.000Z"
  },
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

#### Error Responses

**400 Bad Request** - ข้อมูลไม่ครบถ้วน

```json
{
  "success": false,
  "error": "Validation failed",
  "details": {
    "fieldErrors": {
      "username": ["Required"],
      "password": ["Required"]
    }
  }
}
```

**401 Unauthorized** - ข้อมูลเข้าสู่ระบบไม่ถูกต้อง

```json
{
  "success": false,
  "error": "Invalid username or password"
}
```

---

### 📊 **คะแนน: 15 คะแนน**

| เกณฑ์การให้คะแนน                                      | คะแนน |
| ----------------------------------------------------- | ----- |
| ✅ สร้าง endpoint `/auth/login` ที่รับ POST request   | 2     |
| ✅ Validate input ด้วย Zod และส่ง error แบบ flatten   | 3     |
| ✅ ตรวจสอบ username ในฐานข้อมูล                       | 3     |
| ✅ เปรียบเทียบ password ด้วย bcrypt.compare           | 3     |
| ✅ สร้าง JWT token พร้อม payload (id, username, role) | 3     |
| ✅ ส่ง response ตามรูปแบบพร้อม token                  | 1     |

</details>

---

### **GET /auth/me**

<details>
<summary>ดึงข้อมูลผู้ใช้ปัจจุบัน (Get Current User)</summary>

#### Headers

```
Authorization: Bearer <token>
```

#### Success Response (200 OK)

```json
{
  "success": true,
  "data": {
    "id": 1,
    "username": "john_doe",
    "role": "PATIENT",
    "createdAt": "2026-01-19T10:30:00.000Z",
    "updatedAt": "2026-01-19T10:30:00.000Z"
  }
}
```

#### Error Responses

**401 Unauthorized** - ไม่มี token หรือ token ไม่ถูกต้อง

```json
{
  "success": false,
  "error": "Authentication required"
}
```

**401 Unauthorized** - Token หมดอายุ

```json
{
  "success": false,
  "error": "Token expired"
}
```

---

### 📊 **คะแนน: 10 คะแนน**

| เกณฑ์การให้คะแนน                                               | คะแนน |
| -------------------------------------------------------------- | ----- |
| ✅ สร้าง endpoint `/auth/me` ที่รับ GET request                | 2     |
| ✅ สร้าง middleware ตรวจสอบ JWT token จาก Authorization header | 5     |
| ✅ ดึงข้อมูลผู้ใช้จาก database ตาม user id ใน token            | 2     |
| ✅ ส่ง response ตามรูปแบบหรือ 401 Unauthorized                 | 1     |

</details>

---

## 🎯 **สรุปคะแนน**

| Endpoint              | คะแนน        | หมายเหตุ                |
| --------------------- | ------------ | ----------------------- |
| `POST /auth/register` | 15           | สมัครสมาชิกใหม่         |
| `POST /auth/login`    | 15           | เข้าสู่ระบบและรับ token |
| `GET /auth/me`        | 10           | ดูข้อมูลผู้ใช้ปัจจุบัน  |
| **รวม**               | **40 คะแนน** |                         |
