# 📊 Health Records API

## Base URL

```
/health-records
```

**Authentication Required**: ทุก endpoint ต้องใส่ `Authorization: Bearer <token>` ใน header

---

## 📋 **Endpoints**

### **POST /health-records**

<details>
<summary>สร้างบันทึกสุขภาพใหม่ (Create Health Record)</summary>

#### Headers

```
Authorization: Bearer <token>
```

#### Request Body

```json
{
  "type": "weight",
  "value": 70.5,
  "unit": "kg",
  "note": "หลังออกกำลังกาย"
}
```

#### Request Fields

| Field   | Type     | Required | Description                     |
| ------- | -------- | -------- | ------------------------------- |
| `type`  | `string` | ✅       | ประเภทข้อมูลสุขภาพ              |
| `value` | `any`    | ❌       | ค่าของข้อมูล (ขึ้นอยู่กับ type) |
| `unit`  | `string` | ❌       | หน่วย                           |
| `note`  | `string` | ❌       | หมายเหตุเพิ่มเติม               |

**หมายเหตุ**: ฟิลด์อื่นๆ สามารถเพิ่มได้ตามประเภทข้อมูล (flexible)

#### ตัวอย่าง data formats

```json
// น้ำหนัก
{ "type": "weight", "value": 70.5, "unit": "kg" }

// ส่วนสูง
{ "type": "height", "value": 170, "unit": "cm" }

// ความดันโลหิต
{ "type": "blood_pressure", "systolic": 120, "diastolic": 80, "unit": "mmHg" }

// น้ำตาลในเลือด
{ "type": "blood_sugar", "value": 95, "unit": "mg/dL", "timing": "fasting" }

// อุณหภูมิ
{ "type": "temperature", "value": 36.5, "unit": "celsius" }

// อื่นๆ (flexible)
{ "type": "exercise", "activity": "running", "duration": 30, "unit": "minutes", "calories": 250 }
```

#### Success Response (201 Created)

```json
{
  "success": true,
  "message": "Health record created successfully",
  "data": {
    "id": 1,
    "patientId": 5,
    "type": "weight",
    "value": 70.5,
    "unit": "kg",
    "note": "หลังออกกำลังกาย",
    "createdAt": "2026-01-19T10:30:00.000Z",
    "updatedAt": "2026-01-19T10:30:00.000Z"
  }
}
```

**หมายเหตุ**: `data` ใน response คือ object ที่ flatten จาก Prisma return โดย spread `data` field (JSON) ออกมารวมกับ fields อื่นๆ

#### Error Responses

**400 Bad Request** - ข้อมูลไม่ถูกต้อง

```json
{
  "success": false,
  "error": "Validation failed",
  "details": {
    "fieldErrors": {
      "data": ["Required"]
    }
  }
}
```

**401 Unauthorized** - ไม่มี token หรือ token ไม่ถูกต้อง

```json
{
  "success": false,
  "error": "Authentication required"
}
```

---

### 📊 **คะแนน: 10 คะแนน**

| เกณฑ์การให้คะแนน                                        | คะแนน |
| ------------------------------------------------------- | ----- |
| ✅ สร้าง endpoint `/health-records` ที่รับ POST request | 2     |
| ✅ Validate input ด้วย Zod และส่ง error แบบ flatten     | 3     |
| ✅ ดึง patientId จาก JWT token (req.user)               | 3     |
| ✅ บันทึกข้อมูลลง database และส่ง response ตามรูปแบบ    | 2     |

</details>

---

### **GET /health-records**

<details>
<summary>ดึงบันทึกสุขภาพทั้งหมดของผู้ใช้ (Get All Health Records)</summary>

#### Headers

```
Authorization: Bearer <token>
```

#### Query Parameters

| Parameter | Type     | Required | Description                      |
| --------- | -------- | -------- | -------------------------------- |
| `type`    | `string` | ❌       | กรองตามประเภท (เช่น weight)      |
| `limit`   | `number` | ❌       | จำนวนรายการต่อหน้า (default: 10) |
| `offset`  | `number` | ❌       | ข้ามรายการ (default: 0)          |

#### Success Response (200 OK)

