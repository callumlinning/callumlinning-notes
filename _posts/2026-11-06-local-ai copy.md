---
layout: post
title: "Local AI is more interesting than I expected"
description: "Some thoughts from experimenting with local models, Ollama and running AI on personal hardware."
date: 2026-11-06
---

## Running models locally

The interesting part isn't necessarily replacing cloud models. It's understanding where local inference makes sense.

For example, a simple Python function might look like this:

```python
def analyse_activity(activity):
    return {
        "distance": activity["distance"],
        "heart_rate": activity["average_hr"]
    }
```

There are obvious constraints around memory and model size, but techniques such as quantisation and mixture of experts make the picture much more interesting.

<figure>
  <img src="/assets/images/local-ai/ollama.jpg"
       alt="Ollama running a local language model">

  <figcaption>
    Running a local model through Ollama.
  </figcaption>
</figure>

<div class="video">
  <iframe
    src="https://www.youtube.com/embed/anf6YEhDD7E"
    title="Video title"
    allowfullscreen>
  </iframe>
</div>

<div class="callout">
  <strong>Worth noting</strong>
  <p>
    Running a model locally doesn't necessarily mean
    replacing cloud-based AI.
  </p>
</div>