# info-extractor

Creating a project like "info-extractor" involves several components, including web development, natural language processing (NLP), and error handling. Below is a simplified example of how you might structure such a Python program. For the purposes of this example, we'll use Python's Flask framework for the web application, along with the spaCy library for NLP tasks.

First, make sure you have Flask and spaCy installed. You can install them via pip:

```bash
pip install Flask
pip install spacy
python -m spacy download en_core_web_sm
```

Now, let's create the main components of the program:

### app.py

```python
from flask import Flask, request, jsonify
import spacy

app = Flask(__name__)

# Load the spaCy model
try:
    nlp = spacy.load('en_core_web_sm')
except Exception as e:
    # Log error messages if the spaCy model fails to load
    print(f"Error loading spaCy model: {e}")

@app.route('/')
def index():
    return "Welcome to the Info Extractor!"

@app.route('/extract', methods=['POST'])
def extract_info():
    # Ensure the request data is JSON format
    if not request.is_json:
        return jsonify({"error": "Invalid input, please provide JSON"}), 400

    content = request.get_json().get('text')
    if not content:
        return jsonify({"error": "No text provided"}), 400
    
    try:
        # Process the content text using spaCy
        doc = nlp(content)
        
        # Example: Extract named entities
        entities = [{'text': ent.text, 'label': ent.label_} for ent in doc.ents]

        return jsonify({"entities": entities})

    except Exception as e:
        # Log any unexpected exceptions during processing
        return jsonify({"error": str(e)}), 500

if __name__ == '__main__':
    # Run the app on the default port 5000
    app.run(debug=True)
```

### Explanation of the Code

- **Flask Web Application**: This is a simple Flask app with two endpoints:
  - `'/'`: A welcome page.
  - `'/extract'`: An endpoint that accepts POST requests with JSON data containing the text to analyze.

- **Error Handling**:
  - We handle loading errors for the spaCy model (e.g., if the model is not properly downloaded).
  - We ensure the request payload is JSON and contains the necessary `text` field.
  - We catch exceptions during NLP processing and return an appropriate error message.

- **NLP with spaCy**:
  - We use spaCy to process text and extract named entities (like names, organizations, etc.). This example focuses on basic entity extraction.

- **Running the App**:
  - The app runs on `localhost` at port `5000`. You can open your browser or use a tool like `curl` or Postman to test the `/extract` endpoint.

### Testing the Application

1. Start the Flask application by running:

   ```bash
   python app.py
   ```

2. Test the `/extract` endpoint with a POST request. You can use `curl` or Postman:

   ```bash
   curl -X POST http://localhost:5000/extract -H "Content-Type: application/json" -d "{\"text\":\"Barack Obama was the 44th President of the United States.\"}"
   ```

3. You should receive a JSON response with the extracted entities, for example:

   ```json
   {
     "entities": [
       {"text": "Barack Obama", "label": "PERSON"},
       {"text": "44th", "label": "ORDINAL"},
       {"text": "United States", "label": "GPE"}
     ]
   }
   ```

This example provides a base framework for an information extractor. Depending on your requirements, you can enhance the NLP component to extract other patterns or insights and improve error handling.