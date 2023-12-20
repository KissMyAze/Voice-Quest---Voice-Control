import tkinter as tk
from PIL import Image, ImageTk
import random
import imageio
import os
import pygame
import threading
import pyttsx3
import speech_recognition as sr
import time


class QuizGame():
    pygame.init()
    pygame.mixer.music.load("music_zapsplat_milky_way.mp3")
    pygame.mixer.music.set_volume(0.3)
    if not pygame.mixer.music.get_busy():
        pygame.mixer.music.play(-1)

    def on_enter(self, event):
        self.submit_button.config(bg='green', fg='white')

    def on_leave(self, event):
        self.submit_button.config(bg='black', fg='white')

    def play_sound(self):
        pygame.mixer.init()
        sound = pygame.mixer.Sound("zapsplat_multimedia_button_click_bright_003_92100.mp3")
        channel1 = pygame.mixer.Channel(1)
        channel1.play(sound)

    def load_frames(self):
        video_path = "WhatsApp Video 2023-12-15 at 09.04.36 (1).mp4"
        if os.path.exists(video_path):
            vid = imageio.get_reader(video_path)
            for num, im in enumerate(vid):
                if num % 5 == 0:
                    im = Image.fromarray(im)
                    self.frame_images.append(ImageTk.PhotoImage(im))
        else:
            print("Video file not found!")

    def display_frame(self):
        if self.frame_images:
            self.background_label.config(image=self.frame_images[self.current_frame])
            self.current_frame = (self.current_frame + 1) % len(self.frame_images)
            self.root.after(50, self.display_frame)

    def t2s(self, text):
        pyttsx3.speak(text)
        self.s2t()

    def s2t(self):
        time.sleep(3)
        recording = sr.Recognizer()

        with sr.Microphone() as source:
            recording.adjust_for_ambient_noise(source, duration=3)
            pyttsx3.speak("Aswer the question to get your voice recognized")
            while True:
                audio = recording.record(source, duration=3)
                try:
                    sgtext = recording.recognize_google(audio)
                    break
                except sr.UnknownValueError:
                    pyttsx3.speak("Your Voice is recognized. Please Answer the question again")
            # sgtext = recording.recognize_vosk(audio)
            print(sgtext)
            # print(sgtext)
            if "a" in sgtext.lower():
                self.selected_option.set(self.answers[self.index][0])
            elif "b" in sgtext.lower():
                self.selected_option.set(self.answers[self.index][1])
            elif "c" in sgtext.lower():
                self.selected_option.set(self.answers[self.index][2])
            elif "d" in sgtext.lower():
                self.selected_option.set(self.answers[self.index][3])
        time.sleep(3)
        self.submit_button.invoke()

    def __init__(self, root):
        self.root = root
        self.root.title("Quiz Game")
        self.root.geometry("1200x720")

        self.background_label = tk.Label(root)
        self.background_label.place(x=0, y=0, relwidth=1, relheight=1)

        self.frame_images = []
        self.load_frames()

        self.current_frame = 0
        self.display_frame()

        self.questions = ["What is the name of our planet?",
                          "Which celestial body is known as Earth's natural satellite?",
                          "What is the closest star to Earth?",
                          " What is the force that keeps planets in orbit around the sun?",
                          " Which planet is known as the Red Planet?",
                          " What is the largest planet in our solar system?",
                          " What is the name of the galaxy that contains our solar system?",
                          " Who was the first human to set foot on the moon?",
                          " What is the name of the first artificial satellite launched into space?",
                          " What does NASA stand for?",
                          " What is the speed of light in a vacuum?",
                          " What is the main function of a telescope?",
                          " What is the International Space Station (ISS)?",
                          " What is the name of the rover that NASA sent to Mars in 2021?",
                          " What is the basic unit of electric current?",
                          " What is the main component of the Earth's atmosphere?",
                          " What is the process by which plants make their own food using sunlight?",
                          " What is the largest ocean on Earth?",
                          " What is the name of the first human-made object to reach interstellar space?",
                          " What is the term for a sudden burst of energy on the Sun's surface?",
                          " What is the name of the first artificial satellite launched by the Soviet Union?",
                          " Which planet is known as the Morning Star or Evening Star due to its brightness?",
                          " What is the term for the path an object takes around a star?",
                          " Who developed the theory of general relativity?",
                          " What is the name of the most famous space telescope launched by NASA in 1990?",
                          " What is the primary mission of the Kepler Space Telescope?",
                          " Which spacecraft, launched by NASA in 1977, is the farthest human-made object from Earth?",
                          " What is the primary purpose of the Large Hadron Collider (LHC) located at CERN?",
                          " Which mission successfully landed a probe on Saturn's moon Titan in 2005?",
                          " What is the concept behind the Alcubierre warp drive discussed in theoretical physics?",
                          " Which phenomenon occurs when a massive star collapses under its own gravity, resulting in a super-dense object?",
                          " What is the primary objective of the Artemis program by NASA?",
                          " Which space agency successfully launched the Chang'e-4 mission, the first to land on the far side of the Moon?",
                          " What is the primary goal of the Breakthrough Starshot initiative?",
                          " Which space telescope is designed to observe the universe in infrared wavelengths and is set to succeed the Hubble Space Telescope?",
                          " What is the name of the first artificial satellite launched by the United States?",
                          " Which planet is often referred to as the Evening Star when visible after sunset?",
                          " What is the term for the point in an orbit where a satellite is closest to Earth?",
                          " Who was the first woman in space?",
                          " What is the name of the mission that successfully landed the Curiosity rover on Mars in 2012?",
                          " What does the acronym ISS stand for in the context of space technology?",
                          " What is the force that pulls objects toward the center of the Earth?",
                          " Which spacecraft was the first to carry humans to the Moon's surface?",
                          " What is the name of the giant storm on Jupiter that has been raging for centuries?",
                          " Which element is most abundant in the Sun?",
                          " What is the term for the imaginary line that runs from the North Pole to the South Pole through the Earth's center?",
                          " What is the name of the process by which a star releases energy, including light and heat?",
                          " Which planet is known as the Blue Planet due to its abundant water?",
                          " What is the name of the largest moon of Saturn?",
                          " Which space agency successfully landed the Perseverance rover on Mars in 2021?", ]

        self.correct_answers = ['b) Earth', 'b) Moon', 'a) Proxima Centauri',
                                'b) Gravity', 'a) Mars', 'b) Jupiter', 'b) Milky Way', 'a) Neil Armstrong',
                                'b) Sputnik 1',
                                'a) National Aeronautics and Space Administration', 'a) 186,282 miles per second',
                                'd) Observe distant objects',
                                'd) A habitable artificial satellite','d) Perseverence', 'b) Ampere', 'a) Nitrogen', 'b) Photosynthesis',
                                'b) Pacific Ocean',
                                'b) Voyager 1', 'a) Solar flare', 'a) Sputnik 1', 'b) Venus', 'a) Orbit',
                                'b) Albert Einstein', 'b) Hubble Space Telescope',
                                'a) Study exoplanets', 'a) Voyager 1', 'd) Explore the Higgs boson',
                                'b) Cassini-Huygens', 'a) Faster-than-light travel', 'b) Black hole',
                                'b) Return humans to the Moon and go on to Mars',
                                'd) CNSA (China National Space Administration)',
                                'a) Send small spacecraft to another star system', 'a) James Webb Space Telescope',
                                'a) Explorer 1',
                                'b) Venus', 'b) Perigee', 'a) Valentina Tereshkova', 'b) Mars Science Laboratory',
                                'd) International Space Station', 'a) Gravitational force', 'b) Apollo 11',
                                'a) Great Red Spot',
                                'b) Hydrogen', 'b) Meridian', 'a) Fusion', 'b) Earth', 'a) Titan', 'c) NASA']

        self.answers = [['a) Mars', 'b) Earth', 'c) Venus', 'd) Jupiter'],
                        ['a) Mars', 'b) Moon', 'c) Sun', 'd) Jupiter'],
                        ['a) Proxima Centauri', 'b) Betelgeuse', 'c) Alpha Centauri', 'd) Sirius'],
                        ['a) Magnetism', 'b) Gravity', 'c) Centrifugal force', 'd) Friction'],
                        ['a) Mars', 'b) Jupiter', 'c) Venus', 'd) Saturn'],
                        ['a) Earth', 'b) Jupiter', 'c) Mars', 'd) Saturn'],
                        ['a) Andromeda', 'b) Milky Way', 'c) Triangulum', 'd) Sombrero'],
                        ['a) Neil Armstrong', 'b) Buzz Aldrin', 'c) Yuri Gagarin', 'd) Michael Collins'],
                        ['a) Hubble', 'b) Sputnik 1', 'c) Voyager 1', 'd) ISS'],
                        ['a) National Aeronautics and Space Administration', 'b) North American Space Agency',
                         'c) New Astronomical and Space Association', 'd) Nuclear Astrophysics and Space Authority'],
                        ['a) 186,282 miles per second', 'b) 150,000 miles per hour', 'c) 300,000 kilometers per second',
                         'd) 500,000 kilometers per hour'],
                        ['a) Measure temperature', 'b) Amplify sound waves', 'c) Generate electricity',
                         'd) Observe distant objects'],
                        ['a) A large telescope', 'b) A space observatory', 'c) A human-made satellite',
                         'd) A habitable artificial satellite'],
                        ['a) Spirit', 'b) Curiosity', 'c) Opportunity', 'd) Perseverance'],
                        ['a) Volt', 'b) Ampere', 'c) Ohm', 'd) Watt'],
                        ['a) Nitrogen', 'b) Oxygen', 'c) Carbon dioxide', 'd) Helium'],
                        ['a) Respiration', 'b) Photosynthesis', 'c) Transpiration', 'd) Germination'],
                        ['a) Atlantic Ocean', 'b) Pacific Ocean', 'c) Indian Ocean', 'd) Southern Ocean'],
                        ['a) Pioneer 10', 'b) Voyager 1', 'c) New Horizons', 'd) Juno'],
                        ['a) Solar flare', 'b) Lunar eclipse', 'c) Stellar wind', 'd) Comet tail'],
                        ['a) Sputnik 1', 'b) Explorer 1', 'c) Vanguard 1', 'd) Telstar 1'],
                        ['a) Mars', 'b) Venus', 'c) Mercury', 'd) Jupiter'],
                        ['a) Orbit', 'b) Trajectory', 'c) Route', 'd) Circuit'],
                        ['a) Isaac Newton', 'b) Albert Einstein', 'c) Galileo Galilei', 'd) Johannes Kepler'],
                        ['a) Kepler Space Telescope', 'b) Hubble Space Telescope', 'c) Chandra X-ray Observatory',
                         'd) Spitzer Space Telescope'],
                        ['a) Study exoplanets', 'b) Search for dark matter', 'c) Monitor solar flares',
                         'd) Explore the Kuiper Belt'],
                        ['a) Voyager 1', 'b) Pioneer 10', 'c) New Horizons', 'd) Juno'],
                        ['a) Study cosmic microwave background radiation', 'b) Investigate dark energy',
                         'c) Search for extraterrestrial intelligence', 'd) Explore the Higgs boson'],
                        ['a) Mars Science Laboratory', 'b) Cassini-Huygens', 'c) Galileo', 'd) Rosetta'],
                        ['a) Faster-than-light travel', 'b) Time dilation', 'c) Wormhole creation',
                         'd) Antimatter propulsion'],
                        ['a) Supernova', 'b) Black hole', 'c) Neutron star', 'd) Pulsar'],
                        ['a) Search for extraterrestrial life on Mars',
                         'b) Return humans to the Moon and go on to Mars',
                         'c) Study the outer planets with robotic missions',
                         'd) Establish a permanent human presence on the Moon'],
                        ['a) NASA', 'b) ESA', 'c) ISRO', 'd) CNSA (China National Space Administration)'],
                        ['a) Send small spacecraft to another star system', 'b) Search for extraterrestrial signals',
                         'c) Study the atmospheres of exoplanets', 'd) Develop advanced space habitats'],
                        ['a) James Webb Space Telescope', 'b) Kepler Space Telescope', 'c) Planck Telescope',
                         'd) WISE (Wide-field Infrared Survey Explorer)'],
                        ['a) Explorer 1', 'b) Vanguard 1', 'c) Sputnik 1', 'd) Telstar 1'],
                        ['a) Mars', 'b) Venus', 'c) Jupiter', 'd) Saturn'],
                        ['a) Apogee', 'b) Perigee', 'c) Zenith', 'd) Nadir'],
                        ['a) Valentina Tereshkova', 'b) Sally Ride', 'c) Yuri Gagarin', 'd) Mae Jemison'],
                        ['a) Mars Express', 'b) Mars Science Laboratory', 'c) Mars Pathfinder',
                         'd) Mars Reconnaissance Orbiter'],
                        ['a) Interstellar Space Station', 'b) International Science Shuttle',
                         'c) In conterplanetary Solar System', 'd) International Space Station'],
                        ['a) Gravitational force', 'b) Magnetic force', 'c) Centrifugal force',
                         'd) Electrostatic force'],
                        ['a) Apollo 8', 'b) Apollo 11', 'c) Apollo 13', 'd) Apollo 17'],
                        ['a) Great Red Spot', 'b) White Oval', 'c) Dark Spot', 'd) Blue Storm'],
                        ['a) Helium', 'b) Hydrogen', 'c) Oxygen', 'd) Carbon'],
                        ['a) Equator', 'b) Meridian', 'c) Tropic', 'd) Latitude'],
                        ['a) Fusion', 'b) Fission', 'c) Convection', 'd) Radiation'],
                        ['a) Mars', 'b) Earth', 'c) Venus', 'd) Neptune'],
                        ['a) Titan', 'b) Europa', 'c) Ganymede', 'd) Io'],
                        ['a) ESA', 'b) ISRO', 'c) NASA', 'd) Roscosmos']]

        self.index = self.questions.index(random.choice(self.questions)) - 1
        self.current_question = self.index
        self.score = 0
        self.count = 0

        self.question_label = tk.Label(self.root, text=self.questions[self.current_question], font=("Times New Roman", 30),
                                       bg="black", fg="white", wraplength=1000)
        self.question_label.pack()

        self.selected_option = tk.StringVar()

        self.option_button1 = tk.Radiobutton(self.root, text=self.answers[self.index][0], variable=self.selected_option,
                                             value=self.answers[self.index][0], font=("Bahnschrift Condensed", 25), bg="black", fg="purple",
                                             command=self.play_sound, wraplength=800)
        self.option_button2 = tk.Radiobutton(self.root, text=self.answers[self.index][1], variable=self.selected_option,
                                             value=self.answers[self.index][1], font=("Bahnschrift Condensed", 25), bg="black", fg="purple",
                                             command=self.play_sound, wraplength=800)
        self.option_button3 = tk.Radiobutton(self.root, text=self.answers[self.index][2], variable=self.selected_option,
                                             value=self.answers[self.index][2], font=("Bahnschrift Condensed", 25), bg="black", fg="purple",
                                             command=self.play_sound, wraplength=800)
        self.option_button4 = tk.Radiobutton(self.root, text=self.answers[self.index][3], variable=self.selected_option,
                                             value=self.answers[self.index][3], font=("Bahnschrift Condensed", 25), bg="black", fg="purple",
                                             command=self.play_sound, wraplength=800)
        self.option_button1.pack()
        self.option_button2.pack()
        self.option_button3.pack()
        self.option_button4.pack()

        self.submit_button = tk.Button(self.root, text="Submit", command=self.check_answer, font=("Helvetica", 26),
                                       bg="black", fg="white", height=1, width=7)
        self.submit_button.pack()

        self.score_label = tk.Label(self.root, text="Score: 0", font=("Impact", 23), bg="black", fg="white")
        self.score_label.pack()

        self.submit_button.bind("<Enter>", self.on_enter)
        self.submit_button.bind("<Leave>", self.on_leave)

        th1 = threading.Thread(target=self.t2s, args=(self.questions[self.current_question],))
        th1.start()

    def check_answer(self):
        user_answer = self.selected_option.get()
        correct_answer = self.correct_answers[self.current_question]

        selected_letter = user_answer.split(')')[0] + ')'
        right = ["Wonderful!", "Amazing!", "Great! That's Correct", "Keep it up", "Excellent!"]
        wrong = ["Keep Trying", "Uh-oh! Incorrect Answer", "Try Again", "Don't Worry, Keep Trying", "You can do it!"]
        if selected_letter == correct_answer.split(')')[0] + ')':
            self.score += 1
            self.score_label.config(text=f"Score: {self.score} {random.choice(right)}")
        else:
            self.score_label.config(text=f"Score: {self.score} {random.choice(wrong)}")
        self.selected_option.set("")

        if self.count < 2:
            pygame.mixer.init()
            sound1 = pygame.mixer.Sound(
                "zapsplat_foley_plastic_button_med_small_single_press_bright_click_003_72185.mp3")
            channel1 = pygame.mixer.Channel(1)
            channel1.play(sound1)
            self.index = self.questions.index(random.choice(self.questions)) - 1
            self.current_question = self.index
            self.question_label.config(text=self.questions[self.current_question])
            self.option_button1.config(text=self.answers[self.index][0], value=self.answers[self.index][0])
            self.option_button2.config(text=self.answers[self.index][1], value=self.answers[self.index][1])
            self.option_button3.config(text=self.answers[self.index][2], value=self.answers[self.index][2])
            self.option_button4.config(text=self.answers[self.index][3], value=self.answers[self.index][3])
            self.option_button1.pack()
            self.option_button2.pack()
            self.option_button3.pack()
            self.option_button4.pack()
            self.count += 1
            th1 = threading.Thread(target=self.t2s, args=(self.questions[self.current_question],))
            th1.start()
            # self.t2s(self.questions[self.current_question])
        else:
            pygame.mixer.init()
            pygame.mixer.music.load("little_robot_sound_factory_Jingle_Win_Synth_03.mp3")
            pygame.mixer.music.play()
            self.question_label.config(text="Quiz Completed!")
            self.show_final_score()
            self.submit_button.pack_forget()
            self.score_label.pack_forget()
            self.submit_button.pack_forget()
            self.option_button1.pack_forget()
            self.option_button2.pack_forget()
            self.option_button3.pack_forget()
            self.option_button4.pack_forget()

    def show_final_score(self):
        final_message = f"Quiz Complete\nFinal Score: {self.score}/3"
        final_label = tk.Label(self.root, text=final_message, font=("Helvetica", 20), pady=20, bg="black", fg="white")
        final_label.pack()

        if self.score == 3:

            congrats = ["You're a Quiz Master!", "Perfect Score!", "You're a Genius"]
            message = random.choice(congrats)
            congrats_label = tk.Label(self.root, text=message, font=("Arial", 18, "italic"), fg="green")
            congrats_label.pack()
        elif self.score == 2:

            nice = ["Well Done!", "Great Job!", "Nice Effort!"]
            message = random.choice(nice)
            nice_label = tk.Label(self.root, text=message, font=("Arial", 16), fg="blue")
            nice_label.pack()
        else:
            sorry = ["Keep Going!", "Better Luck Next Time!", "Practice Makes Perfect!"]
            message = random.choice(sorry)
            sorry_label = tk.Label(self.root, text=message, font=("Arial", 14), fg="red")
            sorry_label.pack()

        for widget in self.root.winfo_children():
            if isinstance(widget, (tk.Radiobutton, tk.Entry, tk.Button)):
                widget.pack_forget()


root = tk.Tk()
game = QuizGame(root)
root.mainloop()
