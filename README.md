import time
import cv2
import pytesseract
from PIL import Image
import urllib.request
from selenium import webdriver
from threading import Thread

# Функция для выполнения логики на каждом сайте
def process_site(site_url):
    # Инициализация веб-драйвера (выберите нужный веб-драйвер)
    driver = webdriver.Chrome(executable_path=r"C:\Users\саща\Desktop\chromedriver")

    # Открытие веб-сайта
    driver.get(site_url)

    # Логика для автоматической сборки прибыли и решения капчи
    while True:
        # Выполняйте ваш код для сбора прибыли здесь

        # Сбор прибыли
        # ...

        # Скачивание изображения капчи
        captcha_element = driver.find_element_by_xpath("//img[@class='captcha-image']")
        captcha_url = captcha_element.get_attribute("src")
        urllib.request.urlretrieve(captcha_url, "captcha.jpg")

        # Загрузка и обработка изображения капчи
        captcha_image = Image.open("captcha.jpg")
        captcha_image = captcha_image.convert("RGB")
        captcha_image.save("captcha_processed.jpg")
        captcha_image = cv2.imread("captcha_processed.jpg")

        # Извлечение текста с помощью OCR (оптическое распознавание символов)
        captcha_text = pytesseract.image_to_string(captcha_image)

        # Решение математического примера
        captcha_result = eval(captcha_text)

        # Ввод решения в поле капчи
        captcha_input = driver.find_element_by_xpath("//input[@id='captcha-input']")
        captcha_input.send_keys(str(captcha_result))

        # Отправка формы или выполнение других действий на сайте

        # Подождите 5 минут
        time.sleep(300)

    # Закрытие веб-драйвера
    driver.quit()

# Запуск процессов для каждого сайта
site1_url = 'https://coinpayz.xyz/?r=567742'
site2_url = 'https://keran.co/SOL/index.php'
site3_url = 'https://www.coinpayu.com/?r=Kr4ken'

site1_thread = Thread(target=process_site, args=(site1_url,))
site2_thread = Thread(target=process_site, args=(site2_url,))
site3_thread = Thread(target=process_site, args=(site3_url,))

# Запуск потоков
site1_thread.start()
site2_thread.start()
site3_thread.start()

# Ожидание завершения потоков
site1_thread.join()
site2_thread.join()
site3_thread.join()
