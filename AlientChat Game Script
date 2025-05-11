import openai
import os
import tkinter as tk
from dotenv import load_dotenv
from tkinter import scrolledtext, messagebox

# Load environment variables
load_dotenv()
openai.api_key = os.getenv("OPENAI_API_KEY")

# Alien profiles
alien_profiles = {
    "Zarnok": {
        "name": "Zarnok",
        "species": "Quarnarian",
        "description": (
            "Zarnok is a telepathic diplomat from the ice world of Quarnar. "
            "He communicates in poetic metaphors and values emotional resonance over logic."
        ),
    },
    "Kragthar": {
        "name": "Kragthar",
        "species": "Drakthorian",
        "description": (
            "Kragthar is a warlord from the volcanic world of Drakthor. "
            "He speaks in aggressive and commanding tones, valuing strength and dominance above all else."
        ),
    },
}

# Keywords affecting diplomacy score
positive_keywords = ["peace", "trust", "friend", "agree", "welcome", "harmony", "gratitude"]
negative_keywords = ["war", "threat", "disappoint", "danger", "enemy", "hostile", "reject"]

# Prompt builder
def build_prompt(user_input, alien_profile):
    return f"""
You are roleplaying as {alien_profile['name']}, an alien of the {alien_profile['species']} species.
{alien_profile['description']}

Respond to the human diplomat in character. Speak in short poetic metaphors or riddles if you are Zarnok, or in aggressive and commanding tones if you are Kragthar.

Human: {user_input}
{alien_profile['name']}:"""

# Get GPT response
def get_response(user_input, alien_profile):
    try:
        response = openai.ChatCompletion.create(
            model="gpt-4",
            messages=[
                {"role": "system", "content": "You are a deeply immersive alien diplomat in a sci-fi game."},
                {"role": "user", "content": build_prompt(user_input, alien_profile)},
            ],
            temperature=0.85,
            max_tokens=200,
        )
        return response['choices'][0]['message']['content'].strip()
    except Exception as e:
        return f"Error: {e}"

# Update diplomacy score
def update_diplomacy_score(response, current_score):
    lower_response = response.lower()
    score = current_score
    for word in positive_keywords:
        if word in lower_response:
            score += 5
    for word in negative_keywords:
        if word in lower_response:
            score -= 5
    return max(0, min(100, score))  # Clamp between 0 and 100

# Main GUI app
class AlienChatApp:
    def __init__(self, root):
        self.root = root
        self.root.title("Alien Diplomacy Simulator")

        # Set a spaceship-themed background color
        self.root.configure(bg="#1a1a2e")

        self.diplomacy_score = 50
        self.alliance_formed = False
        self.relations_broken = False
        self.current_alien = "Zarnok"

        # Title label
        self.title_label = tk.Label(
            root,
            text="Alien Diplomacy Simulator",
            font=("Helvetica", 16, "bold"),
            fg="#00ff00",
            bg="#1a1a2e"
        )
        self.title_label.pack(pady=5)

        # Instructions
        self.instructions_label = tk.Label(
            root,
            text=(
                "Instructions:\n"
                "Use your communication skills to reach a diplomacy score of at least 90 to win.\n"
                "If your diplomacy score drops to 0, you lose.\n"
                "Switch between aliens to adapt your strategy."
            ),
            font=("Helvetica", 10),
            fg="#ffffff",
            bg="#1a1a2e",
            justify="center"
        )
        self.instructions_label.pack(pady=5)

        # Diplomacy score label
        self.prominent_score_label = tk.Label(
            root,
            text=f"Diplomacy Score: {self.diplomacy_score}",
            font=("Helvetica", 14, "bold"),
            fg="#ff0000",
            bg="#1a1a2e"
        )
        self.prominent_score_label.pack(pady=5)

        # Chat area
        self.chat_area = scrolledtext.ScrolledText(
            root,
            wrap=tk.WORD,
            width=60,
            height=15,
            state='disabled',
            font=("Courier", 10),
            bg="#0f3460",
            fg="#ffffff"
        )
        self.chat_area.pack(padx=10, pady=10)

        # Entry field
        self.entry = tk.Entry(root, font=("Courier", 10), bg="#16213e", fg="#ffffff")
        self.entry.pack(padx=10, pady=(0, 10), fill=tk.X)
        self.entry.bind("<Return>", self.send_message)

        # Buttons
        self.send_button = tk.Button(
            root,
            text="Send",
            command=self.send_message,
            font=("Helvetica", 10),
            bg="#e94560",
            fg="#ffffff"
        )
        self.send_button.pack(pady=5)

        self.switch_button = tk.Button(
            root,
            text="Switch Alien",
            command=self.switch_alien,
            font=("Helvetica", 10),
            bg="#0f3460",
            fg="#ffffff"
        )
        self.switch_button.pack(pady=5)

    def send_message(self, event=None):
        user_input = self.entry.get().strip()
        if user_input:
            self.display_text(f"You: {user_input}")
            self.entry.delete(0, tk.END)
            response = get_response(user_input, alien_profiles[self.current_alien])
            self.display_text(f"{alien_profiles[self.current_alien]['name']}: {response}")

            # Update diplomacy score
            self.diplomacy_score = update_diplomacy_score(response, self.diplomacy_score)
            self.prominent_score_label.config(text=f"Diplomacy Score: {self.diplomacy_score}")

            # Check for events
            self.check_diplomacy_events()

    def display_text(self, text):
        self.chat_area.configure(state='normal')
        self.chat_area.insert(tk.END, f"{text}\n\n")
        self.chat_area.configure(state='disabled')
        self.chat_area.yview(tk.END)

    def check_diplomacy_events(self):
        if self.diplomacy_score >= 90 and not self.alliance_formed:
            self.alliance_formed = True
            self.display_text("[SYSTEM]: An alliance has been formed! Peace prevails across the stars.")
            messagebox.showinfo("Diplomatic Victory", "An alliance has been formed!")
        elif self.diplomacy_score <= 0 and not self.relations_broken:
            self.relations_broken = True
            self.display_text("[SYSTEM]: Diplomatic ties have broken. Communication has ended.")
            messagebox.showwarning("Diplomatic Failure", "Relations broken. Communication severed.")

    def switch_alien(self):
        if self.current_alien == "Zarnok":
            self.current_alien = "Kragthar"
        else:
            self.current_alien = "Zarnok"
        self.display_text(f"[SYSTEM]: You are now speaking with {alien_profiles[self.current_alien]['name']}.")

# Run app
if __name__ == "__main__":
    root = tk.Tk()
    app = AlienChatApp(root)
    root.mainloop()
