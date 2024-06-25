# GeminiChatBot-Webpage

A responsive web interface for interacting with Gemini, a chat bot powered by AI, capable of handling various queries and tasks. Includes features for uploading images and interacting with the bot seamlessly.

## Features

- User-friendly interface for chatting with Gemini
- Image upload functionality with real-time feedback
- Responsive design for desktop and mobile devices

## Prerequisites

- Web server with CGI support (e.g., Apache, Nginx)
- Python 3.x
- AWS CLI configured with the necessary permissions for S3 operations
- Google Generative AI Python SDK

## Installation

1. **Clone the repository:**

    ```bash
    git clone https://github.com/dilip8700/GeminiChatBot-Webpage.git
    cd GeminiChatBot-Webpage
    ```

2. **Set up the web server:**

    Ensure your web server is configured to execute CGI scripts. For Apache, this typically involves enabling the `cgi` module and configuring a directory to allow CGI execution.

    Example Apache configuration:

    ```apache
    <Directory "/var/www/cgi-bin">
        Options +ExecCGI
        AddHandler cgi-script .py
    </Directory>
    ```

3. **Place the HTML and CGI script:**

    - Copy the `index.html` to your web server's root directory (e.g., `/var/www/html/`).
    - Place the CGI script (`s3_upload.py` and `gemini_chat.py`) in the appropriate CGI directory (e.g., `/var/www/cgi-bin/`).

4. **Install required Python packages:**

    Install the necessary Python packages, including the Google Generative AI SDK and `boto3` for AWS operations:

    ```bash
    pip install google-generativeai boto3
    ```

5. **Set up the upload directory:**

    Create the directory where uploaded images will be stored:

    ```bash
    mkdir -p /var/www/cgi-bin/myupload
    ```

6. **Ensure proper permissions:**

    Set the necessary permissions for the upload directory and the CGI scripts:

    ```bash
    chmod +x /var/www/cgi-bin/s3_upload.py
    chmod +x /var/www/cgi-bin/gemini_chat.py
    chmod 755 /var/www/cgi-bin/myupload
    ```

7. **Configure the Google Generative AI API:**

    Replace `"Enter Your Access Key Here"` in `gemini_chat.py` with your actual Google Generative AI API key.

## Usage

1. **Open the web interface:**

    Navigate to the URL where you placed `index.html` in your web browser (e.g., `http://your-server-ip/index.html`).

2. **Interact with Gemini:**

    Use the chat interface to communicate with Gemini and upload images as needed.

3. **Chat with Gemini:**

    The backend `gemini_chat.py` script handles chat interactions. When you submit a prompt through the web interface, the prompt is sent to this script, which processes it using the Google Generative AI API and returns the response.

## Backend Script Explanation

### `gemini_chat.py`

This Python CGI script handles chat interactions with the Gemini bot using the Google Generative AI API.

```python
#!/usr/bin/python3

import cgi
import os
import google.generativeai as genai

print("Content-Type: text/plain\n")

# Configure the API key
genai.configure(api_key="Enter Your Access Key Here")

# Define the generation configuration
generation_config = {
    "temperature": 1,
    "top_p": 0.95,
    "top_k": 64,
    "max_output_tokens": 8192,
    "response_mime_type": "text/plain",
}

# Create the generative model
model = genai.GenerativeModel(
    model_name="gemini-1.5-flash",
    generation_config=generation_config,
)

# Start a chat session
chat_session = model.start_chat(history=[])

# Parse form data
form = cgi.FieldStorage()
prompt = form.getvalue('prompt')

# Send the prompt to the model and get the response
response = chat_session.send_message(prompt)
print(response.text)
