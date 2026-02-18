> [!WARNING]
> ## Archived
> This project is archived and no longer actively maintained.
>
> The VaultHaus Hackathon 2024 has concluded. The code is preserved here as a reference implementation, but no further updates, bug fixes, or feature development are planned.

<div align="center">
  <img src="logo.png" alt="vaulthaus-hackathon-2024" width="512"/>

  [![Python](https://img.shields.io/badge/Python-3.9%2B-blue.svg)](https://www.python.org/)
  [![Claude AI](https://img.shields.io/badge/Claude-3.5%20Sonnet-orange.svg)](https://www.anthropic.com/)
  [![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
  [![Synthea](https://img.shields.io/badge/Data-Synthea-purple.svg)](https://synthea.mitre.org/)

  **🏥 AI-powered healthcare data quality pipeline that detects, damages, and heals data issues using Claude 🩺**

  [VaultHaus](https://www.vaulthaus.health/) · [Synthea](https://synthea.mitre.org/) · [Anthropic](https://www.anthropic.com/)
</div>

## Overview

**The Pain:** Healthcare data is notoriously messy — format inconsistencies, typos, regional variations, and encoding issues silently corrupt analytics and downstream systems.

**The Solution:** A 4-stage AI pipeline that uses Claude to *understand* your data's expected shape, introduce realistic damage, then intelligently restore it — giving you a reproducible benchmark for data quality tooling.

**The Result:** Measurable quality scores across regex compliance, primary key uniqueness, and foreign key consistency — before and after healing.

Built for the [VaultHaus Hackathon 2024](https://www.vaulthaus.health/) using [Synthea](https://synthea.mitre.org/) synthetic healthcare data.

## ✨ Features

- **🔍 Intelligent Schema Inference** — Scans CSV files, detects column types, computes value distributions, and uses Claude to generate regex patterns with documentation
- **💥 Realistic Data Degradation** — Simulates real-world quality issues: date format variations, case changes, typos, character substitutions, and regional differences
- **🩹 AI-Powered Data Healing** — Restores damaged values to match expected patterns while preserving semantic meaning and generating per-value confidence scores
- **📊 Comprehensive Quality Metrics** — Evaluates regex accuracy, primary key uniqueness, and foreign key referential integrity across all pipeline stages

## 🚀 Quick Start

```bash
# Clone and setup
git clone https://github.com/tsilva/vaulthaus-hackathon-2024.git
cd vaulthaus-hackathon-2024
conda env create -f environment.yml
conda activate uprise-hackathon-2024

# Configure API key
cp .env.example .env
# Edit .env and add your ANTHROPIC_API_KEY

# Run the full pipeline
./run.sh
```

## 🔧 Installation

### Prerequisites

- Python 3.9+
- Conda (recommended) or pip
- Anthropic API key

### Setup

```bash
# Create conda environment
conda env create -f environment.yml
conda activate uprise-hackathon-2024

# Or install with pip
pip install anthropic pandas python-dotenv

# Configure environment
cp .env.example .env
# Add your ANTHROPIC_API_KEY to .env
```

## 📖 Usage

### Full Pipeline

```bash
./run.sh
```

Runs all 4 stages sequentially: schema build → damage → heal → evaluate.

### Individual Stages

```bash
# Stage 1: Build schema from original data
python schema_build.py

# Stage 2: Create damaged dataset
python data_damage.py

# Stage 3: Heal damaged dataset
python data_heal.py

# Stage 4: Evaluate data quality
python data_eval.py           # Evaluate original
python data_eval.py damaged   # Evaluate damaged
python data_eval.py healed    # Evaluate healed
```

## 🏗️ Architecture

```
datasets/Synthea27Nj_5.4/ (original)
    │
    ├── schema_build.py ──→ schema/schema.json
    │
    ├── data_damage.py  ──→ datasets/Synthea27Nj_5.4_damaged/
    │
    ├── data_heal.py    ──→ datasets/Synthea27Nj_5.4_healed/
    │
    └── data_eval.py    ──→ schema/schema_quality.json
```

### Pipeline Stages

| Stage | Script | Input | Output | Description |
|-------|--------|-------|--------|-------------|
| 1 | `schema_build.py` | Original CSVs | `schema/schema.json` | Infers column types, calculates statistics, generates regex patterns |
| 2 | `data_damage.py` | Original CSVs + Schema | Damaged CSVs | Applies realistic data quality degradations |
| 3 | `data_heal.py` | Damaged CSVs + Schema | Healed CSVs | Restores values using Claude's `heal_values` tool |
| 4 | `data_eval.py` | Any dataset + Schema | Quality metrics | Calculates regex accuracy, key uniqueness, FK consistency |

### Technical Details

- **Model**: Claude 3.5 Sonnet with tool-use for structured outputs
- **Prompt Caching**: Uses `cache_control` for cost optimization
- **Healing Cap**: Currently limited to 10 values per column
- **Empty Values**: Preserved as empty strings (`na_filter=False`)

## 📁 Project Structure

```
vaulthaus-hackathon-2024/
├── schema_build.py      # Stage 1: Schema inference
├── data_damage.py       # Stage 2: Data degradation
├── data_heal.py         # Stage 3: AI-powered healing
├── data_eval.py         # Stage 4: Quality evaluation
├── run.sh               # Full pipeline runner
├── environment.yml      # Conda dependencies
├── .env.example         # API key template
├── datasets/            # Data directory (gitignored)
│   ├── Synthea27Nj_5.4/          # Original data
│   ├── Synthea27Nj_5.4_damaged/  # Damaged data
│   └── Synthea27Nj_5.4_healed/   # Healed data
└── schema/              # Schema directory (gitignored)
    ├── tables/              # Per-table schemas
    ├── schema.json          # Master schema
    └── schema_quality.json  # Quality metrics
```

## 📦 Requirements

| Package | Version | Purpose |
|---------|---------|---------|
| Python | 3.9+ | Runtime |
| anthropic | latest | Claude API client |
| pandas | latest | Data manipulation |
| python-dotenv | latest | Environment variables |

## 🙏 Acknowledgments

- [VaultHaus](https://www.vaulthaus.health/) for hosting the hackathon
- [Synthea](https://synthea.mitre.org/) for synthetic healthcare data
- [Anthropic](https://www.anthropic.com/) for Claude AI
