Step 1: Set Up the GitHub Repository
Create a new repository on GitHub.
Add your Python script in this repository.


<><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><>

Create a workflow with GitHub Actions to run the script twice per day.
Step 2: Script to Scrape Yad2


import time
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.chrome.service import Service
from webdriver_manager.chrome import ChromeDriverManager
import telegram  # For Telegram messaging

# Set up the bot token and chat ID (Telegram)
TELEGRAM_BOT_TOKEN = 'YOUR_TELEGRAM_BOT_TOKEN'
TELEGRAM_CHAT_ID = 'YOUR_CHAT_ID'

# Function to send messages via Telegram
def send_telegram_message(message):
    bot = telegram.Bot(token=TELEGRAM_BOT_TOKEN)
    bot.send_message(chat_id=TELEGRAM_CHAT_ID, text=message)

# Set up the Yad2 scraping with Selenium
def scrape_yad2(area, min_rooms, max_rooms, max_budget):
    url = f'https://www.yad2.co.il/realestate/rent?area={area}&minprice=0&maxprice={max_budget}&minrooms={min_rooms}&maxrooms={max_rooms}'
    
    driver = webdriver.Chrome(service=Service(ChromeDriverManager().install()))
    driver.get(url)
    time.sleep(3)  # Let the page load
    
    # Scrape the listing information
    listings = driver.find_elements(By.CLASS_NAME, "feeditem")
    results = []
    
    for listing in listings:
        title = listing.find_element(By.CLASS_NAME, 'title').text
        price = listing.find_element(By.CLASS_NAME, 'price').text
        location = listing.find_element(By.CLASS_NAME, 'subtitle').text
        link = listing.find_element(By.TAG_NAME, 'a').get_attribute('href')
        results.append(f"{title}\n{price}\n{location}\n{link}")
    
    driver.quit()
    
    return results

# Main function
def main():
    area = 'AREA_CODE'  # Replace with the specific area code you are targeting
    min_rooms = 2  # Minimum rooms
    max_rooms = 4  # Maximum rooms
    max_budget = 5000  # Max budget in ILS
    
    results = scrape_yad2(area, min_rooms, max_rooms, max_budget)
    
    if results:
        message = "\n\n".join(results)
        send_telegram_message(message)
    else:
        send_telegram_message("No new listings found.")
        
if __name__ == "__main__":
    main()

<><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><>
Step 3: Set Up GitHub Actions to Run Twice a Day
Create a .github/workflows/schedule.yml file in your repository:

name: Scrape Yad2 and Send to Telegram

on:
  schedule:
    - cron: '0 9,21 * * *'  # Run at 9 AM and 9 PM UTC

jobs:
  scrape_and_send:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout repository
      uses: actions/checkout@v2

    - name: Set up Python
      uses: actions/setup-python@v2
      with:
        python-version: '3.x'

    - name: Install dependencies
      run: |
        pip install selenium
        pip install webdriver-manager
        pip install python-telegram-bot

    - name: Run the scraping script
      run: python script.py

<><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><>


Step 4: Set Up Telegram Bot
Create a bot using BotFather on Telegram.
Get the bot token from BotFather and insert it into the TELEGRAM_BOT_TOKEN variable in the script.
To get your chat ID, send a message to your bot, and then visit https://api.telegram.org/bot<YOUR_BOT_TOKEN>/getUpdates to find your chat ID.

<><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><>



Step 5: Optional - Signal Setup
For Signal, you can use a library like signal-cli to send messages. Here's a basic overview of how that might look:

Install signal-cli and configure it.
Replace the Telegram messaging part of the script with Signal’s message sending command via a subprocess call.
Summary
Python script scrapes Yad2 for listings.
Script runs twice daily using GitHub Actions.
Output is sent via Telegram or Signal to your phone.
Let me know if you need further details, especially on Signal integration!
