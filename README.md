# Persona Fight Club

Paste a product idea. 10 AI personas argue about it in a live group chat, then each votes on whether they would pay for it. A Survival Score is calculated from the votes.

Everything runs on your own machine. No API keys, no accounts, no backend, and nothing you type is sent anywhere. The AI is a small open model served locally by [Ollama](https://ollama.com).

![Survival Score result](main/screenshot-result.png)

## Features

- 10 personas: The Cynical VC, The Broke College Student, The Enterprise CFO, The Gen-Z TikToker, The Grumpy Senior Engineer
- Two rounds of replies that stream in one persona at a time
- **Mock mode (default):** canned replies, works with zero setup
- **Local model mode:** real replies from Ollama, with a model picker that lists whatever you have installed
- **Verdict step:** each persona votes yes, maybe or no with a monthly price and a one-line quote
- **Survival Score:** calculated in code, not by the model (yes = 100, maybe = 50, no = 0, averaged)

## Quick start (mock mode)

No install needed beyond Python 3, which comes with macOS.

```bash
git clone <your-repo-url>
cd persona-fight-club
python3 -m http.server 8000
```

Open http://localhost:8000 and click **Start the fight**.

## Local model setup

1. Install Ollama from https://ollama.com
2. Download a model:
   ```bash
   ollama pull llama3.2:3b
   ```
3. Allow the web page to talk to Ollama, then quit and reopen Ollama.
   - macOS: `launchctl setenv OLLAMA_ORIGINS "*"` (this resets after a reboot, so run it again if needed)
   - Linux or Windows: set the `OLLAMA_ORIGINS` environment variable to `*` and restart Ollama
4. Serve the folder (`python3 -m http.server 8000`), open http://localhost:8000, switch the toggle to **Local model**, pick a model and start the fight.

`*` lets any web page call your local Ollama. You can narrow it to `http://localhost:8000` if you prefer, as long as you always open the app from that address.

The first reply takes 10 to 20 seconds while the model loads. On an 8 GB Mac, stick to models of about 4B parameters or smaller and close heavy apps.

## How it works

There is no backend. The browser page (`index.html`, `style.css`, `app.js`) builds a prompt for each persona and sends it to Ollama at `http://localhost:11434`. Ollama runs the model on your machine and streams the words back. The Python command above only serves the three files.

```
Browser page  ->  Ollama (localhost:11434)  ->  local model
```

## Known issues

- Tested mainly with `llama3.2:3b`. Some other models worked in the terminal but failed inside the app, and the page then shows a generic "Can't reach Ollama" message even though Ollama is running.
- Small models tend to sound alike and sometimes ignore the JSON format in the verdict step. The app falls back to "maybe, $0" when that happens.
- Mock mode only reacts to a few keywords (price, free, AI, subscription, app).
- This is a toy, not market research.

## Project structure

```
index.html   page structure
style.css    styling
app.js       personas, prompts, Ollama calls, scoring
docs/        screenshots
```
