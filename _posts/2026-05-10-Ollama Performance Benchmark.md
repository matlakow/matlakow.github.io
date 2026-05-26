---
layout: post
title:  "Ollama Performance Benchmark"
author: matlakow
categories: [ linux, ai, ollama, macbook ]
---

<h1 style="text-align:center; margin-bottom:40px;">

**P106-090 vs Apple M1 Air**

</h1>

<img src="{{ '/assets/images/logo_post_10052026.png' | relative_url }}" alt="My logo">


I recently decided to benchmark my local Ollama setup before upgrading the GPU in my inference server.
The goal was simple: establish a reliable baseline and measure the real-world improvement after replacing the graphics card.

Test Setup

Server GPU

* NVIDIA P106-090 6 GB
* Ollama running on Linux
* Model: qwen2.5:7b

Laptop

* Apple MacBook Air M1 16 GB
* Ollama running locally
* Same model and prompt

Benchmark Command:
*curl -s http://localhost:11434/api/generate
-d '{
"model": "qwen2.5:7b",
"prompt": "Write a detailed explanation of how an internal combustion engine works.",
"stream": false,
"options": {
"num_predict": 512,
"num_ctx": 4096,
"temperature": 0,
"seed": 123
}
}' | jq*

The important metrics returned by Ollama are:

* eval_count — generated tokens
* eval_duration — generation time
* prompt_eval_count — prompt tokens
* prompt_eval_duration — prompt processing time

Results

NVIDIA P106-090 6 GB

Average generation speed:
~11.45 tokens/sec

Apple M1 Air 16 GB
~13.0 tokens/sec

Surprise Result

The Apple M1 Air was actually around 14% faster than the old Pascal-based NVIDIA mining GPU.

This is not entirely surprising once you consider:

* the P106 is based on older Pascal architecture
* it has no Tensor Cores
* only 6 GB VRAM
* Apple Silicon performs surprisingly well for small and medium LLM inference workloads

Key Takeaways

For lightweight 7B models:

* Apple Silicon is extremely competitive
* old mining GPUs are no longer a great value for LLM inference
* modern RTX cards would provide a massive jump in performance
