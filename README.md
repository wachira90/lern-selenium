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

## Hight Level 

ยอดเยี่ยมเลยครับ! เมื่อคุณคุ้นเคยกับพื้นฐานแล้ว การก้าวเข้าสู่ **ระดับสูง (Advanced Level)** จะเปลี่ยนจากการเขียนสคริปต์สั้นๆ ให้กลายเป็นการสร้างระบบอัตโนมัติที่มีความเสถียร จัดการกับเว็บที่ซับซ้อนได้ และมีโครงสร้างโค้ดแบบมืออาชีพครับ

เรามาดูเทคนิคระดับสูงที่คนทำงานกับ Selenium ต้องรู้กันครับ

---

### ขั้นตอนที่ 1: การใช้ Page Object Model (POM) เพื่อจัดโครงสร้างโค้ด

เมื่อโปรเจกต์ของคุณใหญ่ขึ้น การเขียนโค้ดหาปุ่มและสั่งคลิกปนกันไปหมดจะทำให้แก้ไขยากมาก (เช่น ถ้าเว็บเปลี่ยนชื่อ ID คุณต้องไปตามแก้โค้ดทุกบรรทัด)

**Page Object Model (POM)** เป็น Design Pattern ที่โปรแกรมเมอร์นิยมใช้ โดยแยก "การค้นหา Elements" ออกจาก "ลอจิกการทำงาน" อย่างชัดเจน

**ตัวอย่างการเขียนแบบ POM:**

```python
from selenium.webdriver.common.by import By

# 1. ไฟล์สำหรับเก็บ Elements และ Actions ของหน้านั้นๆ (เช่น login_page.py)
class LoginPage:
    def __init__(self, driver):
        self.driver = driver
        # กำหนด Locators ไว้ที่เดียว
        self.username_input = (By.ID, "user-name")
        self.password_input = (By.ID, "password")
        self.login_button = (By.ID, "login-button")

    def enter_username(self, username):
        self.driver.find_element(*self.username_input).send_keys(username)

    def enter_password(self, password):
        self.driver.find_element(*self.password_input).send_keys(password)

    def click_login(self):
        self.driver.find_element(*self.login_button).click()

# 2. ไฟล์สคริปต์หลักที่เรียกใช้งาน (main.py)
# driver = webdriver.Chrome()
# login_page = LoginPage(driver)
# login_page.enter_username("admin")
# login_page.enter_password("1234")
# login_page.click_login()

```

---

### ขั้นตอนที่ 2: การจำลองเมาส์และคีย์บอร์ดขั้นสูงด้วย ActionChains

บางครั้งการใช้ `.click()` ธรรมดาอาจไม่พอ เช่น คุณต้องการ "เอาเมาส์ไปชี้ค้างไว้ (Hover) เพื่อให้เมนูกางออก" หรือ "ลากแล้ววาง (Drag and Drop)" เราจะใช้คลาส `ActionChains` ครับ

```python
from selenium import webdriver
from selenium.webdriver.common.action_chains import ActionChains
from selenium.webdriver.common.by import By

driver = webdriver.Chrome()
driver.get("https://example.com")

# สร้าง Object ของ ActionChains
actions = ActionChains(driver)

# ตัวอย่างที่ 1: เอาเมาส์ไปชี้ (Hover) ที่เมนู แล้วคลิกเมนูย่อย
main_menu = driver.find_element(By.ID, "dropdown-menu")
sub_menu = driver.find_element(By.ID, "sub-menu-item")

actions.move_to_element(main_menu).click(sub_menu).perform()

# ตัวอย่างที่ 2: การลากแล้ววาง (Drag and Drop)
source = driver.find_element(By.ID, "draggable")
target = driver.find_element(By.ID, "droppable")

actions.drag_and_drop(source, target).perform()

```

> **จุดสำคัญ:** อย่าลืมใส่ `.perform()` ต่อท้ายเสมอ ไม่เช่นนั้นคำสั่งทั้งหมดจะไม่ถูกทำงานจริง

---

### ขั้นตอนที่ 3: การแทรกแซงด้วย JavaScript (Execute Script)

