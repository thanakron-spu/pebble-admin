# pebble(a) · Admin log viewer

A single static page (`index.html`, no build step) that reads the robot's logs from
**ThingsBoard** (`demo.thingsboard.io`). demo.thingsboard.io blocks direct calls from other
websites (no CORS), so the page goes through the Pebble backend, which relays `/tb/api/*` to
ThingsBoard (`apps/api/src/tb-relay`). The default server is the backend's ngrok URL + `/tb`;
the backend (and ngrok) must be running for the page to work.

- **หุ่น Pebble**: what used to be the "pebble" page on the robot's screen: battery, ESP32 link (LED face / wheels), mic and wake word, speaker, Wi-Fi / IP, version, CPU temperature, plus the robot's events of the last 24 hours (CSV)
- **อุปกรณ์**: every device, online/offline, last activity / connect / disconnect
- **Telemetry**: every key the robot sends, by time range, as charts + a table
- **Alarms**: active and cleared alarms with severity
- **Export CSV** on every tab (opens in Excel with Thai text intact)

## Who can get in

You sign in with a **ThingsBoard account** (Tenant admin, or a Customer user for that
customer's devices). The site holds no passwords or keys: the ThingsBoard token is kept in the
browser's sessionStorage and is gone when the tab closes. Anyone without a ThingsBoard login sees
only the sign-in form. The page is marked `noindex` so search engines skip it.

Another ThingsBoard server can be chosen under "เซิร์ฟเวอร์" on the sign-in form.

## Hosting (GitHub Pages)

`.github/workflows/admin-pages.yml` publishes this folder on every push to `main` that changes it.

One-time setup on GitHub: **Settings → Pages → Build and deployment → Source: GitHub Actions**.
The site is then at `https://<user>.github.io/<repo>/` (here: `https://pebble265.github.io/pebble/`).

Pages on a **private** repository needs GitHub Pro (free with the GitHub Student Developer Pack).

## Trying it locally

```bash
cd apps/admin && python3 -m http.server 8091   # then open http://localhost:8091
```

## แท็บ "Log ผู้ใช้"

ดึงจาก backend Pebble (`GET /admin/audit`, ตาราง `audit_logs`) ไม่ใช่ ThingsBoard: ทุกครั้งที่ผู้ใช้หรือหุ่นเปลี่ยนข้อมูล
(เข้าสู่ระบบ, เพิ่ม/แก้/ลบยา, กินยา, ผู้ดูแล, ลบแชต, ลบบัญชี ฯลฯ) ทั้งที่สำเร็จและไม่สำเร็จ
ไม่เก็บค่าที่กรอก (รหัสผ่าน ชื่อยา ข้อความแชต) เก็บแค่ว่าแก้ช่องไหน

สิทธิ์ดู: backend ตรวจ token ThingsBoard ที่ล็อกอินอยู่ แล้วเช็กว่าอีเมลอยู่ใน `ADMIN_EMAILS` ของ `apps/api/.env`
(คั่นหลายคนด้วย `,`) ต้องรัน `database/migrations/012_audit_details.sql` ก่อนใช้
