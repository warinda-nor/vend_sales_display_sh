# Showroom Display Performance — Tableau Extension

Dashboard extension สำหรับวัด Sales Performance ในโชว์รูมว่ายอดขายที่เกิดขึ้นมาจากสินค้าที่มีตัวโชว์ (Display) หรือไม่มีตัวโชว์ (Non-Display) — KPI รวม, Sales by Sales Office, Sales by MCH2, Top 10 Article แยก Display/Non-Display พร้อมรูปสินค้า, ตัวกรอง Year/Month ในตัว extension เอง, ปุ่ม Download (CSV)/View table (แสดงในการ์ดแทน popup) ต่อการ์ด, และปุ่มดาวน์โหลดรวมเป็นไฟล์ Excel เดียว (5 sheet รวม Detail รายธุรกรรม) ที่หัว Dashboard

## โครงสร้างไฟล์

```
display-performance/
  index.html                          ไฟล์หลักของ extension (HTML + CSS + JS ในไฟล์เดียว)
  ShowroomDisplayPerformance.trex      ไฟล์ manifest สำหรับให้ Tableau รู้จัก extension นี้
tableau.extensions.1.latest.js        Tableau Extensions API (index.html เรียกใช้ไฟล์นี้)
```

> **หมายเหตุ:** ไฟล์ `.xlsx`/`.xls`/`.csv` (ข้อมูลขายจริง) และโฟลเดอร์ `preview/` (มีข้อมูลจริงฝังอยู่ในไฟล์ static HTML สำหรับตรวจ layout บนเครื่องเท่านั้น) ถูกกันไว้ใน `.gitignore` ไม่ขึ้น GitHub เพราะเป็นข้อมูลภายใน ไม่จำเป็นต่อการรันตัว extension

> **`display-performance/index.html` ไม่มีข้อมูลตัวอย่าง/mock data ฝังอยู่เลย** — ไฟล์นี้จะแสดงผลได้ก็ต่อเมื่อรันอยู่ภายใน Tableau dashboard จริงเท่านั้น (ดึงข้อมูลสดจาก worksheet ตามสเปกในข้อ 3) ถ้าเปิดไฟล์ตรงๆ ด้วยเบราว์เซอร์จะเห็นแค่กรอบ layout เปล่าๆ กับ error banner ว่า Extensions API เริ่มต้นไม่ได้ — ใช้เช็คแค่โครงสร้าง/การจัดวางเท่านั้น ไม่มีตัวเลขให้ดู

---

## 1) เช็คโครงสร้าง Layout (ยังไม่ต้องต่อ Tableau)

เปิดไฟล์ `display-performance/index.html` ตรงๆ ด้วยเบราว์เซอร์ (ดับเบิลคลิก หรือลากไฟล์เข้าเบราว์เซอร์) — จะเห็น error banner แจ้งว่า Extensions API เริ่มต้นไม่ได้ ("not running inside an iframe, desktop, or popup window") เพื่อยืนยันว่าไฟล์โหลดไม่มี error อื่น

ถ้าต้องการดูข้อมูลจริงต้องเปิดผ่าน Tableau ตามข้อ 3

---

## 2) Deploy ขึ้น GitHub Pages

ขั้นตอนนี้ทำครั้งเดียวเพื่อให้ Tableau (ซึ่งต้องโหลด extension จาก URL แบบ `https://`) เข้าถึงไฟล์ `index.html` ได้

1. เข้า repo บน GitHub: `https://github.com/warinda-nor/vend_sales_display_sh`
2. ไปที่ **Settings → Pages**
3. ที่ **Source** เลือก **Deploy from a branch**
4. เลือก Branch เป็น **main** และ Folder เป็น **/ (root)** แล้วกด **Save**
5. รอ 1–2 นาที ให้ GitHub Pages build เสร็จ แล้วเข้าไปเช็คที่:
   ```
   https://warinda-nor.github.io/vend_sales_display_sh/display-performance/index.html
   ```
   ถ้าเห็น error banner ว่า Extensions API เริ่มต้นไม่ได้ แปลว่า deploy สำเร็จ (ต้องเปิดผ่าน Tableau ถึงจะเห็นข้อมูลจริง)

> URL ด้านบนต้องตรงกับค่าที่อยู่ใน `display-performance/ShowroomDisplayPerformance.trex` (แท็ก `<source-location><url>`) เป๊ะๆ — ถ้าเปลี่ยนชื่อ repo หรือ path ต้องแก้ในไฟล์ `.trex` ให้ตรงกันด้วย

---

## 3) ติดตั้งใช้งานใน Tableau Desktop

