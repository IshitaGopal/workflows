# APIs

## The core idea
An **API** is just a server sitting somewhere (here: http://127.0.0.1:8000) that accepts requests over HTTP and sends back responses. A **client** is just the thing making those requests — in our case, a small Python object (httpx.Client, wrapped by destiny_sdk's OAuthClient) that knows the server's address and handles the boring plumbing (auth headers, JSON encoding) for you.

## The four verbs that matter here
1. **GET** — "show me what's there." Read-only, doesn't change anything. client.get("/robots/<id>/") = "give me this robot's current data."

2. **POST** — "create something new." POST /robots/ = "register a brand-new robot," which is why it hands back a new ID + secret.

3. **PUT** — "replace this thing entirely with what I'm sending you." PUT /robots/<id>/ = "here's the complete new state of this robot" — not a patch, the whole object. That's why the GET-then-PUT dance was necessary: you have to fetch the current name/description/owner first, or your PUT would blank them out.

4. (There's also **DELETE**, which this API doesn't even expose for robots — hence no way to remove one.)
Why GET-then-PUT specifically


Robots don't support partial updates. So the pattern is always:
GET the current object → see what's there now.
Take that, change the one field you care about (entitlements).
PUT the whole thing back.

`client = OAuthClient(base_url="http://127.0.0.1:8000", auth=httpx.Auth()).get_client()`

After this runs, `client` is just a variable holding a Python object — think of it like a little assistant that now permanently knows "the server lives at http://127.0.0.1:8000." You don't have to type that address again for every request; you just say `client.get(...)` or `client.put(...)` and it fills that part in for you.

"knows the server's address" — that's just the base_url you gave it. Every call like `client.get("/robots/<id>/")` gets glued onto that base automatically — you're really hitting `http://127.0.0.1:8000/robots/<id>/.`

"auth headers" — every HTTP request can carry extra metadata called headers, and one of them (Authorization) is how you prove who you are. Instead of you manually attaching "hey here's my token" to every single request, the client does it automatically once it's configured — that's the whole point of the `auth= argument`.

"JSON encoding" — Python has its own data shapes (dicts, sets, custom objects like RobotIn). The server only understands plain text in a format called JSON. So when we did:

`resp = client.put(f"/robots/{robot_id}/", json=updated.model_dump(mode="json"))
`
`model_dump(mode="json")` converts our Python object into a JSON-shaped dict, and `client.put(..., json=...)` turns that into text and sends it. On the way back, .json() (like in `current = client.get(...).json()`) does the reverse — turns the server's text response back into a normal Python dict you can index with current["name"].

So the one-sentence version: the client object remembers the address and your credentials so every `client.get(...) / client.put(...)` call is just "send this data, get data back" — you never have to hand-write the raw HTTP request yourself.

## curl
curl is a command-line tool that does the exact same thing as `client.get(...)` in Python, except you type it straight into the terminal — no Python needed at all. It's the fastest way to just poke a URL and see what comes back.

Here's what each one we ran actually did:
`curl -s http://127.0.0.1:8000/robots/019fa9ba-2c7c-7777-a341-93c72f1c1d2b/

`

— just print the raw response body to the terminal. This is the plain "show me what this returns" version, equivalent to `client.get(...)`.json() but skipping Python entirely.
