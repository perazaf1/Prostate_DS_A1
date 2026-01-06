# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a data science project analyzing prostate cancer data. The dataset (`data/prostate.txt`) contains clinical information about patients who underwent radical prostatectomy. The goal is to understand factors influencing PSA (Prostate-Specific Antigen) levels using statistical analysis and machine learning techniques.

## Environment Setup

### Virtual Environment

The project uses a Python virtual environment located in `venv/`.

```bash
# Activate the virtual environment (Windows)
.\venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# List installed packages
pip list
```

### Environment Variables

The project uses a `.env` file to configure paths:
- `DATA_PATH`: Path to the prostate dataset
- `FIGURES_PATH`: Path where generated figures are saved

These variables are loaded using `python-dotenv` in each notebook.

## Project Structure

```
├── data/
│   └── prostate.txt          # Main dataset (80 observations, 6 variables)
├── notebooks/
│   ├── 01_exploration.ipynb  # Exploratory data analysis
│   ├── 02_pca.ipynb         # Principal Component Analysis
│   ├── 03_regression.ipynb  # Regression analysis
│   └── ghasper.ipynb        # Additional analysis notebook
├── figures/                  # Generated visualizations
├── src/                      # Source code (currently minimal)
└── .env                      # Environment configuration (not versioned)
```

## Dataset Variables

- `vol`: Cancer volume
- `wht`: Prostate weight
- `age`: Patient age
- `bh`: Benign hyperplasia quantity
- `pc`: Capsular penetration (higher = more invasive)
- `psa`: Prostate-Specific Antigen level (target variable)

## Analysis Workflow

### 1. Data Exploration (01_exploration.ipynb)

- Load dataset with pandas using whitespace separator: `pd.read_csv(path, sep=r"\s+")`
- No missing values in the dataset
- Generate descriptive statistics, histograms, boxplots, and KDE plots
- **Log transformation**: All variables except age are transformed using `np.log()` to create new variables prefixed with `l` (e.g., `lvol`, `lpsa`)
- Scatter plots show relationships between log-transformed variables and `lpsa`
- Key findings: `lvol` and `lpc` show strongest correlation with `lpsa`

### 2. PCA Analysis (02_pca.ipynb)

- Uses log-transformed variables: `lvol`, `lwht`, `lbh`, `lpc`, `lpsa`, `lage`
- Standardization with `StandardScaler` before PCA
- Generates correlation circle to visualize variable contributions to principal components
- Key concepts addressed:
  - Perfectly correlated variables should not both be included (causes singularity)
  - Uncorrelated variables are appropriate for PCA (capture independent information)

### 3. Regression Analysis (03_regression.ipynb)

Currently minimal implementation - intended for modeling PSA levels based on other variables.

## Common Patterns

### Figure Saving

All notebooks use a standardized figure-saving function:

```python
from pathlib import Path
import os
from dotenv import load_dotenv

load_dotenv(dotenv_path='../.env')
FIGURES_PATH = Path(os.getenv("FIGURES_PATH"))

def save_fig(name):
    plt.savefig(FIGURES_PATH / name, dpi=600, bbox_inches="tight")
```

Figures are saved at 600 DPI with tight bounding boxes.

### Data Loading

Standard pattern across notebooks:

```python
DATA_PATH = os.getenv("DATA_PATH")
df = pd.read_csv(DATA_PATH, sep=r"\s+")
```

The separator `r"\s+"` is critical - the data uses whitespace (spaces/tabs) as delimiter.

## Key Libraries

- **pandas**: Data manipulation
- **numpy**: Numerical operations and log transformations
- **matplotlib**: Plotting
- **seaborn**: Statistical visualizations
- **scikit-learn**: PCA, StandardScaler, regression models
- **python-dotenv**: Environment variable management

## Running Notebooks

Open and run notebooks in Jupyter:

```bash
# Ensure virtual environment is activated
.\venv\Scripts\activate

# Launch Jupyter (if installed)
jupyter notebook
```

Or use VS Code with the Jupyter extension to run notebooks directly.

## Important Notes

- All variables (except age) are transformed to log scale for analysis - this is intentional to linearize relationships and reduce skewness
- The dataset has exactly 80 observations with no missing values
- Figure generation closes plots after saving to avoid display in notebooks: `plt.close()`
- When modifying analysis, maintain the log-transformation convention: prefix variables with `l`
