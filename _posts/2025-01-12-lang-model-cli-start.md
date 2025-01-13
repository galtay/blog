---
title: "started building a large language model cli"
date: 2025-01-12
tags: llm litellm cli
---

I wanted to learn a little more about [uv](https://github.com/astral-sh/uv)
(Python project and package manager) and I wanted to experiment with
a command line interface for large language models
so I wrote a little python package called
[lang-model-cli](https://github.com/galtay/lang-model-cli).
In this post I'll briefly walk through some of the components.

# Project Management

When [creating a new project](https://docs.astral.sh/uv/concepts/projects/init/)
with `uv init` you have three options,

* application (default or explicitly with --app): This is a light-weight option with no build system and no package.
Use this if you just need a python environment with well defined dependencies for a few scripts.
* package (--package): This option creates a `src` directory and provides a build system. Use this if you want something installable that you can publish to places like PyPI.
* library (--lib): A packge intended to be used by other packages. Includes everything that package does plus a [`py.typed`](https://typing.readthedocs.io/en/latest/spec/distributing.html#packaging-typed-libraries) file.

For my project I used,

```bash
uv init --package lang-model-cli
```

I created a `main.py` file with a `main` function in the `src/lang_model_cli` directory and added the following line to the `pyproject.toml` file to provide the `lang-model-cli` and `lmc` commands once the package is installed,

```bash
[project.scripts]
lang-model-cli = "lang_model_cli.main:main"
lmc = "lang_model_cli.main:main"
```

For publishing to PyPI I did,

```bash
uv build
uv publish -t <pypi_token>
```

Eventually I'll write a github action to automate publishing to PyPI when the main github branch is updated.

# LLM Provider Standardization

I chose [LiteLLM](https://github.com/BerriAI/litellm)
to have a unified "OpenAI style" interface to various LLM providers.
I haven't done any comprehensive tests, but it supports a long
[list of providers](https://docs.litellm.ai/docs/providers)
and the OpenAI API has become popular enough that many other services
(e.g. [vllm](https://github.com/vllm-project/vllm)) offer compatibility layers.
The package also seems up-to-date on things like streaming and formats such as image, audio, and pdf.
If provider specific functionality is released that is hard to use via an OpenAI interface,
then it should be easy enough to specialize for those cases later.

# Rich Formatting

The [rich](https://github.com/Textualize/rich) library provides "rich text and beautiful formatting in the terminal". It's a great way to add visual appeal to command line interfaces or anything else you might be doing in the terminal with Python.


# Command Line Interface

The command line interface iteself is implemented with [typer](https://typer.tiangolo.com/).
This package allows for the definition of CLI arguments and options as Python function arguments.
It also provides good looking output on the terminal.
It can be
[noticeably slow](https://github.com/fastapi/typer/discussions/744)
when starting up due to importing rich, but I like the formatting.
If you want a faster experience and don't want to use rich you can install
[typer-slim](https://typer.tiangolo.com/release-notes/#0121)
instead of typer.

One of the most important design choices when building an LLM CLI is deciding how to translate terminal input into user and system messages of the form,

```python
[
    {"role": "system", "content": <CLI user can provide this>},
    {"role": "user", "content": <CLI user can provide this>},
]
```

I wanted to be able to type input text on the terminal but I also wanted to support using [linux pipes](https://en.wikipedia.org/wiki/Pipeline_(Unix)). I ended up with the following design:

## user message with  `--prompt` or `-p`

```bash
lmc -p "tell me a story"
```

## system message with  `--system` or `-y`

```bash
lmc -p "tell me a story" -y "speak like a pirate"
```

I used `-y` for the short name here because I had already used the `--stream`, `-s` pair to indicate streaming output.

## user message with pipe

```bash
cat <filename> | lmc
```

If text is piped in and there is no `-p` option then the piped input will become the user message content.

## user message with pipe and `-p`

```bash
cat <filename> | lmc -p "Summarize the following text: @pipe"
```

In this case, the `@pipe` string will be replaced with the piped in text.
This could also be accomplished using [command substitution](https://www.gnu.org/software/bash/manual/html_node/Command-Substitution.html),

```bash
lmc -p "Summarize the following text: $(cat <filename>)"
```

## Pondering

I'll leave the `@pipe` substitution in for now to see how I like it, but maybe I should get rid of it?
I'm also considering getting rid of the `-p` option and just have the prompt be the first positional
argument. Would this make piping more natural?



# Quick Links

* [uv](https://github.com/astral-sh/uv) for package management
* [litellm](https://github.com/BerriAI/litellm) for LLM provider abstraction
* [rich](https://github.com/Textualize/rich) for rich terminal formatting
* [typer](https://github.com/fastapi/typer) for the CLI

