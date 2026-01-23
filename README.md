<div align="center">
  <img src="logo.png" alt="vaulthaus-hackathon-2024" width="512"/>

  [![Python](https://img.shields.io/badge/Python-3.9-blue.svg)](https://www.python.org/)
  [![Claude AI](https://img.shields.io/badge/Claude-3.5%20Sonnet-orange.svg)](https://www.anthropic.com/)
  [![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
  [![Synthea](https://img.shields.io/badge/Data-Synthea-purple.svg)](https://synthea.mitre.org/)

  **🏥 AI-powered healthcare data quality pipeline that detects, damages, and heals data issues using Claude 🩺**

  [VaultHaus](https://www.vaulthaus.health/) · [Synthea](https://synthea.mitre.org/)
</div>

## Overview

A 4-stage data quality pipeline built for the [VaultHaus](https://www.vaulthaus.health/) Hackathon 2024. It uses Claude AI to:

1. **Build schemas** from Synthea synthetic healthcare data with inferred patterns
2. **Intentionally damage** data with realistic quality issues (format variations, typos, regional differences)
3. **Heal/restore** data using Claude's structured tool-use capabilities
4. **Evaluate** data quality metrics (regex accuracy, key uniqueness, foreign key consistency)

## Features

- **Intelligent Schema Inference**: Automatically detects column types, calculates statistics, and generates regex patterns using Claude
- **Realistic Data Degradation**: Simulates real-world data quality issues including case changes, format variations, and typos
- **AI-Powered Data Healing**: Restores damaged values while preserving semantic meaning with confidence scores
- **Comprehensive Quality Metrics**: Evaluates regex accuracy, primary key uniqueness, and foreign key consistency

## Quick Start

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

## Installation

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

## Usage

### Full Pipeline

```bash
./run.sh
```

This runs all 4 stages sequentially: schema build → damage → heal → evaluate.

### Individual Stages

```bash
# Stage 1: Build schema from original data
python schema_build.py

# Stage 2: Create damaged dataset
python data_damage.py

# Stage 3: Heal damaged dataset
python data_heal.py

# Stage 4: Evaluate data quality
python data_eval.py              # Evaluate original
python data_eval.py damaged      # Evaluate damaged
python data_eval.py healed       # Evaluate healed
```

## Architecture

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
- **Healing Cap**: Currently limited to 10 values per column (pagination not yet implemented)
- **Empty Values**: Preserved as empty strings (`na_filter=False`)

## Data Flow

The pipeline processes Synthea synthetic healthcare data through these transformations:

1. **Schema Building**: Scans CSV files, infers types (integer, float, string), calculates value distributions, and uses Claude to generate regex patterns and documentation

2. **Data Damaging**: Introduces realistic quality issues:
   - Format variations (dates, phone numbers)
   - Case changes (UPPERCASE, lowercase, MixedCase)
   - Typos and character substitutions
   - Regional format differences

3. **Data Healing**: Analyzes damaged values against regex patterns and restores them while:
   - Preserving semantic meaning
   - Generating confidence scores (0-1)
   - Maintaining data integrity

4. **Quality Evaluation**: Measures:
   - Regex pattern compliance
   - Primary key uniqueness
   - Foreign key referential integrity

## Project Structure

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
│   ├── Synthea27Nj_5.4/         # Original data
│   ├── Synthea27Nj_5.4_damaged/ # Damaged data
│   └── Synthea27Nj_5.4_healed/  # Healed data
└── schema/              # Schema directory (gitignored)
    ├── tables/          # Per-table schemas
    ├── schema.json      # Master schema
    └── schema_quality.json  # Quality metrics
```

## Requirements

| Package | Version | Purpose |
|---------|---------|---------|
| Python | 3.9+ | Runtime |
| anthropic | latest | Claude API client |
| pandas | latest | Data manipulation |
| python-dotenv | latest | Environment variables |

## Contributing

Contributions are welcome! This project was created for the VaultHaus Hackathon 2024.

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request

## License

This project is licensed under the MIT License.

## Acknowledgments

- [VaultHaus](https://www.vaulthaus.health/) for hosting the hackathon
- [Synthea](https://synthea.mitre.org/) for synthetic healthcare data
- [Anthropic](https://www.anthropic.com/) for Claude AI
