# open_innovation
# KrishiMitra: AI-Powered Daily Companion for Farmers
# Full implementation including BhashaBuddy, Daily Tasks, Mandi Prices, Crop Health, and Govt Schemes

from flask import Flask, request, jsonify
from datetime import datetime
import random

app = Flask(__name__)

# ------------------ BhashaBuddy - Language Support ------------------
LANGUAGE_DATA = {
    "hi": {
        "welcome": "कृषि मित्र में आपका स्वागत है!",
        "today_task": "आज के लिए आपका कार्य:",
        "no_task": "आज कोई विशेष कार्य नहीं है।",
        "price_info": "मंडी मूल्य जानकारी:",
        "disease_detected": "फसल रोग का पता चला:",
        "govt_scheme": "सरकारी योजना विवरण:",
    },
    "en": {
        "welcome": "Welcome to KrishiMitra!",
        "today_task": "Your task for today:",
        "no_task": "No specific task for today.",
        "price_info": "Mandi price information:",
        "disease_detected": "Crop disease detected:",
        "govt_scheme": "Government scheme details:",
    },
    "bn": {
        "welcome": "কৃষিমিত্রতে আপনাকে স্বাগতম!",
        "today_task": "আজকের কাজ:",
        "no_task": "আজ কোন নির্দিষ্ট কাজ নেই।",
        "price_info": "মন্ডির মূল্য তথ্য:",
        "disease_detected": "ফসলের রোগ সনাক্ত হয়েছে:",
        "govt_scheme": "সরকারি প্রকল্প বিবরণ:",
    }
    # More languages can be added
}

# ------------------ Farming Tasks ------------------
TASKS = [
    "Check soil moisture levels",
    "Irrigate tomato crops",
    "Spray pesticide for aphids",
    "Harvest brinjal",
    "Apply organic fertilizer to field"
]

# ------------------ Mock Data for Features ------------------
MANDI_PRICES = {
    "wheat": "₹2200/qtl",
    "rice": "₹1800/qtl",
    "mustard": "₹5500/qtl"
}

CROP_DISEASES = {
    "image1.jpg": "Blight disease in potato",
    "image2.jpg": "Rust in wheat leaves"
}

GOVT_SCHEMES = {
    "PM-KISAN": "Rs. 6000 per year to eligible farmers",
    "PMFBY": "Crop insurance at nominal premium rates",
    "Soil Health Card": "Free soil testing and nutrient advice"
}

# ------------------ Helpers ------------------
def translate(key, lang='en'):
    return LANGUAGE_DATA.get(lang, LANGUAGE_DATA['en']).get(key, key)

# ------------------ Routes ------------------
@app.route('/')
def index():
    lang = request.args.get('lang', 'en')
    return jsonify({"message": translate("welcome", lang)})

@app.route('/task')
def get_daily_task():
    lang = request.args.get('lang', 'en')
    today = datetime.now().day
    task = TASKS[today % len(TASKS)] if today % 2 == 0 else None
    return jsonify({
        "title": translate("today_task", lang),
        "task": task if task else translate("no_task", lang)
    })

@app.route('/mandi')
def get_mandi_prices():
    lang = request.args.get('lang', 'en')
    return jsonify({
        "title": translate("price_info", lang),
        "prices": MANDI_PRICES
    })

@app.route('/disease-detect')
def detect_disease():
    lang = request.args.get('lang', 'en')
    # Simulated disease detection from image name (replace with ML model in real app)
    image_name = request.args.get('image', 'image1.jpg')
    disease = CROP_DISEASES.get(image_name, "No disease detected")
    return jsonify({
        "title": translate("disease_detected", lang),
        "result": disease
    })

@app.route('/scheme')
def govt_scheme():
    lang = request.args.get('lang', 'en')
    scheme = request.args.get('name', 'PM-KISAN')
    info = GOVT_SCHEMES.get(scheme, "Scheme not found")
    return jsonify({
        "title": translate("govt_scheme", lang),
        "info": info
    })

if __name__ == '__main__':
    app.run(debug=True)
