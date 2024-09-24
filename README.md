# AI-Model-using-Python
Project of a AI Model  " Using Python and AI"

import cv2
import pygame
import threading
import pyttsx3
import speech_recognition as sr
import datetime
import wikipedia
import webbrowser
import os
import tkinter as tk
from tkinter import messagebox

# Initialize the text-to-speech engine
engine = pyttsx3.init('sapi5')
voices = engine.getProperty('voices')
engine.setProperty('voice', voices[0].id)

def speak(audio):
    engine.say(audio)
    engine.runAndWait()

def takeCommand():
    r = sr.Recognizer()
    with sr.Microphone() as source:
        print("Listening...")
        r.pause_threshold = 1
        audio = r.listen(source)
    try:
        print("Recognizing...")
        query = r.recognize_google(audio, language='en-in')
        print(f"User said: {query}\n")
    except Exception as e:
        print("Say that again please...")
        return "None"
    return query

def play_sound(sound_path):
    pygame.mixer.init()
    pygame.mixer.music.load(sound_path)
    pygame.mixer.music.play()

def play_video(video_path):
    cap = cv2.VideoCapture(video_path)
    if not cap.isOpened():
        print("Error: Could not open video.")
        return

    desired_width = 800
    desired_height = 600

    while cap.isOpened():
        ret, frame = cap.read()
        if not ret:
            break

        frame = cv2.resize(frame, (desired_width, desired_height))
        cv2.imshow('Video', frame)
        if cv2.waitKey(25) & 0xFF == ord('q'):
            break

    cap.release()
    cv2.destroyAllWindows()

def execute_commands():
    query = takeCommand().lower()

    if 'wikipedia' in query:
        speak('Searching Wikipedia...')
        query = query.replace("wikipedia", "")
        results = wikipedia.summary(query, sentences=2)
        speak("According to Wikipedia")
        print(results)
        speak(results)

    elif 'open youtube' in query:
        webbrowser.open("youtube.com")

    elif 'open google' in query:
        webbrowser.open("google.com")

    elif 'open stackoverflow' in query:
        webbrowser.open("stackoverflow.com")

    elif 'open camera' in query:
        cap = cv2.VideoCapture(0)
        while True:
            ret, img = cap.read()
            cv2.imshow('webcam', img)
            if cv2.waitKey(50) == 27:
                break
        cap.release()
        cv2.destroyAllWindows()

    elif 'the time' in query:
        strTime = datetime.datetime.now().strftime("%H:%M:%S")
        speak(f"Sir, the time is {strTime}")

    elif any(greeting in query for greeting in ['hello', 'hey', 'hi', 'hii']):
        speak("Hello sir, How can I help you?")

    elif any(greeting in query for greeting in ['open website', 'open biit computerwebsite', 'show DCA','open Bit computer website', 'programming course','programming courses']):
        webbrowser.open("http://biitcomputers.com/#")

    elif any(greeting in query for greeting in ['python price']):
        speak("Sir the Python programming Course Price 4400 only ")

    # After handling the command, stop the execution
    global running
    running = False

def start_jarvis_after_delay():
    threading.Thread(target=play_video, args=("0099.mp4",)).start()  # Play video in background
    threading.Thread(target=play_sound, args=("00991.mp3",)).start()
    print("Waiting for 23 seconds before starting Jarvis...")
    
    # Wait for 20 seconds before starting Jarvis
    threading.Timer(23, start_jarvis).start()

def start_jarvis():
    global running
    if not running:
        running = True
        # Start the command execution in a separate thread
        threading.Thread(target=execute_commands, daemon=True).start()
        print("Jarvis started.")

def stop_jarvis():
    global running
    if running:
        running = False
        print("Jarvis stopped.")

def create_gui():
    global running
    running = False

    root = tk.Tk()
    root.title("Jarvis Control")

    start_button = tk.Button(root, text="Start", command=start_jarvis_after_delay)
    start_button.pack(pady=20)

    stop_button = tk.Button(root, text="Stop", command=stop_jarvis)
    stop_button.pack(pady=20)

    root.mainloop()

if __name__ == "__main__":
    create_gui()


Project Definition
Objective: Create a virtual assistant using Python that can perform various tasks through voice commands, including web browsing, video playback, and information retrieval.

Components and Theories
Text-to-Speech (TTS)

Library: pyttsx3
Theory: TTS converts text into spoken words. This is useful for making the assistant respond audibly to user commands and queries.
Implementation: The speak function initializes the TTS engine and reads out the provided text.
Speech Recognition

Library: speech_recognition
Theory: This component captures audio input from the user and converts it into text. It's vital for a voice-activated assistant.
Implementation: The takeCommand function uses the microphone to listen for commands and recognizes them using Google's API.
Video and Sound Playback

Libraries: pygame for sound, cv2 (OpenCV) for video
Theory: Playing multimedia enhances user interaction and engagement. It allows the assistant to perform actions like showing videos or playing sounds based on user commands.
Implementation: Functions like play_sound and play_video handle multimedia playback.
Command Execution Logic

Theory: The assistant processes various voice commands to perform different actions (e.g., open websites, tell time, search Wikipedia).
Implementation: The execute_commands function listens for specific phrases and executes corresponding actions, demonstrating a basic command parsing mechanism.
Multithreading

Library: threading
Theory: Multithreading allows the program to perform multiple operations simultaneously, such as listening for commands while playing a video.
Implementation: Separate threads are created for multimedia playback and command execution to maintain responsiveness.
Graphical User Interface (GUI)

Library: tkinter
Theory: A GUI provides a user-friendly way to interact with the application, enabling easy control over starting and stopping the assistant.
Implementation: The create_gui function sets up buttons to start and stop the assistant, demonstrating basic GUI design principles.
Flow of Execution
Initialization: The program initializes the TTS engine and sets up the GUI.
User Interaction: When the user clicks the "Start" button, the assistant begins listening for commands after a 23-second delay during which a video and sound play.
Command Recognition: The assistant recognizes spoken commands, interprets them, and executes the relevant functions (e.g., browsing websites, answering queries).
Continuous Listening: The assistant keeps listening for new commands until it is stopped by the user through the GUI.
Conclusion
This code provides a foundational structure for building a voice-activated virtual assistant. You can expand it by adding more features, improving command recognition, or integrating additional APIs for enriched functionality. Each component works together to create an interactive experience that mimics a personal assistant.
