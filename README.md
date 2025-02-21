# UV MEGA - Make Environments Great Again

PyConf Hyderabad. 22 Feb 2025.
Source: https://github.com/sanand0/uv-mega

- In 2024, I abandoned Python for JavaScript.
- Deno, WASM, and TypeScript are brilliant!
- `uv` reversed that.
- Why is `uv` such a game-changer for Python?
- Let's explore practical examples.

## uv combines python, pip, venv, pipx, pyenv

| Before                        | After                        |
| ----------------------------- | ---------------------------- |
| python hello.py               | uv run hello.py              |
| venv --python 3.12            | uv venv --python 3.12        |
| pip install pandas            | uv pip install pandas        |
| pipx run ruff format hello.py | uv tool ruff format hello.py |
| pyenv install 3.12            | uv python install 3.12       |

## It's backward and forward compatible

- `uv run ...` is IDENTICAL to `python ...`
- `uv pip ...` is IDENTICAL to `pip ...`
- `uv venv ...` is IDENTICAL to `venv ...`

There's nothing new to learn. Switch back any time.

## It's 100% standards compliant

- PEP 440 compatible for versions
- PEP 508 compatible for dependencies
- PEP 517/518 compatible for builds
- PEP 723 compatible for inline script dependencies
- ... etc.

## It is FAST!

- Written in **Rust**
- **FAST** dependency resolver algorithm
- 8-10× faster than pip on cold runs
- 80-115× faster when cached
- Incomparably faster than conda 🤣
- Completes in **milliseconds**, not seconds

```bash
# Watch the speed
uv run --with pandas,ipython ipython
```

## It takes less space

- **Re-use packages** across venvs (hard-links)
- **No extra copies** of pip or setuptools in each venv
- Creating 1000 venvs? No problem.

## No admin rights required

- It's a **single binary**
- Download from https://github.com/astral-sh/uv
- Add `uv` to your PATH and use it

## No conflicts with other Pythons

- `uv` is standalone and independent
- Runs alongside system Python
- Runs alongside Conda, Mamba, etc
- No impact on `uv` if other Python versions change

## It's popular

- **40,000 stars** on GitHub
- https://github.com/astral-sh/uv

## In short, there's no harm using it

- It is FAST!
- It takes less space
- No admin access required
- No conflicts with other Pythons
- It's 100% standards compliant
- It's backward and forward compatible

# Try new things without installation

- Explore libraries without installation
- Try different Pythons without installation
- Test library version changes without installation
- Run tools without installation

## Explore libraries without installation

Skip creating a venv with ipython, pandas, etc.

```bash
uv run --with pandas,requests,ipython ipython
```

Try trending libraries and extras features.
https://github.com/trending/python?since=monthly

```bash
uv run --with dlt[duckdb],ipython ipython
```

https://pypi.org/project/dlt

## Try different Pythons without installation

- Explore new Python features (or PyPy).
- Test your code with older/newer Python versions.

```bash
uv python list
uv run --python 3.7 python
```

- No new venv for each Python.
- No admin rights required.
- No system Python required at all.

## Test library version changes without installation

E.g. Pandas 1.x parses dates differently than 2.x.

```bash
uv run --python 3.9 --with 'pandas<2,numpy<2' python -c "
import pandas as pd;
print(pd.Series(['13-01-2000', '01-12-2000']))"
```

## Run tools without installation

`uv tool` or `uvx` runs CLI tools in isolated environments.

```bash
uvx yt-dlp https://www.youtube.com/watch?v=v3W2cjTWY-Y
uvx black --check .
uvx ruff format .
uvx pytest
uvx llm "What's today's date?"
uvx files-to-prompt *.py | uvx llm --system "Write a README.md"
uvx jupyter lab
uvx markitdown *.pdf
uvx --with datasette-cluster-map datasette *.db
uvx --from httpie http httpbin.org
```

Explore many others: `awscli`, `cog`, `copier`, `mycli`, `mypy`, `pgcli`, etc.

# Write better code

- Write single-file scripts without projects
- Run scripts from a URL
- Fast pre-commit hooks
- Write and run LLM-generated scripts

## Write single-file scripts without projects

PEP 723 defines inline script dependencies. Run this to add dependencies to a script:

```bash
uv add markdown2 rich --script script.py
```

```python
# /// script
# requires-python = ">=3.13"
# dependencies = [
#     "markdown2",
#     "rich",
# ]
# ///
```

- `uv run script.py` creates a venv, installs dependencies, runs script. Like `npx`.
- `#!uv run` shebang converts it into an executable.

## Run scripts from a URL

`uv run [url]` works.

I use this in student exercises. It's easy to share an executable as a URL.

```bash
uv run https://gist.githubusercontent.com/sanand0/f19b6797f82b36da39ac44f3a7d4392a/raw/13246698088795e1942179856aafd466052b66ae/datagen.py email@example.com
```

More examples at https://gist.github.com/simonw

## Fast pre-commit hooks

Add this to your `.git/hooks/pre-commit` to format with ruff before committing.

