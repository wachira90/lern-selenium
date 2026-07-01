# Lerning selenium

**Selenium**! มันเป็นเครื่องมือที่ทรงพลังมากสำหรับการทำ Web Automation (ให้คอมพิวเตอร์เล่นเว็บแทนเรา) และการทำ Web Scraping (ดึงข้อมูลจากเว็บไซต์)

เพื่อให้เข้าใจง่ายและนำไปใช้งานได้จริง ผมจะขอใช้ภาษา **Python** ในการสอนนะ เพราะเป็นภาษาที่ได้รับความนิยมสูงสุดในการเขียน Selenium  เรามาเริ่มกันทีละขั้นตอนจากศูนย์ไปจนถึงระดับกลางกันเลย

---

### ขั้นตอนที่ 1: การเตรียมความพร้อมและติดตั้ง (Setup & Installation)

ก่อนอื่น คุณต้องมี Python ติดตั้งอยู่ในเครื่องก่อน จากนั้นให้เปิด Terminal (หรือ Command Prompt) แล้วพิมพ์คำสั่งเพื่อติดตั้งไลบรารีที่จำเป็น

```bash
pip install selenium

```

> **ข่าวดีสำหรับผู้เริ่มต้น:** ตั้งแต่ Selenium เวอร์ชั่น 4 ขึ้นไป ตัวระบบมี "Selenium Manager" มาให้ในตัว ซึ่งมันจะไปดาวน์โหลด Web Driver (เช่น ChromeDriver) ให้ตรงกับเวอร์ชั่นเบราว์เซอร์ของคุณโดยอัตโนมัติ คุณไม่ต้องไปโหลดไฟล์ `.exe` มาวางเองให้ปวดหัวอีกต่อไปแล้ว!

---

### ขั้นตอนที่ 2: เปิดเบราว์เซอร์และเข้าเว็บไซต์ (ระดับเบื้องต้น)

เรามาเริ่มเขียนโค้ด "Hello World" ของ Selenium กัน นั่นคือการสั่งเปิด Google ขึ้นมา แล้วปิดไป

```python
from selenium import webdriver
import time

# 1. สร้าง Driver เพื่อควบคุม Google Chrome
driver = webdriver.Chrome()

# 2. สั่งให้เปิดเว็บไซต์
driver.get("https://www.google.com")

# 3. ดึงชื่อ Title ของหน้าเว็บมาแสดงผล
print("เปิดหน้าเว็บ:", driver.title)

# หยุดพัก 3 วินาทีให้เรามองทัน (ในการใช้งานจริงไม่ควรใช้ time.sleep เยอะๆ )
time.sleep(3)

# 4. ปิดเบราว์เซอร์
driver.quit()

```

---

### ขั้นตอนที่ 3: การค้นหาปุ่มหรือช่องกรอกข้อความ (Locators)

เพื่อให้บอทของเราคลิกหรือพิมพ์ข้อความได้ เราต้องบอกมันก่อนว่า "ปุ่มนั้นอยู่ตรงไหน" เราจะใช้คลาสที่ชื่อว่า `By` ในการค้นหา

วิธียอดฮิตในการหา Elements:

* `By.ID` : หาจากแอตทริบิวต์ id (เร็วและแม่นยำที่สุด)
* `By.NAME` : หาจากแอตทริบิวต์ name
* `By.CLASS_NAME` : หาจากชื่อ class
* `By.XPATH` : หาจากโครงสร้าง HTML (ยืดหยุ่นที่สุด ใช้ได้กับทุกอย่าง แต่จะทำงานช้ากว่านิดหน่อย)

---

### ขั้นตอนที่ 4: การโต้ตอบกับหน้าเว็บ (Interactions)

เมื่อหาเจอแล้ว เราสามารถสั่ง `.click()` เพื่อคลิก หรือ `.send_keys()` เพื่อพิมพ์ข้อความได้ มาลองเขียนบอทค้นหาข้อมูลใน Google กัน

```python
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.common.keys import Keys
import time

driver = webdriver.Chrome()
driver.get("https://www.google.com")

# 1. หาช่องค้นหาของ Google (ช่องค้นหาของ Google มีชื่อ name ว่า "q")
search_box = driver.find_element(By.NAME, "q")

# 2. พิมพ์คำว่า "Selenium Python" ลงไป
search_box.send_keys("Selenium Python")

# 3. จำลองการกดปุ่ม Enter บนคีย์บอร์ด
search_box.send_keys(Keys.ENTER)

time.sleep(5)
driver.quit()

```

---

### ขั้นตอนที่ 5: การรออย่างชาญฉลาด (Waits) - ก้าวสู่ระดับกลาง

การใช้ `time.sleep(5)` เป็นสิ่งที่ไม่ควรทำในการเขียนโปรแกรมระดับกลาง เพราะมันบังคับให้โปรแกรมหยุด 5 วินาทีเสมอ แม้ว่าเว็บจะโหลดเสร็จตั้งแต่ 1 วินาทีแรกก็ตาม ทำให้บอทของเราทำงานช้ามาก

เราควรใช้ **Explicit Wait** คือบอกให้บอท "รอจนกว่าปุ่มนี้จะโผล่มา ถ้าโผล่มาแล้วก็ทำงานต่อเลยไม่ต้องรอให้หมดเวลา"

```python
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC

driver = webdriver.Chrome()
driver.get("https://www.google.com")

try:
    # รอสูงสุด 10 วินาที จนกว่าช่องค้นหา (name="q") จะพร้อมให้พิมพ์
    search_box = WebDriverWait(driver, 10).until(
        EC.presence_of_element_located((By.NAME, "q"))
    )
    
    search_box.send_keys("การใช้ Explicit Wait Selenium")
    search_box.send_keys(Keys.ENTER)
    
    # รอจนกว่าผลลัพธ์การค้นหาจะแสดงขึ้นมา (สมมติว่าหา ID ชื่อ search)
    results = WebDriverWait(driver, 10).until(
        EC.presence_of_element_located((By.ID, "search"))
    )
    print("โหลดผลลัพธ์เสร็จเรียบร้อย!")

finally:
    driver.quit()

```

---

### ขั้นตอนที่ 6: การรันแบบซ่อนหน้าจอ (Headless Mode)

ในระดับกลาง เมื่อโค้ดของคุณสมบูรณ์แล้ว คุณคงไม่อยากให้หน้าต่าง Chrome เด้งขึ้นมากวนใจทุกครั้งที่รันบอท คุณสามารถตั้งค่าให้มันทำงานแบบ **Headless (ไร้หน้าจอ)** ได้ (ทำงานอยู่เบื้องหลัง)

```python
from selenium import webdriver
from selenium.webdriver.chrome.options import Options

# สร้าง Object สำหรับตั้งค่า
chrome_options = Options()
# เปิดโหมดไร้หน้าจอ
chrome_options.add_argument("--headless=new") 
# แนะนำให้ใส่เพิ่มเพื่อป้องกัน error บนบางระบบ
chrome_options.add_argument("--disable-gpu") 
chrome_options.add_argument("--window-size=1920,1080")

# นำการตั้งค่าไปใส่ใน Driver
driver = webdriver.Chrome(options=chrome_options)

driver.get("https://www.python.org")
print("Title ของเว็บคือ:", driver.title) # จะพิมพ์ผลลัพธ์ออกมา โดยไม่มีหน้าจอเด้งขึ้นมาเลย

driver.quit()

```

---
