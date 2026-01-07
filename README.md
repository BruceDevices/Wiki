# Editing Wiki

## Prerequisites

### Install Python (if not already installed)

* Download Python from:
https://www.python.org/downloads/windows/

During install, check “Add Python to PATH”

OR

* Install from the Windows Store

### Clone or Open Wiki Repository

```
git clone https://github.com/your/repo.git
cd repo
```

### Create a Virtual Environment (recommended)

```
python -m venv venv
venv\Scripts\activate
```

You should see (venv) in the prompt.

### Install Requirements

```
pip install -r requirements.txt
```


## Serve the Site Locally

```
python -m mkdocs serve --livereload
```

You should see output like:

```
INFO     - Serving on http://127.0.0.1:8000/
```


### Open in Your Browser

http://localhost:8000

Edits to Markdown files will auto-reload.


## Build Static Files (optional)

To generate the site into a site/ folder run:

```
mkdocs build
```