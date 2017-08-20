---
layout: post
title: Convert Markdown to Google AMP using Amper
---

[Amper](https://github.com/iSatishYadav/Amper) is a REST-API which converts **Markdown** text to Google's AMP syntax.


## Usage
Send a POST request with:

1. URL: http://amper.satishyadav.com/amp
2. Content-Type: ````application/x-www-form-urlencoded````
3. Parameter "md" with the _Markdown_ syntax.

Output will be amp html.

Visit [this GitHub repo](https://github.com/iSatishYadav/Amper) for examples in C#, JavaScript, Node, PHP to consume this.
