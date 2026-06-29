---
layout: post
title: "GenerateHTTPHandlersJSON — your routes live in the code"
date: 2026-06-29
component: GenerateHTTPHandlersJSON
component_url: https://github.com/mesopelagique/GenerateHTTPHandlersJSON
version: 0.0.1
---

```4d
// @GET(/hello)
Function getHello($request : 4D.IncomingMessage) : 4D.OutgoingMessage
	var $response:=4D.OutgoingMessage.new()
	$response.setBody("Hello, world!")
	return $response
```

That little `// @GET(/hello)` comment is the whole idea.
[**GenerateHTTPHandlersJSON**](https://github.com/mesopelagique/GenerateHTTPHandlersJSON)
**extracts those comments and produces your `HTTPHandlers.json` for you** — no leaving the 4D
code editor to hand-edit a config file. Declare the route right next to the function that
serves it, like you would in any other web framework.

## Run it

```4d
GenerateHTTPHandlers
```

It scans the **shared singleton** classes in `Project/Sources/Classes`, finds every
`Function` that takes a `4D.IncomingMessage` and returns a `4D.OutgoingMessage`, reads the
`// @VERB(pattern)` line right above it, and writes the result to
`Project/Sources/HTTPHandlers.json`:

```json
[
	{ "class": "MyAPI", "method": "getHello", "pattern": "/hello", "verbs": "get" }
]
```

Then just restart the Web Server so it reloads the file. Call it on demand, from a CI step —
or, when included as a component that's authorized for host database events, it runs
automatically `On after host database startup`.

## Install

It's 🧩 dependency-manager ready. Drop it into your `dependencies.json`:

```json
{
  "dependencies": {
    "GenerateHTTPHandlersJSON": {
      "github": "mesopelagique/GenerateHTTPHandlersJSON",
      "version": "0.0.1"
    }
  }
}
```

Prefer to stay self-contained? Copy the single
[`GenerateHTTPHandlers`](https://github.com/mesopelagique/GenerateHTTPHandlersJSON/blob/main/Project/Sources/Methods/GenerateHTTPHandlers.4dm)
method into your own project instead.

---

Browse the full 4D component catalog on the [home page](/), and more AI / Swift / 4D notes over
on [phimage.github.io](https://phimage.github.io/).
