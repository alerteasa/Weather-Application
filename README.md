# Weather-Application
import pygame
import sys
import random
import requests

# Initialize
pygame.init()
screen = pygame.display.set_mode((800, 600))
pygame.display.set_caption("Weather App")
font = pygame.font.SysFont(None, 32)

# Load assets
sun = pygame.image.load("assets/sun.png")
cloud = pygame.image.load("assets/cloud.png")
rain_img = pygame.image.load("assets/rain.png")
lightning = pygame.image.load("assets/lightning.png")
night = pygame.image.load("assets/night.png")
evening = pygame.image.load("assets/evening.png")
cyclone = pygame.image.load("assets/cyclone.png")
rain_sound = pygame.mixer.Sound("assets/rain_sound.wav")

clock = pygame.time.Clock()
weather_state = "sunny"
rain_drops = []
temp = "N/A"
humidity = "N/A"

def create_rain():
    for _ in range(50):
        x = random.randint(0, 800)
        y = random.randint(0, 600)
        rain_drops.append([x, y])

def draw_rain():
    for drop in rain_drops:
        pygame.draw.line(screen, (0, 0, 255), (drop[0], drop[1]), (drop[0], drop[1]+10), 2)
        drop[1] += 5
        if drop[1] > 600:
            drop[1] = 0

def draw_weather():
    screen.fill((135, 206, 235))  # sky blue background
    if weather_state == "sunny":
        screen.blit(sun, (300, 100))
        rain_sound.stop()
    elif weather_state == "rain":
        screen.blit(cloud, (250, 80))
        draw_rain()
        rain_sound.play(-1)
    elif weather_state == "lightning":
        screen.blit(cloud, (250, 80))
        screen.blit(lightning, (270, 200))
        rain_sound.stop()
    elif weather_state == "night":
        screen.blit(night, (0, 0))
        rain_sound.stop()
    elif weather_state == "evening":
        screen.blit(evening, (0, 0))
        rain_sound.stop()
    elif weather_state == "cyclone":
        screen.blit(cyclone, (200, 100))
        rain_sound.stop()

    info = font.render(f"Temp: {temp}°C   Humidity: {humidity}%", True, (0, 0, 0))
    screen.blit(info, (10, 10))

def get_weather(city="tokyo"):
    global weather_state, temp, humidity
    try:
        api_key = "6ddda7657076938700d1940a77acba48"  # Replace with your OpenWeatherMap API key
        url = f"http://api.openweathermap.org/data/2.5/weather?q={city}&appid={api_key}&units=metric"
        data = requests.get(url).json()
        main = data['weather'][0]['main'].lower()
        if 'rain' in main:
            weather_state = "rain"
        elif 'cloud' in main:
            weather_state = "clouds"
        elif 'clear' in main:
            weather_state = "sunny"
        else:
            weather_state = "sunny"
        temp = data['main']['temp']
        humidity = data['main']['humidity']
    except:
        weather_state = "sunny"
        temp = "N/A"
        humidity = "N/A"

create_rain()
get_weather("tokyo")  # Change city name as needed

running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_1:
                weather_state = "sunny"
            elif event.key == pygame.K_2:
                weather_state = "rain"
            elif event.key == pygame.K_3:
                weather_state = "lightning"
            elif event.key == pygame.K_4:
                weather_state = "night"
            elif event.key == pygame.K_5:
                weather_state = "evening"
            elif event.key == pygame.K_6:
                weather_state = "cyclone"
            elif event.key == pygame.K_r:
                get_weather("London")

    draw_weather()
    pygame.display.flip()
    clock.tick(60)

pygame.quit()
sys.exit()
