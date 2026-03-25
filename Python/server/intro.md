## Starting a Simple Local Server in Python

### Command

```bash
python -m http.server 8000
```

### What it does

* Uses http.server
* Starts a lightweight HTTP server
* Serves files from the **current working directory**
* Runs on `http://localhost:8000`

---

## Steps

1. Navigate to your project:

```bash
cd toy-robot
```

2. Start server:

```bash
python -m http.server 8000
```

3. Open in browser:

```
http://localhost:8000
```

---

## Options

* Change port:

```bash
python -m http.server 9000
```

* Bind to a specific host:

```bash
python -m http.server 8000 --bind 127.0.0.1
```

---

## Stop server

```
Ctrl + C
```

---

## What you get

* Directory listing in browser
* Clickable files (HTML, images, etc.)
* Quick static file hosting

---


## Common Use Cases

* Testing static websites
* Viewing HTML/CSS locally
* Sharing files on your machine/network
* Quick debugging without setup

---