```json
{
  "success": true,
  "data": [
    {
      "id": 1,
      "patientId": 5,
      "type": "weight",
      "value": 70.5,
      "unit": "kg",
      "createdAt": "2026-01-19T10:30:00.000Z",
      "updatedAt": "2026-01-19T10:30:00.000Z"
    },
    {
      "id": 2,
      "patientId": 5,
      "type": "blood_pressure",
      "systolic": 120,
      "diastolic": 80,
      "unit": "mmHg",
      "createdAt": "2026-01-18T09:15:00.000Z",
      "updatedAt": "2026-01-18T09:15:00.000Z"
    }
  ],
  "pagination": {
    "total": 25,
    "limit": 10,
    "offset": 0,
    "hasMore": true
  }
}
```

**หมายเหตุ**: Array ของ HealthRecord objects ที่ Prisma return โดยตรง

#### Error Responses

**401 Unauthorized** - ไม่มี token

```json
{
  "success": false,
  "error": "Authentication required"
}
```

---

### 📊 **คะแนน: 12 คะแนน**

| เกณฑ์การให้คะแนน                                        | คะแนน |
| ------------------------------------------------------- | ----- |
| ✅ สร้าง endpoint `/health-records` ที่รับ GET request  | 2     |
| ✅ ดึง patientId จาก JWT token และ query เฉพาะของผู้ใช้ | 5     |
| ✅ รองรับ query parameters (type, limit, offset)        | 3     |
| ✅ ส่ง response พร้อม pagination                        | 2     |

</details>

---

### **GET /health-records/:id**

<details>
<summary>ดึงบันทึกสุขภาพเฉพาะรายการ (Get Health Record by ID)</summary>

#### Headers

```
Authorization: Bearer <token>
```

#### URL Parameters

| Parameter | Type  | Description      |
| --------- | ----- | ---------------- |
| `id`      | `int` | รหัสบันทึกสุขภาพ |

#### Success Response (200 OK)

```json
{
  "success": true,
  "data": {
    "id": 1,
    "patientId": 5,
    "type": "weight",
    "value": 70.5,
    "unit": "kg",
    "note": "หลังออกกำลังกาย",
    "createdAt": "2026-01-19T10:30:00.000Z",
    "updatedAt": "2026-01-19T10:30:00.000Z"
  }
}
```

**หมายเหตุ**: `data` คือ HealthRecord object ที่ Prisma return โดยตรง

#### Error Responses

**401 Unauthorized** - ไม่มี token

```json
{
  "success": false,
  "error": "Authentication required"
}
```

**403 Forbidden** - ไม่มีสิทธิ์เข้าถึง (ไม่ใช่เจ้าของ record)

```json
{
  "success": false,
  "error": "Access denied"
}
```

**404 Not Found** - ไม่พบ record

```json
{
  "success": false,
  "error": "Health record not found"
}
```

---

### 📊 **คะแนน: 10 คะแนน**

| เกณฑ์การให้คะแนน                                            | คะแนน |
| ----------------------------------------------------------- | ----- |
| ✅ สร้าง endpoint `/health-records/:id` ที่รับ GET request  | 2     |
| ✅ ตรวจสอบว่า record เป็นของผู้ใช้ที่ login (403 Forbidden) | 5     |
| ✅ ส่ง 404 Not Found ถ้าไม่พบ record                        | 2     |
| ✅ ส่ง response ตามรูปแบบ                                   | 1     |

</details>

---

### **PUT /health-records/:id**

<details>
<summary>แก้ไขบันทึกสุขภาพ (Update Health Record)</summary>

#### Headers

```
Authorization: Bearer <token>
```

#### URL Parameters

| Parameter | Type  | Description      |
| --------- | ----- | ---------------- |
| `id`      | `int` | รหัสบันทึกสุขภาพ |

#### Request Body

```json
{
  "type": "weight",
  "value": 71.0,
  "unit": "kg",
  "note": "หลังรับประทานอาหาร"
}
```

#### Request Fields

| Field   | Type     | Required | Description                     |
| ------- | -------- | -------- | ------------------------------- |
| `type`  | `string` | ✅       | ประเภทข้อมูลสุขภาพ              |
| `value` | `any`    | ❌       | ค่าของข้อมูล (ขึ้นอยู่กับ type) |
| `unit`  | `string` | ❌       | หน่วย                           |
| `note`  | `string` | ❌       | หมายเหตุเพิ่มเติม               |

