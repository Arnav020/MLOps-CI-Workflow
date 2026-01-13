# MLOps-CI-Workflow

A comprehensive guide to understanding and implementing Continuous Integration using GitHub Actions.

---

## Table of Contents

1. [What is Continuous Integration?](#what-is-continuous-integration)
2. [Why Use CI?](#why-use-ci)
3. [How CI Works](#how-ci-works)
4. [Project Structure](#project-structure)
5. [Understanding the Code](#understanding-the-code)
6. [GitHub Actions Workflow](#github-actions-workflow)
7. [Testing Framework](#testing-framework)
8. [Benefits of CI](#benefits-of-ci)
9. [Step-by-Step Setup Guide](#step-by-step-setup-guide)

---

## What is Continuous Integration?

**Continuous Integration (CI)** is a software development practice where developers regularly merge their code changes into a shared repository, usually multiple times a day. Each merge triggers an automated process to build and test the code, ensuring that new changes integrate smoothly with the existing codebase.

### Key Components:

- **Version Control System (VCS)**: Git/GitHub for managing code
- **CI Server**: Automated server (GitHub Actions, Jenkins, etc.) that monitors the repository
- **Automated Testing**: Tests run automatically to verify code works correctly

---

## Why Use CI?

CI solves several critical problems in software development:

### Problems CI Addresses:

1. **Coding Standards (Linting)** - Ensures consistent code style
2. **Integration Issues** - Catches conflicts early when multiple developers work on the same codebase
3. **Interpreter Issues** - Detects compatibility problems across different environments
4. **Add/Remove Features** - Validates that new features don't break existing functionality

### Benefits:

- **Early Detection of Errors** - Find bugs immediately rather than weeks later
- **Automation of Testing** - No manual intervention needed
- **Faster Development Cycle** - Quick feedback on code quality
- **Reduced Integration Problems** - Continuous merging prevents large, difficult merges

---

## How CI Works

### The CI Pipeline:

```
Developer → Commits Code → Repository (GitHub)
                              ↓
                         CI Server Detects Change
                              ↓
                    Automated Server (GitHub Actions)
                              ↓
              Pull Code → OS Setup → Install Dependencies
                              ↓
                        Performs Tests
                              ↓
                         Report Results
```

### Workflow Triggers:

The CI server should run tests whenever you:
- Make changes to the codebase
- Push to specific branches (e.g., `main`)
- Create pull requests

The server has the same OS and Python dependencies as your app, ensuring consistency across environments.

---

## Project Structure

```
ci-tutorial/
│
├── .github/
│   └── workflows/
│       └── ci.yaml           # GitHub Actions workflow configuration
│
├── app.py                    # Main Streamlit application
├── _test.py                  # Test suite using pytest
└── README.md                 # This file
```

---

## Understanding the Code

### 1. Main Application (`app.py`)

A simple Streamlit web application that calculates powers of numbers.

```python
import streamlit as st

# Streamlit UI
st.title("Power Calculator")
st.write("Enter a number to calculate its square, cube and fifth power.")

# Get User input
n = st.number_input("Enter an integer", value=1, step=1)

# Calculate results
square = n**2
cube = n**3
fifth_power = n**5

# Display Results
st.write(f"The sqaure of {n} is: {square}")
st.write(f"The cube of {n} is: {cube}")
st.write(f"The fifth_power of {n} is: {fifth_power}")
```

**Purpose**: Demonstrates a simple application that needs testing to ensure calculations work correctly.

---

### 2. Test File (`_test.py`)

Uses `pytest` framework to validate application logic.

```python
import pytest

# Function to test to square
def square(n):
    return n**2

# Function to test cube
def cube(n):
    return n**3

# Function to test fifth power
def fifth_power(n):
    return n**5

# Testing the square function
def test_square():
    assert square(2) == 4, "Test Failed: Sqaure of 2 should be 4"
    assert square(3) == 9, "Test Failed: Sqaure of 3 should be 9"

# Testing the cube function
def test_cube():
    assert cube(2) == 8, "Test Failed: Cube of 2 should be 8"
    assert cube(3) == 27, "Test Failed: Cube of 3 should be 27"

# Testing the fifth power function
def test_fifth_power():
    assert fifth_power(2) == 32, "Test Failed: Fifth Power of 2 should be 32"
    assert fifth_power(3) == 243, "Test Failed: Fifth Power of 3 should be 243"

# Test for invalid input
def test_inavlid_input():
    with pytest.raises(TypeError):
        square("string")
```

**Note**: The `pytest` command runs tests from the `_test.py` file by looking for functions starting with `test_`.

---

### 3. CI Workflow Configuration (`ci.yaml`)

```yaml
name: CI Workflow

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout code
      uses: actions/checkout@v2

    - name: Set up Python
      uses: actions/setup-python@v2
      with:
        python-version: '3.12.3'

    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install pytest streamlit

    - name: Run tests
      run: |
        pytest _test.py
```

---

## GitHub Actions Workflow

### Configuration Breakdown:

#### 1. **Workflow Name**
```yaml
name: CI Workflow
```
Any name that describes your workflow.

#### 2. **Triggers (`on`)**
```yaml
on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main
```
- **`push:`** Triggers when code is pushed to the `main` branch
- **`pull_request:`** Triggers when a PR is created targeting the `main` branch

#### 3. **Jobs**
```yaml
jobs:
  test:
    runs-on: ubuntu-latest
```
- **`test`**: Job name (any valid name)
- **`runs-on: ubuntu-latest`**: Specifies the OS environment (defines what fields to do)

#### 4. **Steps**

Each step performs a specific action:

##### **Step 1: Checkout Code**
```yaml
- name: Checkout code
  uses: actions/checkout@v2
```
- Retrieves the latest code from the repository
- Essential for accessing your code in the CI environment

##### **Step 2: Set up Python**
```yaml
- name: Set up Python
  uses: actions/setup-python@v2
  with:
    python-version: '3.12.3'
```
- Installs the specified Python version
- Ensures consistency with your development environment

##### **Step 3: Install Dependencies**
```yaml
- name: Install dependencies
  run: |
    python -m pip install --upgrade pip
    pip install pytest streamlit
```
- Upgrades pip to the latest version
- Installs required packages (pytest for testing, streamlit for the app)

##### **Step 4: Run Tests**
```yaml
- name: Run tests
  run: |
    pytest _test.py
```
- Executes the test suite
- If tests fail, the workflow fails (provides feedback)

---

## Testing Framework

### Types of Tests Implemented:

#### 1. **Unit Tests**
Check individual components (functions) to ensure they work as expected.

```python
def test_square():
    assert square(2) == 4
```

#### 2. **Integration Tests**
Verify that different parts work together correctly (testing multiple functions).

#### 3. **End-to-End Tests**
Simulate real user scenarios to ensure the system behaves correctly.

#### 4. **Invalid Input Tests**
```python
def test_inavlid_input():
    with pytest.raises(TypeError):
        square("string")
```
Ensures the application handles invalid input gracefully.

---

## Benefits of CI

### 1. **Improved Code Quality**
- Automated tests catch bugs early
- Consistent coding standards

### 2. **Faster Delivery**
- Quick feedback on changes
- Automated deployment ready

### 3. **Collaboration**
- Multiple developers can work simultaneously
- Reduced integration conflicts

### 4. **Reduced Risk**
- Smaller, incremental changes
- Easy to identify what broke

---

## Step-by-Step Setup Guide

### Prerequisites:
- GitHub account
- Git installed locally
- Python 3.12+ installed

### Steps:

#### 1. **Create a Simple Streamlit App**
Create `app.py` with your application code.

#### 2. **Create a Folder `.github` in Root**
```bash
mkdir -p .github/workflows
```

#### 3. **Inside `.github/workflows`, Create `ci.yaml`**
Add your workflow configuration (see above).

#### 4. **Write Script (`ci.yaml`)**

Configure:
- **Name**: CI Workflow (any name)
- **On**: Triggers (push, pull_request)
  - **Push branches**: main (listens to PRs when pushed or pulled)
  - **Pull-request branches**: main
- **Jobs**: 
  - Define `test` job
  - Specify `runs-on: ubuntu-latest`
  - **Steps** (vary only, rest with diff steps):
    1. Checkout code (from repo)
    2. Setup environment (install dependencies)
    3. Run tests (already defines tests are correct)
    4. Build artifacts (for deployment, if tests pass)
    5. Deploy

#### 5. **Testing Framework Setup**

Using `pytest`:
- **Looks for**: `test_` in file names or function names
- **Executes them**: Runs all test functions

#### 6. **Build Automation**

The code is built with nodeps, compiling the code & generating artifacts.

#### 7. **Automated Testing**

Types:
- Unit tests - Check individual components on "f" to ensure they work as expected
- Integration tests - These tests verify that different parts work together
- End-to-End tests - Simulate real user scenarios to ensure system as a whole behaves correctly

#### 8. **Feedback**

In the form of success or failure notifications.

---

## Running Tests Locally

Before pushing to GitHub, test locally:

```bash
# Install dependencies
pip install pytest streamlit

# Run tests
pytest _test.py
```

---

## Viewing CI Results

1. Go to your GitHub repository
2. Click on the **"Actions"** tab
3. View workflow runs and their status
4. Click on individual runs to see detailed logs

---

## Key Takeaways

- **CI automates the testing process** every time you push code
- **GitHub Actions** provides a free CI/CD platform integrated with GitHub
- **Early bug detection** saves time and improves code quality
- **Automated workflows** ensure consistency across development environments
- **Testing is crucial** for maintaining code reliability

---

## Additional Resources

- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Pytest Documentation](https://docs.pytest.org/)
- [Streamlit Documentation](https://docs.streamlit.io/)

---

## Notes

- The CI server (GitHub Actions) works on `.yaml` files (or `.yml`)
- Configuration files are located in `.github/workflows/`
- Steps in CI pipeline only test with different steps
- Others like Jenkins, Travis CI, and GitLab CI/CD follow similar principles

---

**Happy Learning! 🚀**