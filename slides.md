---
# Tufte-inspired theme, local in ./slidev-theme-tufte
theme: ./slidev-theme-tufte
title: "Pixi: better developer experience for scientific Python projects"
class: text-center
drawings:
  persist: false
mdc: true
duration: 25min
transition: null
speaker: foo
---

<div class="relative mx-auto w-48">
  <img src="/paxton.png" class="w-full" alt="Paxton" />
</div>
<img src="/slides-qr-code.png" class="absolute top-8 right-8 w-35" alt="Slides QR code" />

# Pixi: better developer experience for scientific Python projects

<div class="subtitle mt-8">Lucas Colley & Wolf Vollprecht <img src="/prefix-logo.svg" class="inline align-middle h-5 mx-1" alt="prefix.dev" /></div>

EuroSciPy 2026 — AGH University of Kraków, Poland

Tuesday July 21 — <a href="https://pixi.prefix.dev">pixi.prefix.dev</a>

---
speaker: Lucas
---

# Prelude: Tutorial Tomorrow!

- If you like what you hear in this talk, learn how to use it with Mike tomorrow at 9am!

<img src="/tutorial.png" class="w-130" alt="Tutorial Schedule" />

---
speaker: Lucas
---

# Agenda

1. Pixi basics (Wolf)
2. Pixi in the scientific Python world (Lucas)
3. Some recent updates and how you can get involved (Wolf)
4. Live demo on SciPy (Lucas)

---
speaker: Wolf
---

# Basics: what is Pixi?

<DocLink href="https://pixi.prefix.dev/latest/getting_started/" label="getting started" />

Cross-platform environment management and build tool for any language.
<div class="grid grid-cols-2 gap-8 mt-6">

<div class="relative">
  <div v-click="[1, 2]" class="absolute inset-0">
  One 'manifest' describes a whole project:
    
  - **dependencies**: use conda *and/or* PyPI packages
  - **environments**:
    - isolate different dev environments
    - match in CI and locally
  - **tasks**: define repeatable development tasks
  - a **cross-platform lock file**: `pixi.lock`
    - records _exact_ dependencies for reproducibility

  </div>

  <div v-click="2" class="absolute inset-0">
  
  It replaces the pile you keep in your README:
  
  - `apt`, `brew`, `pip`, `conda`
  - `virtualenv`, `venv`, `conda env`
  - Makefiles, shell scripts, CI YAML
  
  </div>
</div>

<CodeWindow v-click="1" title="pixi.toml">

```toml {lines: true}
[workspace]
channels = ["conda-forge"]
platforms = ["linux-64", "osx-arm64", "win-64"]

[dependencies]
python = ">=3.12"
numpy = "*"
pytest = ">=7.0"

[tasks]
test = "pytest"
```

</CodeWindow>
</div>

<div v-click="1" class="justify-end absolute bottom-16 right-12 flex">
  <img src="/conda-forge.png" class="h-40" alt="conda-forge" />
</div>

---
speaker: Wolf
---

# Basics: tasks

<DocLink href="https://pixi.prefix.dev/latest/workspace/advanced_tasks/" label="tasks" />

<div class="grid grid-cols-2 gap-8 mt-6">

<CodeWindow title="pixi.toml">

```toml {lines: true}
[tasks]
# a plain shell command
test = "pytest -q"

# tasks compose into a graph
build = "meson compile -C build"
start = { depends-on = ["build", "test"] }

# arguments with defaults
[tasks.fmt]
cmd = "ruff format {{ path }}"
args = [{ arg = "path", default = "." }]

# skip work when nothing changed
[tasks.docs]
cmd = "sphinx-build docs _site"
inputs = ["docs/**"]
outputs = ["_site/**"]
```

</CodeWindow>

<div>

<v-clicks>

- One name, the **same command** on every OS and machine
- `depends-on` builds a graph, pixi runs it in order
- Tasks take **arguments**, `cwd`, and per-task **environment activation**
- `inputs`/`outputs` add **caching**, unchanged work is skipped
- `pixi run <task>` replaces the Makefile, shell script, CI-YAML pile

</v-clicks>

</div>
</div>

---
speaker: Wolf
---

# Basics: CI made easy

