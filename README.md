from flask import Flask, request, render_template, jsonify
import requests

app = Flask(__name__)

# Replace this with the actual API endpoint for Brawl Stars if available
API_BASE_URL = "https://api.brawlstars.com/v1"
API_TOKEN = "your_api_token_here"  # Replace with your Brawl Stars API token

@app.route('/')
def home():
    return render_template('index.html')

@app.route('/profile', methods=['GET'])
def get_profile():
    tag = request.args.get('tag')
    if not tag:
        return jsonify({"error": "Player tag is required"}), 400

    # Clean the tag (remove # and spaces)
    tag = tag.replace("#", "").strip()

    headers = {
        "Authorization": f"Bearer {API_TOKEN}"
    }
    url = f"{API_BASE_URL}/players/%23{tag}"

    try:
        response = requests.get(url, headers=headers)
        response.raise_for_status()
        data = response.json()
        return jsonify(data)
    except requests.exceptions.RequestException as e:
        return jsonify({"error": "Failed to fetch player data", "details": str(e)}), 500

if __name__ == '__main__':
    # Run Flask without debug mode and multiprocessing to avoid sandbox errors
    app.run(debug=False, use_reloader=False)