```bash
#!/bin/sh
uvx ruff format .
if ! git diff --quiet; then
    echo "Error: Files were formatted. Review and commit changes."
    exit 1
fi
```

- No global installation required
- No separate venv for each repo
- Fast hook execution faster (since uv reuses caches)

## Write and run LLM-generated scripts

Generate scripts using `llm` and run them immediately.

```bash
uvx llm "Convert all SQLite tables in glob(*.db) into .csv files using Pandas.
Print each file name as it's converted.
Output ONLY Python code without code fences" | uv run --with pandas python
```

Or save from `llm` to a single-file script, `uv add` dependencies, and `uv run`.

# Stabilize dependencies and deployments

- Run tools with outdated dependencies
- Test isolated environments
- Install offline
- Lock and sync environments
- Use in CI/CD pipelines
- Use in Docker builds
- ... and much, much more

## Run tools with outdated dependencies

If you have code that worked pre-2020 but libraries have changed, use:

```bash
uv run --exclude-newer 2020-01-01 script.py
```

Or, provide specific versions as dependencies:

```bash
uv run --python 3.9 --with 'pandas<2,numpy<2' script.py
```

No need to preserve / re-create the old environment.

## Test isolated environments

To report errors in isolated environments
https://simonwillison.net/2025/Jan/31/black-newline-docstring/

```bash
echo 'class ModelError(Exception):
    "Models can raise this error, which will be displayed to the user"
    pass' | uvx black@24.10.0 -

echo 'class ModelError(Exception):
    "Models can raise this error, which will be displayed to the user"
    pass' | uvx black@25.1.0 -
```

For clean environments, e.g. measuring cold start times, use `--isolated`:

```bash
uv run --no-cache --isolated --with pandas==2.2.3 python -c "
import timeit; print(timeit.timeit('import pandas as pd', number=1))"
```

## Install offline

If you're behind a corporate firewall, on an air-gapped machine, or a flight,
use `--offline` to use cached dependencies you've already downloaded.

```bash
uv run --offline script.py
```

## Lock and sync environments

`uv.lock` is like a `package-lock.json` or like a `requirements.txt`
-- but **cross-platform** and **reproducible**.

- `uv lock` creates a `uv.lock`.
- `uv sync` syncs your venv to match `uv.lock`.

Use it to create a **reproducible** environment.

## Use in CI/CD pipelines

Run linters, test cases, etc. in ephemeral environments.

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
uvx ruff check .
uv sync
uv run --python 3.12 --with '.[test]' pytest
uv run --python 3.13 --with '.[test]' pytest
```

Run in GitHub Actions.

```yaml
steps:
  - uses: actions/checkout@v4
  - name: Install uv
    uses: astral-sh/setup-uv@v5
  - name: Install the project
    run: uv sync --all-extras --dev
  - name: Run linters
    run: uvx ruff check .
  - name: Run tests
    run: uv run --python 3.12 --with '.[test]' pytest
```

## Use in Docker builds

- Installing from a pre-built Python Docker image limits flexibility.
- Installing Python in your own Docker image is slow.
- `uv` is so fast that it's more efficient to install `uv` than Python.

```Dockerfile
RUN curl -LsSf https://astral.sh/uv/install.sh | sh
RUN uv pip install --upgrade flask pydantic beanie
RUN uv pip install -r requirements.txt

ENV UV_LINK_MODE=copy
RUN --mount=type=cache,target=/root/.cache/uv uv sync
```

- "uv is **so fast**, subsequent installs of common libraries will be instant if you happen to have the same version"
- Strategy: Install `uv sync` in one stage. Copy .venv into a slim runtime image without pip/uv at runtime

- https://mkennedy.codes/posts/python-docker-images-using-uv-s-new-python-features/
- https://docs.astral.sh/uv/guides/integration/docker/

## ... and much, much more

- Use with GitLab https://docs.astral.sh/uv/guides/integration/gitlab/
- Use with AWS Lambda https://docs.astral.sh/uv/guides/integration/aws-lambda/
- Use your own indices https://docs.astral.sh/uv/guides/integration/alternative-indexes/
- Pick the right PyTorch https://docs.astral.sh/uv/guides/integration/pytorch/
- Use with Jupyter https://docs.astral.sh/uv/guides/integration/jupyter/

Explore these commands too:

- `uv add` is like `npm install`. It lets you manage your `project.toml`
- `uv build` lets you create a `.whl` for distribution
- `uv init` seeds a `.venv` and a `project.toml`. Has several options to explore
- `uv tool install -e .` installs local tools as executsbles
- `uv workspace` manages multiple projects in a single repo

# What you should do now

- Use `uv` instead of Python for development, and in pipelines / Dockerfiles.
- Use `uv` instead of pip, venv, or pipx.
- Use `uvx` to directly run CLI tools.
- Use inline dependencies to create single-file scripts.
- Use `uv.lock` instead of `requirements.txt`.

`uv` has many more features. Explore at https://docs.astral.sh/uv/

Source: https://github.com/sanand0/uv-mega
