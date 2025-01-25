import requests
import smtplib
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart
import re

# Constants
WEATHER_API_KEY = "your_openweathermap_api_key"  # Replace with your OpenWeatherMap API key
EMAIL_ADDRESS = "your_email@gmail.com"          # Replace with your email
EMAIL_PASSWORD = "your_email_password"          # Replace with your email password
SMTP_SERVER = "smtp.gmail.com"
SMTP_PORT = 587

# Function to fetch weather data
def fetch_weather(location):
    url = f"http://api.openweathermap.org/data/2.5/weather?q={location}&units=metric&appid={WEATHER_API_KEY}"
    try:
        response = requests.get(url)
        response.raise_for_status()
        data = response.json()
        weather_desc = data['weather'][0]['description']
        temp = data['main']['temp']
        feels_like = data['main']['feels_like']
        return f"Weather in {location.capitalize()}: {weather_desc}, Temperature: {temp}°C, Feels Like: {feels_like}°C."
    except Exception as e:
        return f"Error fetching weather data: {str(e)}"

# Function to send email
def send_email(to_email, subject, body):
    try:
        msg = MIMEMultipart()
        msg['From'] = EMAIL_ADDRESS
        msg['To'] = to_email
        msg['Subject'] = subject
        msg.attach(MIMEText(body, 'plain'))
        server = smtplib.SMTP(SMTP_SERVER, SMTP_PORT)
        server.starttls()
        server.login(EMAIL_ADDRESS, EMAIL_PASSWORD)
        server.send_message(msg)
        server.quit()
        return "Email sent successfully!"
    except Exception as e:
        return f"Error sending email: {str(e)}"

# Function to perform basic arithmetic
def perform_calculation():
    print("\n--- Calculator ---")
    print("Enter a basic arithmetic expression (e.g., 5 + 3 or 10 / 2):")
    try:
        expression = input("> ")
        # Validate the input
        if not re.match(r"^\d+(\.\d+)?\s*[\+\-\/]\s\d+(\.\d+)?$", expression):
            raise ValueError("Invalid expression format.")
        result = eval(expression)
        print(f"Result: {result}")
    except Exception as e:
        print(f"Error: {str(e)}")

# Console-based user interface
def main():
    while True:
        print("\n--- Main Menu ---")
        print("1. Fetch live weather data")
        print("2. Send daily weather update via email")
        print("3. Use the calculator")
        print("4. Exit")
        choice = input("Enter your choice: ")
        
        if choice == "1":
            location = input("Enter the location: ")
            weather_info = fetch_weather(location)
            print(weather_info)
        elif choice == "2":
            location = input("Enter the location for weather updates: ")
            email = input("Enter recipient email: ")
            weather_info = fetch_weather(location)
            print("Sending email...")
            status = send_email(email, f"Daily Weather Update for {location.capitalize()}", weather_info)
            print(status)
        elif choice == "3":
            perform_calculation()
        elif choice == "4":
            print("Exiting. Have a great day!")
            break
        else:
            print("Invalid choice. Please try again.")

# Run the script
if __name__== "__main__":
    main()