1. เปิด Tableau Desktop แล้วเปิด Dashboard ที่ต้องการใส่ extension
2. ลาก object **Extension** จากแผง Objects มาวางในตำแหน่งที่ต้องการ
3. เลือก **My Extensions → Access Local Extensions** แล้วเลือกไฟล์ `display-performance/ShowroomDisplayPerformance.trex`
   (หรือถ้า deploy ผ่าน GitHub Pages แล้ว จะสามารถแชร์ไฟล์ `.trex` นี้ให้คนอื่นใช้ได้เลยโดยไม่ต้องมีไฟล์ index.html อยู่ในเครื่อง เพราะ extension จะไปโหลดจาก URL บน GitHub Pages โดยตรง)
4. Dashboard ต้องมี Worksheet อย่างน้อย 1 ตัว ที่ grain เป็นรายแถวธุรกรรม (ไม่ aggregate) ประกอบด้วย field ต่อไปนี้ — **ตั้งชื่อ Worksheet เป็นอะไรก็ได้ตามใจ** เพราะ extension จะดูจาก field `article_id` เพื่อระบุว่านี่คือ worksheet ที่ต้องใช้ (ไม่ได้ดูจากชื่อ worksheet)
5. การเลือกเดือนทำได้ 2 ชั้น: (ก) ถ้ามี Filter ของ Tableau เองบน dashboard (เช่นบน field `end_of_month`) extension จะเห็นเฉพาะข้อมูลที่ผ่าน filter นั้นมาแล้ว และ (ข) ตัวกรอง **Year / Month ในหัว extension เอง** (มุมขวาบน) จะกรองซ้ำอีกชั้นจากข้อมูลที่ได้รับมา — ใช้อันไหนอันเดียว หรือทั้งสองอันร่วมกันก็ได้ เปลี่ยน filter ฝั่งไหนก็ตาม extension จะ refresh ให้เองอัตโนมัติ (ฟัง event `SummaryDataChanged`)
   > **สำคัญ**: field `end_of_month` ที่ลากลง worksheet ต้อง**เป็นวันที่แบบเต็ม (exact date)** ไม่ใช่ date part ที่ตัดเหลือแค่ "Month" (เช่น กด custom เป็น discrete "January") — เพราะตัวกรอง Year/Month ของ extension อ่านค่าปีจากตัววันที่จริงด้วย ถ้าลากเป็น date part ที่ไม่มีปีติดมา ตัวกรองจะอ่านปีผิดหรือกรองไม่ได้
6. ถ้า field ที่ต้องใช้ขาดไป extension จะโชว์ banner สีแดงบอกชื่อ field ที่ขาดแบบเจาะจง ไม่ใช่หน้าจอเปล่าๆ — ให้แก้ชื่อ field ใน Tableau (หรือแก้ค่าคงที่ `REQUIRED_FIELDS` ใน `index.html`) ให้ตรงกัน

### สเปก field ที่ Worksheet ต้องมี

| Field ใน Tableau | ใช้ทำอะไร |
|---|---|
| `article_id` | ใช้ระบุว่านี่คือ worksheet ที่ต้องใช้ / นับ SKU ต่อบทความในตาราง Top 10 |
| `article_name` | ชื่อสินค้าในตาราง Top 10 Article |
| `brand` | คอลัมน์ Brand ในตาราง Top 10 Article |
| `vendor_id`, `vendor_name` | การ์ด Vendor |
| `branch` | Sales by Sales Office |
| `sls_grp_desc` | Sales Channel |
| `mch3_desc`, `mch2_desc`, `mch1_desc`, `mch_desc` | Sales by MCH2 ใช้ `mch2_desc` โดยตรง ตัวอื่นเก็บไว้เผื่อขยายภายหลัง |
| `flag_display_stk` | ใช้แบ่ง Display/Non-Display สำหรับการ์ด **Sales by Sales Office** เท่านั้น |
| `display_comp` | ใช้แบ่ง Display/Non-Display สำหรับ **KPI, Sales by MCH2, Top 10 Article** — field นี้กับ `flag_display_stk` ให้ผลไม่ตรงกันในข้อมูลจริงส่วนใหญ่ เป็นความตั้งใจ ไม่ใช่บั๊ก |
| `sale_qty` | Sales Qty ในทุกการ์ด / ใช้ rank Top 10 เมื่อเปิด toggle "Rank by Sales Qty" |
| `net_inc_tax` | Net Sales ในทุกการ์ด |
| `end_of_month` | ใช้ให้ตัวกรอง Year/Month ในหัว extension ทำงาน และใช้เป็น field สำหรับ Tableau Filter เลือกเดือนได้ด้วย (ดูข้อ 5) |
| `fv_sale_per_months` | ไม่ได้ใช้คำนวณอะไรในการ์ดไหน แต่ export ออกไปใน sheet "Detail Sales Display" ของไฟล์ Excel ที่ดาวน์โหลด |