**หมายเหตุ**: ฟิลด์อื่นๆ สามารถเพิ่มได้ตามประเภทข้อมูล (flexible)

#### Success Response (200 OK)

```json
{
  "success": true,
  "message": "Health record updated successfully",
  "data": {
    "id": 1,
    "patientId": 5,
    "type": "weight",
    "value": 71.0,
    "unit": "kg",
    "note": "หลังรับประทานอาหาร",
    "createdAt": "2026-01-19T10:30:00.000Z",
    "updatedAt": "2026-01-19T11:00:00.000Z"
  }
}
```

**หมายเหตุ**: `data` ใน response คือ object ที่ flatten จาก Prisma return โดย spread `data` field (JSON) ออกมารวมกับ fields อื่นๆ

#### Error Responses

**400 Bad Request** - ข้อมูลไม่ถูกต้อง

```json
{
  "success": false,
  "error": "Validation failed",
  "details": {
    "fieldErrors": {
      "type": ["Required"]
    }
  }
}
```

**401 Unauthorized** - ไม่มี token

```json
{
  "success": false,
  "error": "Authentication required"
}
```

**403 Forbidden** - ไม่มีสิทธิ์แก้ไข

```json
{
  "success": false,
  "error": "Access denied"
}
```

**404 Not Found** - ไม่พบ record

```json
{
  "success": false,
  "error": "Health record not found"
}
```

---

### 📊 **คะแนน: 10 คะแนน**

| เกณฑ์การให้คะแนน                                           | คะแนน |
| ---------------------------------------------------------- | ----- |
| ✅ สร้าง endpoint `/health-records/:id` ที่รับ PUT request | 2     |
| ✅ Validate input ด้วย Zod และส่ง error แบบ flatten        | 3     |
| ✅ ตรวจสอบสิทธิ์ของเจ้าของ record (403 Forbidden)          | 3     |
| ✅ อัปเดตข้อมูลและส่ง response ตามรูปแบบ                   | 2     |

</details>

---

### **DELETE /health-records/:id**

<details>
<summary>ลบบันทึกสุขภาพ (Delete Health Record)</summary>

#### Headers

```
Authorization: Bearer <token>
```

#### URL Parameters

| Parameter | Type  | Description      |
| --------- | ----- | ---------------- |
| `id`      | `int` | รหัสบันทึกสุขภาพ |

#### Success Response (200 OK)

```json
{
  "success": true,
  "message": "Health record deleted successfully"
}
```

#### Error Responses

**401 Unauthorized** - ไม่มี token

```json
{
  "success": false,
  "error": "Authentication required"
}
```

**403 Forbidden** - ไม่มีสิทธิ์ลบ

```json
{
  "success": false,
  "error": "Access denied"
}
```

**404 Not Found** - ไม่พบ record

```json
{
  "success": false,
  "error": "Health record not found"
}
```

---

### 📊 **คะแนน: 8 คะแนน**

| เกณฑ์การให้คะแนน                                              | คะแนน |
| ------------------------------------------------------------- | ----- |
| ✅ สร้าง endpoint `/health-records/:id` ที่รับ DELETE request | 2     |
| ✅ ตรวจสอบสิทธิ์ของเจ้าของ record (403 Forbidden)             | 4     |
| ✅ ลบข้อมูลและส่ง response ตามรูปแบบ                          | 2     |

</details>

---

## 🎯 **สรุปคะแนน**

| Endpoint                     | คะแนน        | หมายเหตุ                      |
| ---------------------------- | ------------ | ----------------------------- |
| `POST /health-records`       | 10           | สร้างบันทึกสุขภาพใหม่         |
| `GET /health-records`        | 12           | ดึงบันทึกทั้งหมด + pagination |
| `GET /health-records/:id`    | 10           | ดึงบันทึกเฉพาะรายการ          |
| `PUT /health-records/:id`    | 10           | แก้ไขบันทึกสุขภาพ             |
| `DELETE /health-records/:id` | 8            | ลบบันทึกสุขภาพ                |
| **รวม**                      | **50 คะแนน** |                               |
