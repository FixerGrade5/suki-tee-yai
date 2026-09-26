# CLAUDE.md — บันทึกสำหรับ AI / นักพัฒนาที่ทำงานต่อในโปรเจกต์นี้

โปรเจกต์: ระบบสั่งอาหารร้านบุฟเฟต์ **"สุกี้ตี๋ใหญ่"**
Stack: Next.js (App Router, JavaScript) + Supabase + deploy บน Vercel

---

## ⚠️ สำคัญมาก: params ของ Dynamic Route เป็น Promise

โปรเจกต์นี้ใช้ **Next.js เวอร์ชันล่าสุด (15+)** ซึ่งเปลี่ยนพฤติกรรมของ
`params` (และ `searchParams`) ใน Dynamic Route Segment (เช่น
`app/order/[tableId]/page.js`) ให้เป็น **Promise** แทนที่จะเป็น object
ตรง ๆ เหมือนเวอร์ชันเก่า

**ต้อง unwrap ทุกครั้ง ห้ามใช้ `params.xxx` ตรง ๆ เด็ดขาด**

### กรณี Client Component — ใช้ `use()` จาก React

```jsx
'use client';
import { use } from 'react';

export default function Page({ params }) {
  const { tableId } = use(params);
  // ...
}
```

### กรณี Server Component (async function) — ใช้ `await`

```jsx
export default async function Page({ params }) {
  const { tableId } = await params;
  // ...
}
```

จุดนี้จะเกี่ยวข้องโดยตรงตอนสร้างหน้าสั่งอาหารในขั้นตอนถัดไป (เช่นหน้า
สั่งอาหารตามหมายเลขโต๊ะ) — อย่าลืม unwrap ทุกครั้งที่มี Dynamic Route

---

## โครงสร้างฐานข้อมูล Supabase (มีอยู่แล้ว — ห้ามสร้างตารางใหม่ซ้ำ)

ใช้โครงสร้างนี้อ้างอิงตลอดทั้งโปรเจกต์:

### `sessions`
| column | หมายเหตุ |
|---|---|
| id | primary key |
| table_number | หมายเลขโต๊ะ |
| adult_count | จำนวนผู้ใหญ่ |
| child_count | จำนวนเด็ก |
| status | สถานะของ session |
| created_at | เวลาที่สร้าง |

### `menu_categories`
| column | หมายเหตุ |
|---|---|
| id | primary key |
| name | ชื่อหมวดหมู่เมนู |
| sort_order | ลำดับการแสดงผล |

### `menu_items`
| column | หมายเหตุ |
|---|---|
| id | primary key |
| category_id | FK → `menu_categories.id` |
| name | ชื่อเมนู |

### `orders`
| column | หมายเหตุ |
|---|---|
| id | primary key |
| session_id | FK → `sessions.id` |
| table_number | หมายเลขโต๊ะ |
| items | `jsonb` — รายการอาหารที่สั่ง |
| status | สถานะออเดอร์ |
| created_at | เวลาที่สร้าง |

---

## Environment Variables

ตั้งค่าใน Vercel Project Settings และไฟล์ `.env.local` (ดูตัวอย่างใน
`.env.local.example`):

- `NEXT_PUBLIC_SUPABASE_URL`
- `NEXT_PUBLIC_SUPABASE_ANON_KEY`

Client ถูกสร้างไว้แล้วที่ `lib/supabaseClient.js` — import `supabase`
จากไฟล์นี้เพื่อใช้งานได้เลย ไม่ต้องสร้าง client ใหม่ที่อื่น

---

## หน้าที่มีอยู่แล้ว (สำหรับทดสอบ deploy)

- `/` — หน้าแรก แสดงชื่อร้านและลิงก์ไปหน้า `/generate-qr` กับ `/kitchen`
  (ทั้งสองหน้ายังไม่ได้สร้าง จะสร้างในขั้นตอนถัดไป)