<DocLink href="https://pixi.prefix.dev/latest/integration/ci/github_actions/" label="GitHub Actions" />

<div class="grid grid-cols-2 gap-6 mt-6 items-start">

<Terminal title="On your laptop">
  <TermLine>pixi run test</TermLine>
  <TermLine output>✓ environment up-to-date</TermLine>
  <TermLine output>========= 42 passed in 3.14s =========</TermLine>
</Terminal>

<CodeWindow title=".github/workflows/ci.yml">

```yaml {lines: true}
steps:
  - uses: actions/checkout@v4
  - uses: prefix-dev/setup-pixi@v0.10.0
  # the exact command you run locally:
  - run: pixi run test
```

</CodeWindow>

</div>

<v-clicks>

- **Local == CI**: same lockfile, same env, same command
- This makes your setup CI system agnostics
- Very easy caching setup so runs are really fast

</v-clicks>

---
speaker: Lucas
---

# You and your friends are joining the group!

<div class="mt-2">


</div>

- **[Python](https://github.com/python/cpython/tree/main/Tools/pixi-packages)** - `Tools/pixi-packages/` - managed builds: ASAN, freethreading, TSAN
- **[NumPy](https://github.com/numpy/numpy/tree/main/pixi-packages)** - `pixi-packages/` - transitive source builds: ASAN, freethreading, TSAN
- **[SciPy](https://github.com/scipy/scipy/blob/main/pixi.toml)** - `pixi.toml` - 30+ environments across platforms and users
- **[pandas](https://github.com/pandas-dev/pandas/blob/main/pixi.toml)** - `pixi.toml` - development environments and a big test matrix, incl. freethreading
- **[Xarray](https://github.com/pydata/xarray/blob/main/pixi.toml)** - `pixi.toml` - development environments and a big test matrix, incl. nightly builds
- **[cuda-python](https://github.com/NVIDIA/cuda-python/blob/main/pixi.toml)** - `pixi.toml` - monorepo orchestration: root tasks fan out to sub-package manifests, in CUDA 12 & 13 environments
- And many more projects in the scientific Python ecosystem: `napari`, `skrub`, `pydata/sparse`, `finch-tensor-lite`, `networkx`

<div class="absolute right-12 bottom-16 w-256 flex flex-wrap items-center justify-end gap-x-8 gap-y-6 opacity-90">
  <img src="/python.svg" class="h-10" alt="Python" />
  <img src="/numpy.svg" class="h-12" alt="NumPy" />
  <img src="/scipy.svg" class="h-12" alt="SciPy" />
  <img src="/pandas.svg" class="h-12" alt="pandas" />
  <img src="/xarray.svg" class="h-12" alt="Xarray" />
  <img src="/nvidia.svg" class="h-8" alt="NVIDIA" />
</div>

---
layout: center
class: text-center
speaker: Lucas
---

# So, why did they all adopt it?

<div class="flex justify-center mt-6">
<div class="text-left">

- build from source reproducibly across platforms
- simplify complex build environments with non-PyPI dependencies
- new contributors can follow the exact same workflow as lead maintainers easily

</div>
</div>

---
speaker: Lucas
---

# Working on SciPy without conda/Pixi...

<div class="relative h-100">
  <div v-click.hide class="absolute inset-0">
    <img src="/scipy-building.png" class="h-16 mx-auto" alt="SciPy docs menu" />
  </div>
  <div v-click="[1, 2]" class="absolute inset-0">
    <img src="/scipy-linux.png" class="w-full h-full object-contain" alt="SciPy docs menu" />
  </div>
  <div v-click="[2, 3]" class="absolute inset-0">
    <img src="/scipy-macos.png" class="w-full h-full object-contain" alt="SciPy docs menu" />
  </div>
  <div v-click="3" class="absolute inset-0">
    <img src="/scipy-win.png" class="w-full h-full object-contain" alt="SciPy docs menu" />
  </div>
</div>

---
speaker: Lucas
---

# Many environments, one manifest

<DocLink href="https://pixi.prefix.dev/latest/workspace/multi_environment/" label="environments" />

<div class="grid grid-cols-[1.2fr_1fr] gap-6 mt-2">

<CodeWindow title="scipy/pixi.toml" href="https://github.com/scipy/scipy/blob/main/pixi.toml">

```toml {lines: true}
# a feature turns behavior on...
[feature.py-freethreading.dependencies]
python-freethreading = "*"
[feature.py-freethreading.activation.env]
PYTHON_GIL = "0"

[feature.cuda12.dependencies]
cuda-version = "==12.9"

# environments compose features
# 30+ of them, e.g.:
[environments.cupy]
features = ["run-deps", "test-deps", "cuda13", "py-cuda"]
solve-group = "cuda13"

[environments.build-freethreading]
features = ["py-freethreading", "build-deps", "..."]
no-default-feature = true # avoid python-gil
solve-group = "freethreading"
```

</CodeWindow>

<div>

<v-clicks>

- **Features** are reusable slices of deps/tasks/activation
- **Environments** compose features
- SciPy ships **30+**: BLAS variants, GPU stacks, freethreading, debug
- Pick one at run time: <br>`pixi run -e cupy test`
- Each contributor gets the environment they need, reproducibly

</v-clicks>

</div>
</div>

---
speaker: Lucas
---

# Instrumented Builds of CPython

- If you want to better understand why CPython has a `pixi.toml`:
  - [Talk from the EuroPython Packaging Summit](https://lucascolley.github.io/talks/europython-26-instrumented/)
  - Depending on a from-source build of a project like CPython should not be a mystery
- [SciPy](https://github.com/scipy/scipy/pull/24066) and [PyArrow](https://github.com/apache/arrow/pull/49849) are both keen to use this in finding security vulnerabilities and bugs

---
speaker: Lucas
---

# Your turn

<DocLink href="https://pixi.prefix.dev/latest/reference/cli/pixi/init/" label="pixi init" />

<div class="grid grid-cols-[1.2fr_1fr] gap-8 mt-6">

<div>

<Terminal title="pick where you are">
  <TermLine output v-click="1"># 1. start fresh</TermLine>
  <TermLine v-click="1">pixi init my-analysis && cd my-analysis</TermLine>
  <TermLine v-click="1">pixi add python numpy matplotlib</TermLine>
  <TermLine output v-click="2"># 2. coming from conda / mamba</TermLine>
  <TermLine v-click="2">pixi init --import environment.yml</TermLine>
  <TermLine output v-click="3"># 3. already a Python package</TermLine>
  <TermLine v-click="3">pixi init --format pyproject</TermLine>
</Terminal>

</div>

<div>

<div v-click="1" class="mt-2">

**Start fresh**: manifest, environment and lockfile appear as you go

</div>

<div v-click="2" class="mt-4">

**Import**: channels and dependencies come along from `environment.yml`

</div>

<div v-click="3" class="mt-4">

**Extend**: pixi lives inside `pyproject.toml` under `[tool.pixi]`; your PyPI deps stay where they are

</div>

<div v-click="4" class="mt-6">

Whatever the mode: commit `pixi.toml` **and** `pixi.lock`, teammates just `pixi run`

</div>

</div>
</div>

---
speaker: Lucas
---

# Grow the environment into a package

<DocLink href="https://pixi.prefix.dev/latest/build/dev/" label="dev packages" />

<div class="grid grid-cols-[1.2fr_1fr] gap-6 mt-2">

<CodeWindow title="pixi.toml">

```toml {lines: true}
[workspace]
channels = ["https://prefix.dev/conda-forge"]
platforms = ["linux-64", "osx-arm64", "win-64"]
preview = ["pixi-build"]

# your project becomes a real conda package
[package.build.backend]
name = "pixi-build-python"
version = "*"

[package.run-dependencies]
numpy = "*"

# develop against it, without installing it
[dev]
my-project = { path = "." }

[dependencies]
pytest = "*"
```

</CodeWindow>

<div>

<v-clicks>

- Add a `[package]` section to specify how your project is packaged and used by others
- `[dev]` + a **path dependency**: the package's build/host/run deps land in your env, the package itself isn't built. Only need to specify deps in one place
- From here: `pixi publish`, or others depend on you straight **from git**

</v-clicks>

</div>
</div>

---
speaker: Wolf
---

# Build and Ship a package: `pixi publish`

<DocLink href="https://pixi.prefix.dev/latest/reference/cli/pixi/publish/" label="pixi publish" />

<div class="grid grid-cols-2 gap-6 mt-4 items-start">

<Terminal title="build & ship a package">
  <TermLine>pixi publish --target-channel https://prefix.dev/my-channel</TermLine>
  <TermLine output>✓ built, pushed, attested (OIDC)</TermLine>
</Terminal>

<div>

<v-clicks>

- **Publish** your package to a channel, SciPy already does this [in CI](https://github.com/scipy/scipy/blob/main/.github/workflows/pixi-packages.yml)
- **Trusted publishing**: OIDC, attestations, no long-lived tokens

</v-clicks>

</div>

</div>

---
speaker: Wolf
---

# Notable recent improvements

<DocLink href="https://pixi.prefix.dev/latest/reference/pixi_manifest/" label="manifest" />

<div class="grid grid-cols-3 gap-4 mt-6 items-start">

<div v-click="1">

**Conditional dependencies**

<CodeWindow title="pixi.toml">

```toml
[dependencies.unix-helper]
version = "*"
when = "__unix"

[dependencies.cupy]
version = "*"
when = "__cuda >= 13.0"

[dependencies.typing-ext]
version = "*"
when = "python < 3.12"
```

</CodeWindow>

</div>

<div v-click="2">

**Extras**

<CodeWindow title="pixi.toml">

```toml
[dependencies.my-pkg]
version = "*"
extras = ["plot"]

[pypi-dependencies.pandas]
version = ">=2"
extras = ["excel"]
```

</CodeWindow>

</div>

<div v-click="3">

**Workspace dependencies**

<CodeWindow title="pixi.toml">

```toml
[workspace.dependencies]
numpy = "1.*"

[workspace.dependencies.shared-lib]
path = "packages/shared-lib"

# members inherit the pin
[package.run-dependencies]
numpy = { workspace = true }
shared-lib = { workspace = true }
```

</CodeWindow>

</div>

</div>

---
speaker: Wolf
---

# Where you can help

<v-clicks>

- **Try Pixi** — share your experience and help us and others improve it.
- **[Contribute to conda-forge](https://conda-forge.org/docs/maintainer/adding_pkgs/)**: add the packages your field needs, so everyone can `pixi add` them
- **Join our [Discord](https://discord.gg/kKV8ZxyzY4)** and help us and others with your experience, or just hang out and chat.

</v-clicks>

---
speaker: Wolf
---

# A little about Prefix.dev

<div class="grid grid-cols-[1.4fr_1fr] gap-8 mt-4 items-center">

<div>

We build Pixi, rattler-build, and the `rattler` core, and keep them free.

- Public, private & on-premise **channels** for your team
- Security-first: OIDC, attestations, CVE mapping, reproducible builds
- Onboarding & support for **Pixi** and **Conda** users

</div>

<div class="text-center">
  <img src="/prefix-logo.svg" class="h-16 mx-auto" alt="prefix.dev" />
</div>

</div>

---
layout: center
class: text-center
speaker: Lucas
---

# Now time for a live demo on SciPy!

---
layout: center
class: text-center
---

# Thanks, EuroSciPy!

<img src="/slides-qr-code.png" class="absolute top-8 right-8 w-35" alt="Slides QR code" />

<img src="/paxton.png" class="h-50 mx-auto mt-4 mb-6" alt="Paxton" />

Thanks to the EuroSciPy 2026 organisers and volunteers!

<div class="mt-6">

Read the real files:
[python](https://github.com/python/cpython/tree/main/Tools/pixi-packages),
[numpy](https://github.com/numpy/numpy/tree/main/pixi-packages),
[scipy](https://github.com/scipy/scipy/blob/main/pixi.toml),
[pandas](https://github.com/pandas-dev/pandas/blob/main/pixi.toml),
[xarray](https://github.com/pydata/xarray/blob/main/pixi.toml),
[cuda-python](https://github.com/NVIDIA/cuda-python/blob/main/pixi.toml)

[pixi.prefix.dev](https://pixi.prefix.dev) - [prefix.dev](https://prefix.dev) - [github.com/prefix-dev](https://github.com/prefix-dev)

</div>

<br/>
