# Packaging and Publishing Guide

## Project Structure Preparation

1. Ensure correct project structure:
```
robyn_admin/
├── robyn_admin/
│   ├── __init__.py
│   ├── core/
│   ├── utils/
│   └── ...
├── setup.py
├── README.md
├── LICENSE
└── requirements.txt
```

2. Create setup.py file:
```python
from setuptools import setup, find_packages

with open("README.md", "r", encoding="utf-8") as fh:
    long_description = fh.read()

setup(
    name="robyn_admin",
    version="0.1.0",
    author="Your Name",
    author_email="your.email@example.com",
    description="A modern admin interface for Robyn framework",
    long_description=long_description,
    long_description_content_type="text/markdown",
    url="https://github.com/yourusername/robyn_admin",
    packages=find_packages(),
    classifiers=[
        "Programming Language :: Python :: 3",
        "License :: OSI Approved :: MIT License",
        "Operating System :: OS Independent",
    ],
    python_requires=">=3.7",
    install_requires=[
        "robyn>=0.1.0",
        "tortoise-orm>=0.19.0",
        # other dependencies...
    ],
    include_package_data=True,  # Include non-Python files
    package_data={
        'robyn_admin': [
            'static/*',
            'templates/*',
            'i18n/*'
        ],
    },
)
```

## Packaging Steps

1. Install packaging tools:
```bash
pip install build twine
```

2. Build distribution packages:
```bash
python -m build
```
This will create two files in the `dist/` directory:
- `robyn_admin-0.1.0.tar.gz` (source distribution)
- `robyn_admin-0.1.0-py3-none-any.whl` (built distribution)

## Test Installation

1. Create virtual environment for testing:
```bash
python -m venv test_env
source test_env/bin/activate  # Linux/Mac
# or
test_env\Scripts\activate  # Windows
```

2. Test local installation:
```bash
pip install dist/robyn_admin-0.1.0-py3-none-any.whl
```

## Publishing to PyPI

1. Register PyPI account:
- Visit https://pypi.org to register
- Create API token

2. Configure authentication:
```bash
# Create or edit ~/.pypirc
[pypi]
username = __token__
password = your-token
```

3. Upload to PyPI:
```bash
python -m twine upload dist/*
```

4. Upload to Test PyPI (optional):
```bash
python -m twine upload --repository-url https://test.pypi.org/legacy/ dist/*
```

## Version Updates

1. Update version number in setup.py

2. Rebuild and upload:
```bash
python -m build
python -m twine upload dist/*
```

## Important Notes

1. Version Management
   - Follow semantic versioning
   - Update version number before each release

2. File Inclusion
   - Ensure MANIFEST.in includes all necessary non-Python files
   - Check package_data configuration

3. Dependency Management
   - Specify required dependencies in install_requires
   - Use appropriate version ranges

4. Documentation
   - Include clear installation and usage instructions in README.md
   - Include necessary license information

5. Security
   - Don't include sensitive information in code
   - Use .gitignore to exclude unnecessary files 