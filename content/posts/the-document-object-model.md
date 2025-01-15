+++
title = "The Document Object Model (DOM)"
date = 2025-01-14T17:47:00+01:00
draft = true
tags = ["frontend", "browser"]
categories = ["web development"]
+++
The DOM, or **Document Object Model**, is a programming interface provided by the browser that allows programming languages to interact with and modify the structure and content of the currently loaded document, most often an HTML document.

When a browser loads a webpage, it parses the HTML markup, constructing a tree-like structure that represents the structure of the document.

Every part of this tree is called a node. Nodes can represent:

- **Element nodes** (e.g., `<div>`, `<a>`, `<img>`)
- **Text nodes** (e.g., text inside an element)
- **Attribute nodes** (e.g., `id="myInput"`)
- **Document nodes** (the entire document itself)

This is an example of the tree structure:

```plaintext
<html> (Document Node)
└── <body> (Element Node)
    ├── <h1> (Element Node)
    │    └── "Hello World!" (Text Node)
    └── <input> (Element Node)
         └── id="myInput" (Attribute Node)

```

We can then use JavaScript to read, modify and update this tree via the [**Document API**](https://developer.mozilla.org/en-US/docs/Web/API/Document). E.g., grabbing a specific HTML element by its id:

```javascript
const myInput = document.getElementById('#myInput')
```

Then adding an event listener to validate it on user input:

```javascript
myInput.addEventListener('change', (event) => {
    if (typeof event.currentTarget.value !== 'string') {
        alert("Input value must be a string!")
    }
})
```
