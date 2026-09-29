# UniQ - Smart Canteen & Live Queue Platform
> ระบบสั่งอาหารและบริหารคิวโรงอาหารอัจฉริยะ (Live Queue & Canteen Hub) แบบ Multi-Role เชื่อมต่อ Google Apps Script และ Google Sheets แบบเรียลไทม์ 100%

---

## 🌟 ฟีเจอร์หลัก (Key Features)

### 1. สถาปัตยกรรมหลัก (Architecture Compliance)
- **Zero Local Storage & Pure Cloud Sync**: ข้อมูลร้านค้า, ออเดอร์, ธุรกรรม ไม่มีการเก็บหรือแคชใน `localStorage` ดึงข้อมูลสดจาก Cloud ผ่านการ Polling ทุกๆ 4 วินาที
- **Session Management**: บันทึกข้อมูล Session ผู้ใช้งานปัจจุบันใน `sessionStorage` เท่านั้น (รีเฟรชหน้าเดิมได้ไม่หลุด, หากเปิดแท็บใหม่หรือเครื่องใหม่ บังคับล็อกอินเสมอ)
- **Thailand Timezone (`Asia/Bangkok`)**: แสดงและบันทึกเวลาในรูปแบบ `DD/MM/YYYY HH:mm:ss`
- **Newest First Sorting**: ประวัติการสั่งซื้อ, การเติมเงิน, ถอนเงิน และธุรกรรมการเงิน ถูกจัดเรียงใหม่สุดอยู่บนสุดเสมอ

---

### 2. Multi-Role Features

#### 👨‍🎓 บทบาทลูกค้า / นิสิต (`STUDENT`)
- **กระดานคิวสด (Live Queue Board)**: ดูจำนวนคิวรอและคิวที่กำลังปรุงของแต่ละร้านค้าแบบสดๆ
- **สั่งอาหารและปรับแต่ง (Order Customization)**: เลือกเมนู, เลือกท็อปปิ้งเสริม (Addons), ระบุหมายเหตุพิเศษ, เลือกว่าจะทานที่โต๊ะ (ระบุเลขโต๊ะ) หรือห่อกลับบ้าน
- **2 ช่องทางการชำระเงิน**:
  - **UniQ Wallet**: หักเงินสดทันที มีระบบคืนเงินอัตโนมัติหากออเดอร์ถูกยกเลิก
  - **PromptPay QR Code**: สแกนจ่ายตรงเข้าร้าน พร้อมแนบรูปภาพสลิปโอนเงิน
- **Live Queue Tracking**: ติดตามสถานะคิวแบบเรียลไทม์ (`PENDING` -> `COOKING` -> `READY` -> `COMPLETED`) พร้อมเสียงแจ้งเตือนและ Confetti เมื่ออาหารเสร็จ (`READY`)
- **รีวิวร้านค้า**: ให้คะแนน 1-5 ดาวและเขียนข้อความรีวิวเมื่อรับอาหารสำเร็จ
- **UniQ Wallet Hub**: เติมเงิน (แนบสลิป), ถอนเงินเข้าบัญชีธนาคาร, ดูรายการเดินบัญชี `IN`/`OUT`

#### 👨‍🍳 บทบาทร้านค้า Kitchen Display System (`MERCHANT`)
- **จอครัว KDS เรียลไทม์**: รับออเดอร์ใหม่พร้อมเสียงแจ้งเตือน (Web Audio API Bell)
- **ตรวจสลิปโอนเงิน**: ขยายดูรูปภาพสลิป PromptPay ของลูกค้า
- **ปุ่มปรับสถานะคิวรวดเร็ว**: กดรับทำอาหาร (`COOKING`) -> กดเรียกคิว (`READY`) -> กดจบออเดอร์ (`COMPLETED`)
- **โอนเงินเข้ากระเป๋าร้านอัตโนมัติ**: เมื่อออเดอร์ที่ชำระผ่าน UniQ Wallet เสร็จสิ้น (`COMPLETED`) ระบบจะโอนเงินเข้า Wallet ของร้านและลงชีท `Transactions` ทันที
- **จัดการร้านค้าและเมนู**: เปิด-ปิดร้าน, ปรับเวลาเปิด-ปิด, อัปโหลด QR Code, เพิ่ม/ลบเมนูอาหาร, ปรับสถานะของหมด (Sold Out)
- **การเงินร้านค้า**: สรุปยอดขายวันนี้, ยอดขายสะสมทั้งหมด, ส่งคำขอถอนเงินเข้าบัญชีธนาคาร

#### 🛡️ บทบาทผู้ดูแลระบบสูงสุด (`ADMIN`)
- **Dashboard Overview**: สรุปยอดขายรวมทั้งแอปวันนี้/ทั้งหมด, สถิติออเดอร์, ร้านค้าที่เปิด, กราฟสัดส่วนรายได้แต่ละร้าน
- **อนุมัติการเงิน**:
  - อนุมัติ/ปฏิเสธคำขอเติมเงิน พร้อมตรวจสลิป
  - อนุมัติคำขอถอนเงิน พร้อมแนบหลักฐานสลิปการโอนเงิน
- **จัดการร้านค้าและผู้ใช้**: ตรวจสอบรายชื่อ, ปิดร้านชั่วคราว, ลบร้าน, ลบผู้ใช้งาน
- **Master Reset**: ฟังก์ชันล้างและคืนค่าฐานข้อมูลเริ่มต้น 7 ชีทบน Google Sheets

---

## 📂 โครงสร้างฐานข้อมูล 7 ชีทบน Google Sheets

1. `Users`: `id` | `name` | `username` | `role` | `phone` | `merchantShopId` | `avatarUrl` | `bio` | `password`
2. `Shops`: `id` | `name` | `category` | `rating` | `reviewCount` | `bannerImage` | `isOpen` | `canteenZone` | `menus` | `qrCodeUrl` | `openTime` | `closeTime`
3. `Orders`: `id` | `queueNumber` | `studentName` | `shopName` | `totalAmount` | `status` | `createdAt` | `customerUsername` | `shopId` | `items` | `diningMode` | `tableNumber` | `estimatedReadyTime` | `prepDurationMin` | `slipImageUrl` | `cancelReason`
4. `Topups`: `id` | `username` | `name` | `amount` | `slipUrl` | `status` | `createdAt`
5. `Withdraws`: `id` | `username` | `name` | `role` | `amount` | `accountNo` | `bankName` | `status` | `slipUrl` | `createdAt`
6. `Transactions`: `id` | `username` | `type` (IN/OUT) | `amount` | `title` | `detail` | `createdAt`
7. `Reviews`: `id` | `shopId` | `userName` | `rating` | `comment` | `createdAt`

---

## ⚡ วิธีรันโปรเจกต์ (Quick Start)

```bash
# 1. ติดตั้ง dependencies
npm install

# 2. รันในโหมดพัฒนา
npm run dev

# 3. เปิดเว็บเบราว์เซอร์ที่:
http://localhost:3000 (หรือ 3001)
```

### การเชื่อมต่อกับ Google Sheets ของคุณ:
1. ดูคู่มือฉบับเต็มใน [`google-apps-script/README_GAS.md`](google-apps-script/README_GAS.md)
2. คัดลอกโค้ดจาก [`google-apps-script/Code.gs`](google-apps-script/Code.gs) ไปใส่ใน Apps Script
3. Deploy เป็น Web App (Who has access: **Anyone**)
4. นำ Web App URL มากดใส่ที่ปุ่ม **"ตั้งค่า Google Sheets"** (⚙️) ในหน้าเว็บ
