# SiteSummary

SiteSummary is a simple AI website summarizer. The user enters a website URL, the app fetches the page content, removes noisy page elements, and asks an AI model to return a short, friendly markdown summary.

## What the project does

1. Takes a website URL from the user.
2. Adds `https://` automatically if the URL does not include a scheme.
3. Downloads the webpage HTML.
4. Removes scripts, styles, navigation, headers, footers, images, and inputs.
5. Extracts the page title and readable text.
6. Sends the extracted content to a Groq-hosted Llama model using the OpenAI-compatible API.
7. Shows the generated summary in a Gradio web interface.

## Project structure

```text
sitesummary/
  app.py          # Starts the Gradio web app
  scraper.py      # Fetches and cleans website text
  summarizer.py   # Sends website text to the AI model and returns a summary
  README.md       # Project documentation
```

There are also a few files in the parent folder:

```text
first_call.py     # Test script for calling Groq with the OpenAI client
openai.py         # Test script for calling OpenAI directly
a.py              # Small experiment file
.env              # Stores API keys locally
```

## Main files explained

### `app.py`

This file creates the user interface with Gradio.

It imports the `summarize` function from `summarizer.py`, creates a textbox for the website URL, and displays the result as markdown. The app is launched with `share=True`, which creates a public Gradio link.

### `scraper.py`

This file contains `fetch_website_contents(url)`.

It uses `requests` to download the website and BeautifulSoup to parse the HTML. It removes common non-content tags such as `script`, `style`, `nav`, `footer`, and `header`, then returns the page title and cleaned page text.

### `summarizer.py`

This file loads the API key from `.env`, creates an OpenAI-compatible client for Groq, and defines `summarize(url)`.

The function gets the cleaned website content from `scraper.py`, sends it to the `llama-3.3-70b-versatile` model, and returns the AI-generated summary.

## Requirements

Install these Python packages:

```bash
pip install gradio requests beautifulsoup4 python-dotenv openai
```

## Environment setup

Create a `.env` file in the `sitesummary` folder, or in the folder you run the app from, with your Groq API key:

```env
GROQ_API_KEY=your_groq_api_key_here
```

The key is loaded in `summarizer.py` with `python-dotenv`.

## How to run

From the project root, move into the `sitesummary` folder:

```bash
cd sitesummary
```

Run the Gradio app:

```bash
python app.py
```

After running the command, Gradio will show a local URL in the terminal. Because `share=True` is enabled, it can also create a public share link.

## Example use

Enter a URL such as:

```text
https://example.com
```

The app will return a short markdown summary of the website.

## Notes and limitations

- Some websites block scraping requests.
- JavaScript-heavy websites may not return much useful text because this project does not run browser JavaScript.
- Very large pages may produce too much text for the model context window.
- The summary quality depends on the extracted page content.

## Possible improvements

- Add a `requirements.txt` file.
- Add better error handling when the API key is missing.
- Limit or chunk very long website text before summarizing.
- Add support for multiple summary styles, such as bullet points, detailed summaries, or beginner-friendly explanations.
- Set `share=False` by default if the app only needs to run locally.
