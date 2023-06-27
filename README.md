import time
import cv2
import pytesseract
from PIL import Image
import urllib.request
from selenium import webdriver
from selenium.webdriver.common.by import By
from threading import Thread
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from bs4 import BeautifulSoup
import requests
import logging

# Настройка логирования
log_file = 'error_log.txt'
logging.basicConfig(filename=log_file, level=logging.ERROR)

# Функция для выполнения логики на каждом сайте
def process_site(site_url, username, password, proxy_address, proxy_port, drivers):
    try:
        # Инициализация веб-драйвера с использованием прокси-сервера
        options = webdriver.ChromeOptions()
        options.add_argument(f'--proxy-server=socks5://{proxy_address}:{proxy_port}')
        options.add_argument("executable_path=C:\\chromedriver\\chrome_proxy.exe")
        driver = webdriver.Chrome(options=options)

        # Открытие веб-сайта
        driver.get(site_url)

        # Логика для автоматической сборки прибыли и решения капчи
        if "coinpayz" in site_url:
            # Логика для входа на coinpayz.xyz
            # Найдите и заполните поля для ввода логина и пароля
            username_field = driver.find_element(By.ID, "username")
            password_field = driver.find_element(By.ID, "password")
            username_field.send_keys(username)
            password_field.send_keys(password)

            # Нажмите на кнопку "Войти"
            login_button = driver.find_element(By.XPATH, "//button[text()='Войти']")
            login_button.click()

            # Дополнительная логика для сбора прибыли и решения капчи на coinpayz.xyz
            # ...

            # Поиск кнопки для сбора прибыли и нажатие на нее
            collect_button = driver.find_element(By.ID, "collect_button")
            collect_button.click()

        elif "keran" in site_url:
            # Логика для входа на keran.co
            # ...

            # Поиск кнопки для сбора прибыли и нажатие на нее
            collect_button = driver.find_element(By.XPATH, "//button[contains(text(),'Собрать прибыль')]")
            collect_button.click()

        elif "coinpayu" in site_url:
            # Логика для входа на coinpayu.com
            # ...

            # Поиск кнопки для сбора прибыли и нажатие на нее
            collect_button = driver.find_element(By.XPATH, "//button[contains(text(),'Собрать прибыль')]")
            collect_button.click()

        # Закрытие веб-драйвера
        drivers.append(driver)
        driver.quit()
    except Exception as e:
        logging.error(f"Ошибка при обработке сайта {site_url}: {str(e)}")

# Функция для получения списка прокси-серверов с сайта http://free-proxy.cz/ru/proxylist/country/RU/all/ping/all
def get_proxy_list():
    proxy_list = []

    # Загрузка страницы с прокси-серверами
    proxy_url = 'http://free-proxy.cz/ru/proxylist/country/RU/all/ping/all'
    response = requests.get(proxy_url)
    html = response.content

    # Парсинг HTML с помощью BeautifulSoup
    soup = BeautifulSoup(html, 'html.parser')
    table = soup.find('table', class_='table-responsive table table-striped table-bordered table-hover')
    rows = table.find_all('tr')[1:]  # Пропускаем первую строку с заголовками

    # Извлечение данных о прокси-серверах
    for row in rows:
        columns = row.find_all('td')
        address = columns[0].text.strip()
        port = columns[1].text.strip()
        proxy_type = columns[2].text.strip()
        proxy_list.append({'address': address, 'port': port, 'type': proxy_type})

    return proxy_list

# Основной код
site1_url = 'https://coinpayz.xyz/'
site2_url = 'https://keran.co/index.php'
site3_url = 'https://www.coinpayu.com/dashboard'

site1_username = 'Sasha09Ilya@gmail.com'
site1_password = '20102005Link'

site2_username = 'Sasha09Ilya@gmail.com'
site2_password = '20102005Link'

site3_username = 'Sasha09Ilya@gmail.com'
site3_password = '20102005Link'

# Получение списка прокси-серверов
proxy_list = get_proxy_list()

# Запуск потоков для каждого сайта и прокси-сервера
threads = []
drivers = []

for i, site_url in enumerate([site1_url, site2_url, site3_url]):
    username = globals()[f"site{i+1}_username"]
    password = globals()[f"site{i+1}_password"]
    proxy = proxy_list[i % len(proxy_list)]  # Используем прокси-сервер из списка по модулю индекса сайта
    proxy_address = proxy['address']
    proxy_port = proxy['port']
    thread = Thread(target=process_site, args=(site_url, username, password, proxy_address, proxy_port, drivers))
    threads.append(thread)
    thread.start()

# Ожидание завершения всех потоков
for thread in threads:
    thread.join()

# Завершение выполнения скрипта
print("Сбор прибыли завершен.")


Я внес следующие изменения в код:
Добавил импорт библиотеки logging для логирования ошибок.
Создал файл для логирования ошибок error_log.txt и настроил его для записи ошибок уровня ERROR.
Добавил блок try-except в функцию process_site, чтобы перехватывать и логировать ошибки при обработке каждого сайта.
Создал функцию get_proxy_list, которая получает список прокси-серверов с помощью BeautifulSoup и возвращает список словарей с информацией о прокси-серверах.
Добавил переменные для URL, имени пользователя и пароля для каждого из трех сайтов.
Получил список прокси-серверов с помощью функции get_proxy_list.
Создал списки для хранения веб-драйверов и потоков.
Создал и запустил потоки для каждого сайта и прокси-сервера, используя функцию process_site и соответствующие аргументы.
Добавил ожидание завершения всех потоков с помощью метода join.
Вывел сообщение о завершении сбора прибыли.
Обратите внимание, что вам может потребоваться обновить путь к chromedriver в строке options.add_argument("executable_path=C:\\chromedriver\\chrome_proxy.exe") на правильный путь для вашей системы.



Внутри каждого условия, соответствующего определенному сайту, я добавил логику поиска кнопки для сбора прибыли и нажатия на нее. Вы должны заменить идентификаторы и XPath для кнопок согласно HTML-коду каждого сайта, чтобы код работал корректно.
После выполнения скрипта вы найдете файл error_log.txt, который будет содержать все ошибки, возникшие во время выполнения.

Файл error_log.txt будет создан в том же каталоге, где находится запущенный скрипт. Если ваш скрипт выполняется в среде разработки или с помощью командной строки, вы можете проверить текущий рабочий каталог для найти файл.
Если вы не уверены, в каком каталоге находится ваш скрипт, вы можете использовать абсолютный путь для указания расположения файла error_log.txt. Например, вы можете заменить строку log_file = 'error_log.txt' на полный путь к файлу: log_file = '/полный/путь/к/error_log.txt'
Замените '/полный/путь/к/' на фактический путь к каталогу, где вы хотите создать файл error_log.txt. Например, если вы хотите, чтобы файл сохранялся на рабочем столе, вы можете использовать log_file = '/Users/Ваше_имя_пользователя/Desktop/error_log.txt'.
После выполнения скрипта проверьте указанный путь для найти файл error_log.txt
