from selenium import webdriver
from selenium.webdriver.chrome.service import Service
from selenium.webdriver.common.by import By
from webdriver_manager.chrome import ChromeDriverManager
import pandas as pd
import time

driver = webdriver.Chrome(
    service=Service(ChromeDriverManager().install())
)

driver.get("https://www.imdb.com/search/title/?title_type=feature&languages=ta")
time.sleep(5)

movies = driver.find_elements(
    By.XPATH,
    "//li[contains(@class,'ipc-metadata-list-summary-item')]"
)

data = []

for movie in movies:
    try:
        text = movie.text.split("\n")

        title = text[0]

        year = ""
        rating = ""

        for item in text:
            if len(item) == 4 and item.isdigit():
                year = item

            try:
                value = float(item)
                if value <= 10:
                    rating = item
            except:
                pass

        data.append([title, year, rating])

    except Exception as e:
        print(e)

df = pd.DataFrame(
    data,
    columns=["Movie Name", "Year", "IMDb Rating"]
)

df.to_csv("tamil_movies.csv", index=False)

print(df.head())
print("Total Tamil Movies:", len(df))

input("Press Enter to close browser...")
driver.quit()