มีหลายกรณีที่ Selenium แบบปกติทำงานไม่ได้ เช่น ปุ่มถูกองค์ประกอบอื่นบังอยู่ (Element is not clickable at point) หรือต้องการดึงข้อมูลที่อยู่ใน Shadow DOM การสั่งรัน JavaScript ตรงๆ เข้าไปในเบราว์เซอร์คือไม้ตายสุดท้ายที่แก้ปัญหาได้แทบทุกอย่าง

```python
driver = webdriver.Chrome()
driver.get("https://example.com")

# ตัวอย่างที่ 1: การเลื่อนหน้าจอ (Scroll) ลงไปล่างสุด
driver.execute_script("window.scrollTo(0, document.body.scrollHeight);")

# ตัวอย่างที่ 2: บังคับคลิกปุ่มที่โดนบังอยู่ (ใช้ JS คลิกแทน)
hidden_button = driver.find_element(By.ID, "hidden-btn")
driver.execute_script("arguments[0].click();", hidden_button)

# ตัวอย่างที่ 3: ดึงค่าบางอย่างจากหน้าเว็บโดยใช้ JS
page_title = driver.execute_script("return document.title;")
print(page_title)

```

---

### ขั้นตอนที่ 4: การสลับไปจัดการ iFrame และหน้าต่างใหม่ (Window Handles)

เว็บสมัยใหม่มักมีการซ้อนเว็บอื่นไว้ในกรอบ (iFrame) หรือเมื่อคลิกแล้วเปิดแท็บใหม่ ถ้าคุณไม่สั่งให้ Selenium "ย้ายโฟกัส" มันจะหาปุ่มในหน้าต่างใหม่ไม่เจอเลย

**การย้ายไปแท็บใหม่:**

```python
# สมมติว่าคลิกลิงก์แล้วมีแท็บใหม่เด้งขึ้นมา
driver.find_element(By.ID, "open-new-tab").click()

# ดึงรายชื่อหน้าต่าง (Tabs) ทั้งหมดที่เปิดอยู่ (หน้าต่างล่าสุดจะอยู่ท้ายสุด)
windows = driver.window_handles

# สั่งสลับการควบคุมไปยังแท็บใหม่ (Index 1)
driver.switch_to.window(windows[1])
print("ตอนนี้เราอยู่ในแท็บใหม่แล้ว:", driver.title)

# หากต้องการกลับมาแท็บเดิม
# driver.switch_to.window(windows[0])

```

**การเจาะเข้าไปใน iFrame:**

```python
# ต้องสลับเข้าไปใน iFrame ก่อนถึงจะกดปุ่มที่อยู่ข้างในนั้นได้
iframe = driver.find_element(By.ID, "payment-frame")
driver.switch_to.frame(iframe)

# หลังจากทำธุระใน iFrame เสร็จ ต้องสลับกลับมาหน้าเว็บหลัก
driver.switch_to.default_content()

```

---

### ขั้นตอนที่ 5: การหลบหลีกระบบป้องกันบอท (Anti-Bot Bypass)

ในระดับสูง ปัญหาปวดหัวที่สุดคือเว็บไซต์จับได้ว่าเราใช้ Selenium (เช่น เจอหน้า Cloudflare หรือ reCAPTCHA กั้น) เพราะ Chrome Driver ปกติจะมีตัวแปรบางอย่างฝังมาเพื่อบอกว่าตัวมันคือบอท

เราจะใช้ไลบรารีทางเลือกที่ชื่อว่า `undetected-chromedriver` ซึ่งออกแบบมาเพื่อลบร่องรอยความเป็นบอทออกทั้งหมด

**การติดตั้ง:** `pip install undetected-chromedriver`

**วิธีใช้งาน:**

```python
import undetected_chromedriver as uc
import time

# ใช้ uc แทน webdriver แบบปกติ
options = uc.ChromeOptions()
options.add_argument("--disable-popup-blocking")

driver = uc.Chrome(options=options)
driver.get("https://nowsecure.nl/") # เว็บทดสอบระบบป้องกันบอท

time.sleep(10)
driver.quit()

```

---
