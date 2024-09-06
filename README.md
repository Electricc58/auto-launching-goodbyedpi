# auto-launching-goodbyedpi
Бесит, когда приходится открывать goodbyedpi отдельно, когда заходишь на ютуб. Я с помощью нейросети perplexity написал код (сори, если много строк, я просто полный ноль в этом всём), который будет открывать GoodbyeDpi при заходе на сайт YouTube.

Перед всей процедурой обязательно установите [GoodbyeDpi]([https://pages.github.com/](https://github.com/ValdikSS/GoodbyeDPI)) на своё устройство.
Можете воспользоваться моим корявым методом, если вы чайник, как и я:
1. Создайте какую нибудь папку где угодно
2. Наведитесь в папке на любое пустое место и с зажатым шифтом нажмите ЛКМ. Далее нажмите на `Открыть окно Powershell здесь`. У вас должен быть скачан питон, не ниже 3.11
3. Впишите эту строчку и нажимайте enter
`pip install pygetwindow pystray Pillow psutil`
4. Создайте в этой папке файл 'my_script.txt'
5. Туда впишите этот код
````
import os
import time
import threading
import pygetwindow as gw
from PIL import Image, ImageDraw
import pystray
from pystray import MenuItem, Icon
import ctypes
import psutil

# Скрытие окна консоли
def hide_console():
    hwnd = ctypes.windll.kernel32.GetConsoleWindow()
    if hwnd:
        ctypes.windll.user32.ShowWindow(hwnd, 0)  # 0 = SW_HIDE

# Путь к файлу, который нужно открыть
file_path = "Ваш путь к файлу 1_russia_blacklist.cmd"  # Замените на ваш путь к файлу .cmd

def is_process_running(process_name):
    for proc in psutil.process_iter(['name']):
        try:
            if process_name.lower() in proc.info['name'].lower():
                return True
        except (psutil.NoSuchProcess, psutil.AccessDenied, psutil.ZombieProcess):
            pass
    return False

def open_file_if_youtube_active():
    while True:
        time.sleep(1)  # Проверяем каждую секунду
        # Получаем список всех открытых окон
        windows = gw.getAllTitles()
        # Проверяем, есть ли открытое окно YouTube
        if any("YouTube" in window for window in windows):
            if not is_process_running('goodbyedpi.exe'):  # Проверяем, запущен ли процесс
                try:
                    print(f"Открытие файла: {file_path}")  # Отладочное сообщение
                    os.startfile(file_path)  # Открываем файл
                except Exception as e:
                    print(f"Ошибка при открытии файла: {e}")
        else:
            # Если YouTube закрыт, ждем 10 секунд перед следующей проверкой
            time.sleep(10)

def create_image(width, height):
    image = Image.new('RGB', (width, height), color=(255, 255, 255))
    dc = ImageDraw.Draw(image)
    dc.rectangle((width // 4, height // 4, width * 3 // 4, height * 3 // 4), fill=(0, 0, 0))
    return image

def on_quit(icon, item):
    icon.stop()

def run_tray_icon():
    icon = Icon("test_icon", create_image(64, 64), "My Tray Icon", menu=pystray.Menu(
        MenuItem("Quit", on_quit)
    ))
    icon.run()

if __name__ == "__main__":
    hide_console()  # Скрываем окно консоли
    # Запускаем функцию в отдельном потоке
    threading.Thread(target=open_file_if_youtube_active, daemon=True).start()
    run_tray_icon()
````
и добавьте ваше расположение файла 1_russia_blacklist.cmd во второй решётке (#)

6. Сохраняйте и переименовывайте файл в my_script.py
Также советую воспользоваться пунктом 8
7. Добавьте ярлык этого файла в папку `D:\Roaming\Microsoft\Windows\Start Menu\Programs\Startup`

Если вдруг чё, я кину в [Release](https://github.com/Electricc58/auto-launching-goodbyedpi/releases) файл который у меня
