# Hands-On - Hello World API

By the end of this exercise you will have a working FastAPI application running in Google Cloud Shell that responds to HTTP requests. Every step builds on the previous one — work through them in order and don't skip ahead.

If you get stuck, check with a neighbor before raising your hand. You're often stuck on the same thing.

---

## Part 1: Create a GitHub Repository

1. Go to [github.com](https://github.com) and sign in
2. Click the **+** icon in the top right corner and select **New repository**
3. Name it `hello-world-api`
4. Set visibility to **Public**
5. Check **Add a README file**
6. Click **Create repository**

Once created, click the green **Code** button and copy the HTTPS URL — you'll need it shortly. It will look like:

```
https://github.com/your-username/hello-world-api.git
```

---

## Part 2: Open Google Cloud Shell

Go to [shell.cloud.google.com](https://shell.cloud.google.com) and sign in with your Purdue Google account. Cloud Shell will open a terminal in your browser.

Click the **Open Editor** button (the pencil icon in the Cloud Shell toolbar) to open the built-in code editor alongside your terminal. You'll use both throughout this exercise — the editor for writing code, the terminal for running commands.

> **What is Cloud Shell?** It's a Linux environment that runs entirely in your browser. Google provides it free with your GCP account. Python, Git, and most developer tools are pre-installed. Your home directory persists between sessions, but don't rely on it alone — you'll push everything to GitHub as a backup.

---

## Part 3: Configure Git in Cloud Shell

Git needs to know who you are before it can record commits. This is a one-time setup per environment. Run these two commands in the terminal, replacing the name and email with your own:

```bash
git config --global user.name "Your Name"
git config --global user.email "your@email.com"
```

Verify it took:

```bash
git config --global --list
```

You should see your name and email in the output.

> **Why does this matter?** Every commit you make is stamped with your name and email. When you're working on a team project, this is how your teammates (and your instructor) know who wrote what.

---

## Part 4: Clone the Repository

In the terminal, clone your GitHub repository into Cloud Shell:

```bash
git clone https://github.com/your-username/hello-world-api.git
cd hello-world-api
```

In the editor, click **File → Open Folder** and navigate to your `hello-world-api` folder. This sets the editor's working directory to your project — all new files you create will appear here.

---

## Part 5: Set Up Your Project with Poetry

Install Poetry if it isn't already available:

```bash
curl -sSL https://install.python-poetry.org | python3 -
```

> [!IMPORTANT]
> **READ THE OUTPUT!!** The Poetry installer will tell you to run a command that adds Poetry to your PATH. This command will include your username, so copy it directly from the output.
> 
> _For example, my command is: `export PATH="/home/ree/.local/bin:$PATH"`_
> 
> Copy and paste your command on the command line. Then press Enter.

After installation, verify it works:

```bash
poetry --version
```

> **If the command isn't found:** Run `export PATH="$HOME/.local/bin:$PATH"` and try again. You can also add this line to your `~/.bashrc` file so it persists across sessions.

Initialize a new Poetry project for dependency management only:

```bash
poetry init --no-interaction
```

This creates a `pyproject.toml` file in your project folder. You should see it appear in the editor's file tree on the left. Click on it to open it.

Find the `[tool.poetry]` section and add `package-mode = false` so Poetry knows this project is not an installable package:

```toml
[tool.poetry]
name = "hello-world-api"
version = "0.1.0"
description = ""
package-mode = false
```

Save the file with **File → Save** (or `Ctrl+S`).

Now **use the Terminal** to add FastAPI and Uvicorn (the server that runs FastAPI) as dependencies:

```bash
poetry add fastapi "uvicorn[standard]"
```

Poetry will resolve and install the packages. When it finishes, you'll see a `poetry.lock` file appear in the editor's file tree alongside `pyproject.toml`.

> **What just happened?** Poetry created an isolated virtual environment for this project and installed FastAPI and Uvicorn into it. It also recorded the exact versions in `poetry.lock`. Anyone who clones this repo and runs `poetry install` will get the exact same environment.

---

## Part 6: Write Your First API

In the editor, click **File → New File** and name it `main.py`. Add the following code:

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def root():
    """
    Create a root route that responds with a static message.
    """
    return {"message": "Hello, World!"}

@app.get("/hello/{name}")
def hello(name: str):
    """
    Create a route that responds with custom output taken from input.
    """
    return {"message": f"Hello, {name}!"}
```

Save the file with `Ctrl+S`.

**What's happening here:**

- `FastAPI()` creates your application
- `@app.get("/")` tells FastAPI to call the `root()` function when someone sends a GET request to `/`
- `@app.get("/hello/{name}")` defines a route with a **path parameter** — the `{name}` in the URL becomes the `name` argument in the function
- Both functions return a Python dictionary, which FastAPI automatically converts to JSON

---

## Part 7: Run Your API

In the terminal, start your API with Uvicorn via Poetry:

```bash
poetry run uvicorn main:app --reload --host 0.0.0.0 --port 8080
```

You should see output like:

```
INFO:     Uvicorn running on http://0.0.0.0:8080 (Press CTRL+C to quit)
INFO:     Started reloader process
```

> **`--reload`** means the server automatically restarts when you save changes to your code — very useful during development.

> **`--host 0.0.0.0`** makes the server accessible from outside Cloud Shell, which is required for the Web Preview to work.

To view your API in the browser, click the **Web Preview** button in the Cloud Shell toolbar (the icon that looks like a box with an arrow) and select **Preview on port 8080**. A new browser tab will open showing your API's JSON response.

Try editing the URL in that tab to add `/hello/your-name` — for example:

```
https://8080-...your-cloud-shell-url.../hello/Purdue
```

You should see:

```json
{"message": "Hello, Purdue!"}
```

---

## Part 8: Explore Swagger UI

FastAPI automatically generates interactive documentation for your API. With your server still running, edit the URL in your preview tab to go to `/docs`:

```
https://8080-...your-cloud-shell-url.../docs
```

You'll see the Swagger UI — a list of all your endpoints. Click on **GET /hello/{name}**, then **Try it out**, enter a name, and click **Execute**. Swagger sends the request and shows you the full response including status code and headers.

This is how we'll test APIs throughout the course — no extra tools needed.

---

## Part 9: Update your README.md
Your README file is your "home page" for your remote repository.

For public repositories, it's a great place to explain the organization and purpose of the software.

For private or internal repositories, it's a way to communicated with the rest of your team.

Update your `README.md` file with a simple introductory message.

```markdown
# Rob's Hello World API

This is my first API.
```
## Part 10: Commit and Push to GitHub

Stop the server with `CTRL+C` in the terminal.

You can commit directly from the editor. Click the **Source Control** icon in the left sidebar (it looks like a branch with three dots). You'll see `main.py`, `pyproject.toml`, and `poetry.lock` listed as new files.

Click the **+** next to each file to stage it, type a commit message like `Initial Hello World API` in the message box at the top, and click **Commit**. Then click **Sync Changes** to push to GitHub.

Alternatively, from the terminal:

```bash
git add .
git commit -m "Initial Hello World API"
git push
```

Go to your GitHub repository in the browser and confirm the three files are there.

> **Get in the habit:** Every time you step away from Cloud Shell, commit and push. Cloud Shell environments can reset. GitHub is your safety net.

---

## ✅ You're Done When...

- [ ] Git is configured with your name and email in Cloud Shell
- [ ] Your `hello-world-api` repo exists on GitHub
- [ ] `package-mode = false` is set in `pyproject.toml`
- [ ] Your API runs and responds at `/` and `/hello/{name}`
- [ ] Swagger UI loads at `/docs`
- [ ] All three files are pushed to GitHub

---

## 🤖 AI Usage Guidelines

You're welcome to use AI tools to help you understand what the code is doing or to troubleshoot errors. However, try to type the code yourself rather than pasting AI-generated code wholesale — the muscle memory of writing these patterns by hand pays off quickly.

If you use AI to resolve an error, make sure you understand what it changed and why before moving on.
