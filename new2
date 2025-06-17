from undetected_geckodriver import Firefox
from selenium.webdriver.common.by import By
from selenium.webdriver.common.action_chains import ActionChains
from selenium.webdriver.firefox.options import Options
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
import time
import json

# لیست سوالات
questions = [
    "hello",
    "message 2 hello"
]

qa_dict = {}
# لینک Share (اینو با لینک واقعی خودت عوض کن)
share_url = "https://chatgpt.com/share/xxx"  # لینک Share خودت رو بذار

# تنظیمات فایرفاکس
options = Options()
# بدون --headless برای باز شدن به صورت گرافیکی
options.set_preference("network.proxy.type", 0)  # غیرفعال کردن پراکسی
options.set_preference("dom.webdriver.enabled", False)  # مخفی کردن تشخیص وب‌درایور
options.set_preference("useAutomationExtension", False)  # غیرفعال کردن افزونه‌های خودکار
options.set_preference("general.useragent.override", 
                      "Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0")

try:
    # راه‌اندازی مرورگر
    driver = Firefox(options=options)
    driver.get(share_url)
    print(f"Opening Share link: {share_url}")

    # صبر برای لود باکس ورودی
    WebDriverWait(driver, 20).until(
        EC.presence_of_element_located((By.ID, "prompt-textarea"))
    )

    for q in questions:
        try:
            # پیدا کردن باکس ورودی
            input_box = WebDriverWait(driver, 10).until(
                EC.presence_of_element_located((By.ID, "prompt-textarea"))
            )
            actions = ActionChains(driver)
            actions.click(input_box).send_keys(q).perform()
            print(f"Question sent: {q}")

            # پیدا کردن و کلیک روی دکمه ارسال
            send_button = WebDriverWait(driver, 10).until(
                EC.element_to_be_clickable((By.CSS_SELECTOR, 'button[data-testid="send-button"]'))
            )
            send_button.click()

            # صبر برای لود پاسخ
            response_element = WebDriverWait(driver, 30).until(
                EC.presence_of_element_located((By.CSS_SELECTOR, 'div[data-message-author-role="assistant"] div.markdown.prose p'))
            )
            last_response = response_element.text
            qa_dict[q] = last_response
            print(f"Answer received: {last_response[:60]}...")

            time.sleep(2)  # فاصله کوتاه بین سوالات
        except Exception as e:
            qa_dict[q] = f"Error for question '{q}': {e}"
            print(f"Error for question '{q}': {e}")

    # ذخیره پاسخ‌ها تو فایل JSON
    with open("chatgpt_qa_output.json", "w", encoding="utf-8") as f:
        json.dump(qa_dict, f, ensure_ascii=False, indent=4)
    print("Answers saved to chatgpt_qa_output.json")

except Exception as e:
    print(f"Error: {e}")
finally:
    try:
        driver.quit()
        print("Browser closed")
    except:
        print("Browser not open")
