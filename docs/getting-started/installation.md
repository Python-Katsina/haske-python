# Installation

Haske targets Python 3.8 and newer. Installing the optional Rust toolchain unlocks accelerated routing, JSON parsing, template rendering, ORM helpers, and compression routines, but the framework gracefully falls back to pure Python implementations if the native extension is unavailable.

## 1. Prepare your environment

1. Install Python 3.8+ and ensure `pip` is available on your PATH.
2. (Recommended) Install the Rust toolchain via [rustup](https://rustup.rs) so the `_haske_core` extension can be built from source.
3. Create and activate a virtual environment to isolate dependencies:
   ```bash
   python -m venv .venv
   source .venv/bin/activate  # Windows: .venv\\Scripts\\activate
   ```

## 2. Install from PyPI

Install the latest published release with pip:

```bash
pip install haske
```

This command downloads the Python package and, when Rust is available, compiles the accompanying native extension for maximum performance.

## 3. Install from source

To work with the bleeding-edge code or contribute to Haske itself, clone the repository and run the setup script:

```bash
git clone https://github.com/Python-Katsina/haske-python.git
cd haske-python
python setup.py
```

Running `setup.py` builds the Rust components and installs the package into your active environment.

## 4. Verify the installation

After installation, you can confirm everything is wired correctly by importing the framework and printing its version:

```python
from haske import Haske, __version__
print("Haske", __version__)
```

If you see the version number, you are ready to create your first Haske app.
