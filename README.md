# สุกี้ตี๋ใหญ่ — ระบบสั่งอาหาร

Next.js (App Router, JavaScript) + Supabase, deploy บน Vercel

## เริ่มต้นใช้งาน

```bash
npm install
cp .env.local.example .env.local   # แล้วกรอกค่า Supabase ของจริง
npm run dev
```

เปิด [http://localhost:3000](http://localhost:3000)

## Deploy บน Vercel

1. Push โปรเจกต์นี้ขึ้น Git repository
2. Import เข้า Vercel
3. ตั้งค่า Environment Variables ใน Vercel:
   - `NEXT_PUBLIC_SUPABASE_URL`
   - `NEXT_PUBLIC_SUPABASE_ANON_KEY`
4. Deploy — ทดสอบว่าเข้าหน้าแรกได้ และลิงก์ไป `/generate-qr`, `/kitchen`
   แสดงผล (หน้าเหล่านี้จะสร้างในขั้นตอนถัดไป)

ดูรายละเอียดโครงสร้างฐานข้อมูลและข้อควรระวังเรื่อง Next.js เวอร์ชันใหม่
ได้ที่ [`CLAUDE.md`](./CLAUDE.md)
