# 🗄️ Database Setup

## เทคโนโลยี

- **Prisma ORM** - Modern database toolkit
- **PostgreSQL** - Relational database

---

## 📋 **รายละเอียดเกณฑ์การให้คะแนน**

### **Model `User` (4 คะแนน)**

<details>
<summary>เกณฑ์การให้คะแนน</summary>

#### ✅ **ต้องมี Fields (3 คะแนน)**

**ต้องมี:**

- `id` - Primary key, auto increment
- `username` - Unique
- `password` - String (สำหรับเก็บ hashed password)
- `role` - Enum (DOCTOR/PATIENT)
- `createdAt` - Timestamp
- `updatedAt` - Timestamp (auto update)

#### ✅ **Relations (1 คะแนน)**

**ต้องมี:**

- Relation กับ `DoctorNote` แบบ self-referential (2 relations: DoctorNotesAsDoctor, DoctorNotesAsPatient)
- Relation กับ `HealthRecord`

</details>

---

### **Model `HealthRecord` (4 คะแนน)**

<details>
<summary>เกณฑ์การให้คะแนน</summary>

#### ✅ **ต้องมี Fields (3 คะแนน)**

**ต้องมี:**

- `id` - Primary key, auto increment
- `patientId` - Foreign key to User
- `type` - String (ประเภทข้อมูลสุขภาพ)
- `value` - Optional String (ค่าข้อมูล)
- `unit` - Optional String (หน่วย)
- `note` - Optional String (หมายเหตุ)
- `createdAt`, `updatedAt` - Timestamps

#### ✅ **Relations & Indexes (1 คะแนน)**

**ต้องมี:**

- Relation กับ `User` (patient)
- Index บน `patientId` เพื่อ performance

</details>

---

### **Model `DoctorNote` (4 คะแนน)**

<details>
<summary>เกณฑ์การให้คะแนน</summary>

#### ✅ **ต้องมี Fields (3 คะแนน)**

**ต้องมี:**

- `id` - Primary key, auto increment
- `note` - String (คำแนะนำจากแพทย์)
- `doctorId` - Foreign key to User (แพทย์)
- `patientId` - Foreign key to User (ผู้ป่วย)
- `createdAt`, `updatedAt` - Timestamps

#### ✅ **Relations & Indexes (1 คะแนน)**

**ต้องมี:**

- Relation กับ `User` สองแบบ (doctor และ patient)
- ใช้ named relations (`"DoctorNotesAsDoctor"`, `"DoctorNotesAsPatient"`)
- Indexes บน `doctorId` และ `patientId`

</details>

---

### **Generated Directory & Prisma Instance (3 คะแนน)**

<details>
<summary>เกณฑ์การให้คะแนน</summary>

#### ✅ **Generator Configuration (1 คะแนน)**

**ต้องมี:**

- `generator client` config
- `output` path ชี้ไปที่ `src/db/generated/prisma` directory

#### ✅ **Prisma Instance File (2 คะแนน)**

**ต้องมี:**

- ไฟล์ Prisma instance (เช่น `src/db/prisma.js` หรือ `lib/prisma.js`)
- Import `PrismaClient` จาก `generated/prisma`
- สร้าง instance ของ `PrismaClient`
- Export เพื่อใช้งานในที่อื่น
- (Optional) Prevent multiple instances ใน development

</details>

---

## 🎯 **สรุปคะแนน**

| เกณฑ์การให้คะแนน                                 | คะแนน        |
| ------------------------------------------------ | ------------ |
| ✅ Prisma Schema ถูกต้องและใช้ PostgreSQL        | -            |
| ✅ Model `User` ครบถ้วนตาม ERD                   | 4            |
| ✅ Model `HealthRecord` ครบถ้วนตาม ERD           | 4            |
| ✅ Model `DoctorNote` ครบถ้วนตาม ERD             | 4            |
| ✅ มี `generated/` directory และ Prisma instance | 3            |
| **รวม**                                          | **15 คะแนน** |
