# ai_assistant_gemini.py
# Full AI Assistant using Google Gemini + Speech Recognition + gTTS
# By Kanak Raj (Kanaktronics) 

import google.generativeai as genai
import speech_recognition as sr
from gtts import gTTS
import pygame
import os

# ---------- CONFIG ----------
genai.configure(api_key="YOUR_API_KEY_HERE")
model = genai.GenerativeModel("gemini-pro")

# Initialize pygame for TTS playback
pygame.mixer.init()

# ---------- FUNCTIONS ----------
def listen():
    """Convert speech to text using SpeechRecognition"""
    r = sr.Recognizer()
    with sr.Microphone() as source:
        print("🎤 Listening...")
        r.pause_threshold = 1
        audio = r.listen(source)
    try:
        print("⏳ Recognizing...")
        query = r.recognize_google(audio, language="en-in")
        print(f"🧑 You: {query}")
        return query
    except:
        print("❌ Could not recognize. Please try again.")
        return None

def speak(text):
    """Convert text to speech using gTTS"""
    print("🤖 Assistant:", text)
    tts = gTTS(text=text, lang="en", slow=False)
    tts.save("response.mp3")
    pygame.mixer.music.load("response.mp3")
    pygame.mixer.music.play()
    while pygame.mixer.music.get_busy():
        pass
    os.remove("response.mp3")

def chat_with_gemini(prompt):
    """Send query to Gemini and get response"""
    response = model.generate_content(prompt)
    return response.text

# ---------- MAIN LOOP ----------
print("🤖 Kanaktronics AI Assistant (Gemini Powered)")
print("Say 'exit' anytime to quit.\n")

while True:
    query = listen()
    if query is None:
        continue

    if query.lower() in ["exit", "quit", "stop"]:
        speak("Goodbye! Have a nice day.")
        break

    # Gemini response
    answer = chat_with_gemini(query)
    speak(answer)
