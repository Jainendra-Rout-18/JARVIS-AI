# TODO - add camera access
# TODO - add alarm feature
# TODO - add GUI
import pyautogui
import pyttsx3
import requests
import speech_recognition as sr
import datetime
import os
import random
from requests import get
import wikipedia
import webbrowser
import pywhatkit as kit
import sys
import time
import pyjokes

# Introducing the Jarvis with the voice
engine = pyttsx3.init("sapi5")
voices = engine.getProperty("voices")
# print(voices[0].id)
engine.setProperty("voices", voices[len(voices) - 1].id)


# Text to speech
def speak(audio):
    engine.say(audio)
    print(audio)
    engine.runAndWait()


def takecommand():
    r = sr.Recognizer()
    with sr.Microphone() as source:
        print("Listening...")
        r.pause_threshold = 1
        audio = r.listen(source)

    try:
        print("Recognizing...")
        query = r.recognize_google(audio, language="en-in")
        print(f"User said: {query}\n")

    except Exception as e:
        # print(e)
        speak("Say that again please...")
        return "none"
    return query


def wish():
    hour = int(datetime.datetime.now().hour)
    tt = time.strftime("%I:%M %p")

    if (hour >= 0) and (hour < 12):
        speak(f"Good Morning, its {tt}")
    elif (hour >= 12) and (hour < 18):
        speak(f"Good Afternoon, its {tt}")
    else:
        speak(f"Good Evening, its {tt}")
    speak("I am Jarvis, How can I help you?")

def news():
    main_url = 'https://newsapi.org/v2/top-headlines?sources=techcrunch&apiKey=5cb79d08c1b74a06856098f2468767ea'

    main_page = requests.get(main_url).json()
    articles = main_page['articles']
    head = []
    day = ["first", "second", "third", "fourth", "fifth"]
    for ar in articles:
        head.append(ar["title"])
    for i in range(len(day)):
        speak(f"{day[i]} article is {head[i]}")


if __name__ == "__main__":
    wish()
    while True:

        query = takecommand().lower()

        if "open notepad" in query:
            npath = "C:\\Windows\\system32\\notepad.exe"
            os.startfile(npath)

        elif "close notepad" in query:
            speak("Okay sir, closing notepad")
            os.system("taskkill /f /im notepad.exe")

        elif "open adobe reader" in query:
            rpath = "C:\\Program Files\\Adobe\\Acrobat DC\\Acrobat\\Acrobat.exe"
            os.startfile(rpath)

        elif "close adobe reader" in query:
            speak("Okay sir, closing reader")
            os.system("taskkill /f /im Acrobat.exe")

        elif "open android studio" in query:
            apath = "C:\\Program Files\\Android\\Android Studio\\bin\\studio64.exe"
            os.startfile(apath)

        elif "close android studio" in query:
            speak("Okay sir, closing android studio")
            os.system("taskkill /f /im studio64.exe")

        elif "open command prompt" in query:
            os.system("start cmd")

        elif "close command prompt" in query:
            speak("Okay sir, closing command prompt")
            os.system("taskkill /f /im cmd.exe")

        elif "open spotify" in query:
            os.system("start spotify")

        elif "close spotify" in query:
            speak("Okay sir, closing spotify")
            os.system("taskkill /f /im spotify.exe")

        elif "open whatsapp" in query:
            os.system("start whatsapp")

        elif "close whatsapp" in query:
            speak("Okay sir, closing whatsapp")
            os.system("taskkill /f /im whatsapp.exe")

        elif "open calculator" in query:
            os.system("start calc")

        elif "close calculator" in query:
            speak("Okay sir, closing calculator")
            os.system("taskkill /f /im calc.exe")

        elif "play music" in query:
            music_dir = "C:\\Users\\routj\\Documents\\songs"
            songs = os.listdir(music_dir)
            rd = random.choice(songs)
            os.startfile(os.path.join(music_dir, rd))

        elif "my ip address" in query:
            ip = get("https://api.ipify.org").text
            speak(f"Your IP address is {ip}")

        elif "wikipedia" in query:
            speak("Searching Wikipedia...")
            query = query.replace("wikipedia", "")
            results = wikipedia.summary(query, sentences=2)
            speak(f"According to Wikipedia, {results}")
            print(results)

        elif "open google" in query:
            speak("sir, what should I search on google")
            query = takecommand().lower()
            webbrowser.open("https://www.google.com/search?q=" + query)

        elif "send message" in query or "send a message" in query or 'send the message' in query:
            speak("Whom do you want to send the message to?")
            num = takecommand().lower()
            num = "+91" + num

            speak("What should I say?")
            msg = takecommand().lower()
            kit.sendwhatmsg(num, msg, int(datetime.datetime.now().hour), int(datetime.datetime.now().minute) + 2)
            pyautogui.press("Enter")
            speak("Message sent")

        elif "song from youtube" in query:
            speak("Which song do you want to listen?")
            sng = takecommand().lower()
            kit.playonyt(sng)

        elif "you can sleep" in query:
            speak("Thanks, have a nice day")
            sys.exit()

        elif "tell me a joke" in query:
            joke = pyjokes.get_joke()
            speak(joke)

        elif "shut down the system" in query:
            os.system("shutdown /s /t 5")

        elif "restart the system" in query:
            os.system("shutdown /r /t 5")

        elif "turn on sleep mode" in query:
            os.system("rundll32.exe powrprof.dll,SetSuspendState 0,-1,0")

        elif "switch window" in query:
            pyautogui.keyDown("alt")
            pyautogui.press("tab")
            time.sleep(1)
            pyautogui.keyUp("alt")

        elif "tell me a news" in query:
            speak("please wait sir, fetching the latest news...")
            news()

        elif "where am i" in query or "where are we" in query:
            speak("wait sir, let me check")
            try:
                ipAdd = requests.get('https://api.ipify.org').text
                print(ipAdd)
                url = "https://get.geojs.io/v1/ip/geo/" + ipAdd + ".json"
                geo_requests = requests.get(url)
                geo_data = geo_requests.json()
                city = geo_data["city"]
                # state = geo_data["state"]
                country = geo_data["country"]
                speak(f"I am not sure, but I think we are in {city} situated in {country} country")
            except Exception as e:
                speak("sorry sir, due to network error I am not able to find where we are.")
                print(e)

        # username = '31jjrj374nwrzn5ggwbqqvs5aaja'
        # clientID = 'c6d814f01ee84f38b98c29a6c7a9a20d'
        # clientSecret = '71f7cd2923da46df9ce0ed83e452c89a'
        # redirect_uri = 'http://google.com/callback/'
        #
        # oauth_object = spotipy.SpotifyOAuth(clientID, clientSecret, redirect_uri)
        # token_dict = oauth_object.get_access_token()
        # token = token_dict['access_token']
        # spotifyObject = spotipy.Spotify(auth=token)
        # user_name = spotifyObject.current_user()
        #
        # # To print the response in readable format.
        # print(json.dumps(user_name, sort_keys=True, indent=4))

        sites = [
            ("youtube", "https://youtube.com"),
            ("wikipedia", "https://wikipedia.org"),
            ("stack overflow", "https://stackoverflow.com"),
            ("instagram", "https://instagram.com"),
            ("github", "https://github.com"),
        ]
        for site in sites:
            if f"open {site[0]}".lower() in query.lower():
                speak(f"opening {site[0]}...")
                webbrowser.open(site[1])

        # speak("Do you have any other work to do?")
