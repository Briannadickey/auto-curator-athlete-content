# auto-curator-athlete-content
Automatically pull and summarize athlete-endorsed content (like IG captions, tweets, interviews) and prep it for Nike product pages or marketing.
# Auto-Curator Athlete Content

A smart automation project that gathers and summarizes athlete-generated content (social media posts, interviews, videos) to enrich product pages and marketing materials — inspired by Nike’s storytelling and athlete-first branding.

## Overview

This tool pulls the latest public content from athlete influencers, uses AI to summarize key themes, and formats it into short blurbs suitable for Nike’s product descriptions, social media, or campaigns.

## Features

- Search and select an athlete (from a predefined list)
- Fetch latest public posts (IG captions, tweets, YouTube transcripts)
- Use GPT API to auto-summarize and generate marketing-friendly blurbs
- Clean, minimal Flask UI to showcase summaries per athlete

## Tech Stack

- **Python**: Backend logic + API handling
- **Flask**: Simple UI/web server
- **OpenAI API**: GPT summarization
- **BeautifulSoup / Tweepy / YouTube API**: For content fetching
- **SQLite / JSON**: Store cached athlete data
- **HTML/CSS**: Front-end templating

## Folder Structure

```
auto-curator-athlete-content/
├── app/                  
│   ├── routes.py         
│   ├── content_fetcher.py
│   ├── summarizer.py     
│   └── templates/
│       └── index.html    
├── static/
│   └── styles.css        
├── data/
│   └── athletes.json     
├── .gitignore
├── app.py                
├── requirements.txt      
├── README.md
```

## Setup Instructions

1. **Clone the repo**
```bash
git clone https://github.com/yourusername/auto-curator-athlete-content.git
cd auto-curator-athlete-content
```

2. **Create virtual environment and install dependencies**
```bash
python -m venv venv
source venv/bin/activate  # or venv\Scripts\activate on Windows
pip install -r requirements.txt
```

3. **Add your API keys**

Create a `.env` file and add:
```
OPENAI_API_KEY=your-key-here
TWITTER_BEARER_TOKEN=your-twitter-token
```

4. **Run the app**
```bash
python app.py
```

5. **Go to** `http://127.0.0.1:5000` in your browser.

## Future Improvements

- OAuth login for athlete account linking
- Better NLP tuning for voice/tone
- Deployment on Render or Vercel
- Admin interface for adding new athletes

---

Inspired by Nike's pursuit of innovation and its athlete-first narrative.
# === auto-curator-athlete-content ===

# --- app.py ---
from flask import Flask
from app.routes import main

app = Flask(__name__)
app.register_blueprint(main)

if __name__ == '__main__':
    app.run(debug=True)

# --- app/routes.py ---
from flask import Blueprint, render_template, request
from app.summarizer import summarize_text
from app.content_fetcher import get_mock_athlete_content

main = Blueprint('main', __name__)

@main.route('/', methods=['GET', 'POST'])
def index():
    summary = ""
    if request.method == 'POST':
        athlete = request.form.get('athlete')
        raw_text = get_mock_athlete_content(athlete)
        summary = summarize_text(raw_text)
    return render_template('index.html', summary=summary)

# --- app/summarizer.py ---
import os
import openai

openai.api_key = os.getenv("OPENAI_API_KEY")

def summarize_text(content):
    if not content:
        return "No content to summarize."

    response = openai.ChatCompletion.create(
        model="gpt-3.5-turbo",
        messages=[
            {"role": "system", "content": "Summarize this athlete content for a Nike product page."},
            {"role": "user", "content": content}
        ],
        temperature=0.7,
        max_tokens=150
    )
    return response['choices'][0]['message']['content'].strip()

# --- app/content_fetcher.py ---
def get_mock_athlete_content(athlete_name):
    sample_data = {
        "LeBron James": "Just left it all on the court. Love this game. More to come. #striveforgreatness",
        "Serena Williams": "Confidence is everything. I’m ready for what’s next. #unstoppable",
        "Naomi Osaka": "Mental strength > everything else. Thankful for the support lately.",
    }
    return sample_data.get(athlete_name, "No recent content available.")

# --- app/templates/index.html ---
<!DOCTYPE html>
<html>
<head>
    <title>Athlete Content Summarizer</title>
</head>
<body>
    <h1>Select an Athlete</h1>
    <form method="POST">
        <select name="athlete">
            <option>LeBron James</option>
            <option>Serena Williams</option>
            <option>Naomi Osaka</option>
        </select>
        <button type="submit">Summarize</button>
    </form>

    {% if summary %}
        <h2>AI-Generated Summary:</h2>
        <p>{{ summary }}</p>
    {% endif %}
</body>
</html>
# === requirements.txt ===
flask
openai
python-dotenv

# === .gitignore ===
# Byte-compiled / optimized / DLL files
__pycache__/
*.py[cod]
*.so

# Virtual environments
venv/
env/

# Environment variables
.env

# Mac & Windows system files
.DS_Store
Thumbs.db

# VSCode config
.vscode/

# === data/athletes.json ===
[
  {
    "name": "LeBron James",
    "socials": {
      "instagram": "kingjames",
      "twitter": "KingJames"
    }
  },
  {
    "name": "Serena Williams",
    "socials": {
      "instagram": "serenawilliams",
      "twitter": "serenawilliams"
    }
  },
  {
    "name": "Naomi Osaka",
    "socials": {
      "instagram": "naomiosaka",
      "twitter": "naomiosaka"
    }
  }
]