> ชื่อ field ต้องตรงกับในตาราง **เป๊ะๆ** (ตรงตามค่าคงที่ `REQUIRED_FIELDS` ท้ายไฟล์ `index.html`) ถ้าใน data source ใช้ชื่อคอลัมน์ต่างจากนี้ ให้แก้ค่าในตัวแปรนี้ให้ตรงกับ data source จริง

---

## Download เป็น Excel (5 sheet)

ไอคอนดาวน์โหลดที่หัว extension (ข้างตัวกรอง Year/Month) จะสร้างไฟล์ `.xlsx` เดียวที่มี 5 sheet โดยข้อมูลทุก sheet ตรงกับตัวกรอง Year/Month ที่เลือกอยู่ ณ ตอนนั้น:

| Sheet | เนื้อหา |
|---|---|
| Sales by Sales Office | เหมือนตารางในการ์ด (Display/Non-Display/Total ต่อสาขา) |
| Sales by MCH2 | เหมือนตารางในการ์ด (Display/Non-Display/Total ต่อ MCH2) |
| Top10 Display | Top 10 article ฝั่ง Display ตามโหมด rank (Net Sales/Sales Qty) ที่การ์ดเปิดอยู่ตอนนั้น |
| Top10 Non-Display | เหมือนกันแต่ฝั่ง Non-Display |
| Detail Sales Display | ข้อมูล**รายธุรกรรมดิบ** (ไม่รวมยอด) ครบทุกคอลัมน์ตาม `REQUIRED_FIELDS` |

ต้องมีอินเทอร์เน็ตตอนใช้งาน เพราะไลบรารีสร้างไฟล์ Excel (SheetJS) โหลดจาก CDN (`cdnjs.cloudflare.com`)

ปุ่ม Download (ไอคอนเดี่ยว) และ View table บนแต่ละการ์ด (Sales by Sales Office, Sales by MCH2) ยังใช้แยกเฉพาะการ์ดนั้นได้ตามปกติ — View table จะสลับมาแสดงตารางแทนกราฟ**ในการ์ดเดิม** ไม่ได้เด้ง popup

---

## ข้อจำกัดที่ควรรู้

- **ไม่มี Parameter ที่ต้องสร้าง** — extension ไม่อ่าน Parameter ใดๆ เลย การเลือกช่วงเวลาทำผ่าน Tableau Filter ปกติ และ/หรือตัวกรอง Year/Month ในตัว extension เอง (ดูข้อ 5 ในหัวข้อติดตั้ง)
- **Display/Non-Display แยก field กันตามการ์ด โดยตั้งใจ**: `flag_display_stk` ขับ Sales by Sales Office ส่วน `display_comp` ขับ KPI/Sales by MCH2/Top 10 Article — ถ้าจะเปลี่ยนให้ทุกการ์ดอ่าน field เดียวกัน ต้องแก้ทั้งใน `aggregateRows()` ของ `index.html` และเอกสารนี้ให้ตรงกัน
- field ที่เป็นตัวเลข (measure) ถ้าถูกลากขึ้น shelf แบบ aggregate จะได้ fieldName กลับมาเป็น `AGG(ชื่อ field)` ไม่ใช่ชื่อ field เพียวๆ — extension ตัดคำห่อนี้ให้อัตโนมัติแล้ว (`normalizeFieldName`) ไม่ต้องแก้อะไรฝั่ง Tableau
- extension ฟัง event `SummaryDataChanged` ของ worksheet อยู่แล้ว เปลี่ยน filter/เปลี่ยนเดือนบน dashboard ข้อมูลจะ refresh ให้อัตโนมัติโดยไม่ต้องปิด-เปิด extension ใหม่
- การหา worksheet ที่ถูกต้อง (ที่มี field `article_id`) ใช้ `getSummaryColumnsInfoAsync()` เช็คแค่รายชื่อ column เท่านั้น ไม่ได้โหลดข้อมูลทุกแถวของทุก worksheet บน dashboard มาเช็ค — เร็วกว่าและไม่กระทบ worksheet อื่นที่ไม่เกี่ยวข้อง
- ทุกครั้งที่แก้ `display-performance/index.html` แล้ว push ขึ้น GitHub ต้องรอ GitHub Pages build ใหม่ (ปกติ 1–2 นาที) ก่อนที่ Tableau จะเห็นเวอร์ชันล่าสุด — ถ้าไม่เห็นการเปลี่ยนแปลง ให้ลอง hard refresh หรือปิด-เปิด dashboard ใหม่
