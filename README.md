# haiku_discord_bot
# Haiku Discord Bot

This project automatically collects textual data from Reddit and generates haikus (5-7-5 syllable structure).

## Features

- Collects data from Reddit API
- Performs syllable analysis using CMU dictionary
- Generates haikus
- Outputs locally (Week 1 milestone)

## Setup

Clone repository

git clone https://github.com/username/haiku-discord-bot.git

Install dependencies

pip install -r requirements.txt

Run program

python3 src/main.py

## Week 2 Goals

- Integrate Discord Webhook
- Automate posting with CRON
- Improve syllable filtering



Project Structure

Plain Text
-praw
-pronouncing
-requests

Bash
pip install -r requirements.txt


data_collecter.py

import praw

def get_titles(limit=50):
    reddit = praw.Reddit(
        client_id="YOUR_CLIENT_ID",
        client_secret="YOUR_CLIENT_SECRET",
        user_agent="haiku-bot"
    )

    titles = []

    for submission in reddit.subreddit("news").hot(limit=limit):
        titles.append(submission.title)

    return titles

syllables.py
import pronouncing

def count_syllables(word):
    phones = pronouncing.phones_for_word(word)

    if phones:
        return pronouncing.syllable_count(phones[0])
    
    return 1

  haiku_generator.py
  import random
from syllables import count_syllables


def line_builder(words, target):
    line = []
    syllables = 0

    random.shuffle(words)

    for word in words:
        s = count_syllables(word)

        if syllables + s <= target:
            line.append(word)
            syllables += s

        if syllables == target:
            return " ".join(line)

    return None


def generate_haiku(words):

    for _ in range(100):

        line1 = line_builder(words, 5)
        line2 = line_builder(words, 7)
        line3 = line_builder(words, 5)

        if line1 and line2 and line3:
            return f"{line1}\n{line2}\n{line3}"

    return "Could not generate haiku"

  main.py
  import re
from data_collector import get_titles
from haiku_generator import generate_haiku


def clean_words(titles):

    words = []

    for title in titles:
        title = re.sub(r"[^a-zA-Z ]", "", title)

        for word in title.lower().split():
            if len(word) > 2:
                words.append(word)

    return words


def main():

    titles = get_titles()

    words = clean_words(titles)

    haiku = generate_haiku(words)

    print("\nGenerated Haiku:\n")
    print(haiku)


if __name__ == "__main__":
    main()

  run.sh
  #!/bin/bash

make executable
chmod +x run.sh

cd ~/haiku-discord-bot
python3 src/main.py

.gitignore
__pycache__/
*.pyc
.env
config/config.py
    
