# 🛡️ Middleware & Error Handling

## 📋 **Overview**

Middleware สำหรับจัดการ error และ route ที่ไม่พบในระบบ

---

## 🔍 **Not Found Middleware (404)**

<details>
<summary>จัดการกรณีที่ไม่พบ route</summary>

### Description

Middleware นี้จะจับทุก request ที่ไม่ตรงกับ route ใดๆ ในระบบ และส่ง error 404 Not Found กลับไป

### Implementation Guidelines

- ต้องวางไว้ **หลังจาก route ทั้งหมด** ในไฟล์หลัก (เช่น app.ts หรือ index.ts)
- ส่ง HTTP status code **404**
- ส่ง response ในรูปแบบ JSON ที่สอดคล้องกับระบบ

### Response Format

```json
{
  "success": false,
  "error": "Route not found"
}
```

---

### 📊 **คะแนน: 2 คะแนน**

| เกณฑ์การให้คะแนน                                   | คะแนน |
| -------------------------------------------------- | ----- |
| ✅ สร้าง middleware สำหรับจัดการ 404 Not Found     | 1     |
| ✅ ส่ง response ในรูปแบบ JSON ที่ถูกต้องตามมาตรฐาน | 1     |

</details>

---

## ⚠️ **Error Handling Middleware**

<details>
<summary>จัดการ error ทั่วไปในระบบ</summary>

### Description

Middleware นี้จะจับ error ทั้งหมดที่เกิดขึ้นในระบบ รวมถึง:

- Validation errors จาก Zod
- Database errors
- Unexpected errors
- Custom errors

### Implementation Guidelines

- ต้องวางไว้ **หลังจาก Not Found Middleware**
- รับ parameter 4 ตัว: `(err, req, res, next)`
- ส่ง HTTP status code ที่เหมาะสม (500 สำหรับ internal error)
- จัดการ error แบบต่างๆ ได้อย่างเหมาะสม

### Response Format

**General Error**:

```json
{
  "success": false,
  "error": "Internal server error"
}
```

**Validation Error (Zod)**:

```json
{
  "success": false,
  "error": "Validation failed",
  "details": {
    "fieldErrors": {
      "username": ["Required"],
      "password": ["String must contain at least 6 character(s)"]
    }
  }
}
```

**Custom Error with Status Code**:

```json
{
  "success": false,
  "error": "Custom error message"
}
```

---

### 📊 **คะแนน: 3 คะแนน**

| เกณฑ์การให้คะแนน                                                    | คะแนน |
| ------------------------------------------------------------------- | ----- |
| ✅ สร้าง error middleware ที่รับ 4 parameters (err, req, res, next) | 1     |
| ✅ จัดการ error แบบต่างๆ ได้อย่างเหมาะสม (Zod, custom, general)     | 1     |
| ✅ ส่ง response และ status code ที่ถูกต้องตาม error type            | 1     |

</details>

---

## 🎯 **สรุปคะแนน**

| Middleware                 | คะแนน       | หมายเหตุ                  |
| -------------------------- | ----------- | ------------------------- |
| Not Found Middleware (404) | 2           | จัดการ route ที่ไม่พบ     |
| Error Handling Middleware  | 3           | จัดการ error ทั่วไปในระบบ |
| **รวม**                    | **5 คะแนน** |                           |
