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



xplanation
Set Mend User Secrets Step: 

This step uses conditional logic to determine which set of secrets should be used based on the github.actor.
If the actor is specific-user, it assigns MEND_EMAIL_SPECIFIC_USER and MEND_USER_KEY_SPECIFIC_USER to the environment variables MEND_EMAIL and MEND_USER_KEY.
Otherwise, it defaults to MEND_EMAIL_DEFAULT and MEND_USER_KEY_DEFAULT.
Environment Variables: 

After setting the environment variables via $GITHUB_ENV, they are used in the Mend CLI Scan step by referencing ${{ env.MEND_EMAIL }} and ${{ env.MEND_USER_KEY }}.
Secrets Management:

Make sure to define the four secrets (MEND_EMAIL_SPECIFIC_USER, MEND_USER_KEY_SPECIFIC_USER, MEND_EMAIL_DEFAULT, and MEND_USER_KEY_DEFAULT) in the GitHub repository’s secrets configuration to ensure they are securely stored and available to the workflow.
Customization:

Replace specific-user with the actual GitHub username of the specific user for whom you want to assign the special secrets. Adjust the secret names accordingly to match what you've set up in your GitHub settings.



