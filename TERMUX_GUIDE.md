# คู่มือการรัน Gemini Web to API บน Termux (ภาษาไทย & English)

คู่มือนี้สำหรับผู้ใช้งานที่ต้องการรัน `gemini-web-to-api` บน Termux (Android) โดยเวอร์ชันนี้ได้รับการปรับปรุงให้รันได้ง่ายขึ้น แม้จะไม่มีคุกกี้ `__Secure-1PSIDTS` ก็ตาม

---

## 🌟 ฟีเจอร์ที่ปรับปรุงเพิ่มเติม (Custom Modifications)
1. **`__Secure-1PSIDTS` Optional**: ปลดล็อกการบังคับตรวจสอบคุกกี้ TS ตอนเริ่มต้นระบบ ทำให้สามารถกรอกเฉพาะ `__Secure-1PSID` แล้วให้ระบบไปใช้ฟังก์ชัน `RotateCookies()` เพื่อขอรับค่า TS จาก Google มาทำงานต่อโดยอัตโนมัติ
2. **Model Aliasing**: แมปชื่อโมเดล `gemini-3.1-pro` และ `gemini-3-pro` ให้ชี้ไปที่ตัวโมเดลหลังบ้านของ Google โดยตรง ช่วยให้ส่งคำสั่งจากบอท/เอเจนต์ต่างๆ (เช่น Hermes) เข้ามาประมวลผลได้อย่างราบรื่น

---

## 🛠️ ขั้นตอนการติดตั้งและใช้งาน (Installation & Run)

### ขั้นตอนที่ 1: เตรียมคุกกี้จากเบราว์เซอร์มือถือ
เนื่องจากการดึงคุกกี้ผ่านหน้าจอ F12 บนมือถือทำได้ยาก แนะนำให้ใช้ตัวช่วยดังนี้:
1. ติดตั้งเบราว์เซอร์ **Kiwi Browser** บนมือถือของคุณ
2. เข้าไปที่ Chrome Web Store ผ่าน Kiwi Browser และติดตั้งส่วนขยาย (Extension) **[Cookie-Editor](https://chromewebstore.google.com/detail/cookie-editor/hlkenndedecfkcjhhghjmacpdfccieib)**
3. เปิดไปที่เว็บ **[gemini.google.com](https://gemini.google.com)** และทำการเข้าสู่ระบบ (ล็อกอิน)
4. กดที่ปุ่ม **3 จุด** ของเบราว์เซอร์ Kiwi -> เลื่อนลงไปล่างสุด เลือกเปิด **`Cookie-Editor`**
5. มองหาคุกกี้ชื่อ **`__Secure-1PSID`** แล้วทำการคัดลอกค่า (Value) ของมันออกมา (หรือกด **Export -> Header String** เพื่อคัดลอกทั้งหมดแล้วแกะเอาเฉพาะค่าของตัวนี้)

### ขั้นตอนที่ 2: ตั้งค่าตัวแปรสภาพแวดล้อม (Environment Config)
สร้างไฟล์ `.env` ไว้ในโฟลเดอร์โปรเจกต์:
```bash
cp .env.example .env
```
แก้ไขข้อมูลในไฟล์ `.env` ดังนี้ (เว้นว่างค่า `GEMINI_1PSIDTS` ไว้ได้):
```env
PORT=4981
LOG_LEVEL=info
APP_ENV=development
RATE_LIMIT_ENABLED=true
RATE_LIMIT_WINDOW_MS=60000
RATE_LIMIT_MAX_REQUESTS=30

GEMINI_1PSID="ค่า_Secure-1PSID_ที่คุณคัดลอกมา"
GEMINI_1PSIDTS=""
GEMINI_REFRESH_INTERVAL=30
GEMINI_MAX_RETRIES=3
```
*ระบบจะดึงคุกกี้ `__Secure-1PSIDTS` มาใส่ในหน่วยความจำและเริ่มทำงานให้อัตโนมัติในวินาทีแรกที่รันเซิร์ฟเวอร์*

### ขั้นตอนที่ 3: สั่งรันเซิร์ฟเวอร์
หากใช้ Termux ให้ติดตั้ง Go compiler ก่อน:
```bash
pkg install -y golang
```
จากนั้นรันเซิร์ฟเวอร์:
```bash
go run cmd/server/main.go
```
เมื่อรันสำเร็จ หน้าจอจะแสดงพอร์ตที่เปิดใช้งาน (เริ่มต้นคือพอร์ต `4981`):
```text
INFO Server started on: http://127.0.0.1:4981
```

---

## 🔌 วิธีเชื่อมต่อใช้งาน (How to Connect)

### 1. ทดสอบใช้งานด้วย cURL
```bash
curl -X POST http://localhost:4981/openai/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{"model": "gemini-3.1-pro", "messages": [{"role": "user", "content": "สวัสดี! แนะนำตัวสั้นๆ"}]}'
```

### 2. สำหรับเชื่อมต่อเข้ากับ Hermes Agent
แก้ไขในไฟล์คอนฟิกหลักของ Hermes `~/.hermes/config.yaml`:
```yaml
model:
  default: gemini-3.1-pro
  provider: custom:gemini-local
providers:
  custom:gemini-local:
    name: gemini-local
    api_key: not-needed
    base_url: http://localhost:4981/openai/v1
    default_model: gemini-3.1-pro
    models:
      gemini-3.1-pro: {}
```
จากนั้นสั่งรีสตาร์ตบอท Hermes เพื่อเริ่มคุยผ่าน API ทันที!
```bash
pm2 restart hermes-gateway
```
