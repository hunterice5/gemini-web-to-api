<p align="center">
  <img src="assets/gemini.png" width="400" alt="Gemini Logo">
</p>

<p align="center">
  <b>ภาษา / Languages: <a href="README.md">English</a> | <a href="README.th.md">ภาษาไทย</a></b>
</p>

<h1 align="center">Gemini Web To API 🚀 (เวอร์ชันภาษาไทย)</h1>

<p align="center">
  เครื่องมือแปลงหน้าเว็บ Google Gemini (Web Interface) ให้กลายเป็น REST API มาตรฐาน<br/>
  เข้าถึงพลังของ Gemini ได้โดยไม่ต้องใช้ API Key — ใช้แค่คุกกี้ของคุณเท่านั้น!
</p>

> [!NOTE]
> โปรเจกต์นี้จัดทำขึ้นเพื่อ **การศึกษาและวิจัยเท่านั้น** โปรดใช้งานอย่างรับผิดชอบและห้ามใช้ในเชิงพาณิชย์

> [!WARNING]
> โปรเจกต์นี้ไม่ได้เกี่ยวข้องหรือได้รับการสนับสนุนจาก Google เป็นการดึงค่าคุกกี้เพื่อทำระบบจำลอง อาจจะไม่เป็นไปตามข้อตกลงการใช้งานของ Google โปรดรับความเสี่ยงด้วยตัวคุณเอง

---

## 🎯 ทำไมต้องใช้ Gemini Web To API?

**ปัญหา**: คุณต้องการใช้โมเดลล่าสุดของ Google Gemini แต่อาจจะไม่มีสิทธิ์ใช้ API Key หรือต้องการใช้หน้าเว็บที่รองรับ Gemini Advanced

**ทางออก**: จำลองเซิร์ฟเวอร์ขึ้นมาในเครื่องเพื่อ:
- ✅ เชื่อมต่อหน้าเว็บ Gemini โดยใช้คุกกี้จากเบราว์เซอร์ของคุณ
- ✅ ให้บริการ API ปลายทางที่รองรับรูปแบบ OpenAI / Claude / Gemini
- ✅ รองรับการใช้งานบนระบบจำลองและบอทเอเจนต์ต่างๆ (เช่น Hermes)
- ✅ จัดการการหมุนเวียนคุกกี้ (Cookie Rotation) เพื่อคงเซสชันการทำงานเบื้องหลังโดยอัตโนมัติ

---

## ⚡ เริ่มต้นใช้งานอย่างรวดเร็ว (สำหรับ Termux และทั่วไป)

### 🛠️ การติดตั้งและรันจากโค้ด (Build from source)

**ขั้นตอนที่ 1 — คัดลอกโปรเจกต์**
```bash
git clone https://github.com/hunterice5/gemini-web-to-api.git
cd gemini-web-to-api
```

**ขั้นตอนที่ 2 — การเตรียมคุกกี้**
1. เปิดเบราว์เซอร์ของคุณ (เช่น Kiwi Browser หากทำบนมือถือ) และล็อกอินเข้าเว็บ **[gemini.google.com](https://gemini.google.com)**
2. ติดตั้งส่วนขยาย **[Cookie-Editor](https://chromewebstore.google.com/detail/cookie-editor/hlkenndedecfkcjhhghjmacpdfccieib)**
3. เปิดไปที่หน้าเว็บ Gemini แล้วคลิกเปิดส่วนขยาย Cookie-Editor
4. คัดลอกค่าของคุกกี้ชื่อ **`__Secure-1PSID`** (ค่าจะขึ้นต้นด้วย `g.a...`)
5. สร้างไฟล์ชื่อ `.env` ขึ้นมาจากไฟล์ตัวอย่าง:
   ```bash
   cp .env.example .env
   ```
6. นำค่าคุกกี้ที่คัดลอกมาใส่ในไฟล์ `.env` (คุณสามารถเว้นว่าง `GEMINI_1PSIDTS` ไว้ได้ เนื่องจากระบบเวอร์ชันนี้จะดึงค่า TS ล่าสุดจาก Google ให้อัตโนมัติเมื่อเริ่มต้นรัน):
   ```env
   GEMINI_1PSID="ค่า_Secure-1PSID_ของคุณ"
   GEMINI_1PSIDTS=""
   GEMINI_REFRESH_INTERVAL=30
   GEMINI_MAX_RETRIES=3
   ```

**ขั้นตอนที่ 3 — สั่งรันโปรแกรม**
หากใช้งานบน Termux (Android) ให้ติดตั้ง Go Compiler ก่อน:
```bash
pkg install -y golang
```
จากนั้นสั่งรันด้วยคำสั่ง:
```bash
go run cmd/server/main.go
```
เมื่อเริ่มทำงานสำเร็จ หน้าจอจะแสดงข้อความพร้อมใช้งานบนพอร์ต `4981`:
```text
INFO Server started on: http://127.0.0.1:4981
```

---

## 🔌 วิธีการนำไปใช้งาน (Usage Examples)

### การทดสอบด้วย cURL
```bash
curl -X POST http://localhost:4981/openai/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{"model": "gemini-3.1-pro", "messages": [{"role": "user", "content": "สวัสดี! แนะนำตัวสั้นๆ"}]}'
```

### การตั้งค่าร่วมกับบอท Hermes Agent
คุณสามารถนำไปผูกในไฟล์ค่าตั้งต้นของ Hermes (`~/.hermes/config.yaml`) เพื่อใช้งานโมเดล `gemini-3.1-pro` ได้ทันที:
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
จากนั้นสั่งรีสตาร์ต Hermes เพื่อเริ่มทำงาน:
```bash
pm2 restart hermes-gateway
```

---

## 📘 เอกสารคู่มือระบบ (API Documentation)
เมื่อรันเซิร์ฟเวอร์เรียบร้อยแล้ว คุณสามารถเข้าศึกษา API Endpoints ทั้งหมดได้จากหน้าเว็บจำลองผ่านเอกสารประกอบของ Scalar ได้ที่:
👉 **`http://localhost:4981/docs`**
