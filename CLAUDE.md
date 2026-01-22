# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Healthcare data quality pipeline for the VaultHaus Hackathon 2024. Uses Synthea synthetic healthcare data to:
1. Build data quality schemas with inferred patterns
2. Intentionally damage data with realistic quality issues
3. Heal/restore data using Claude AI
4. Evaluate data quality metrics

## Commands

```bash
# Setup
conda env create -f environment.yml
cp .env.example .env  # Add ANTHROPIC_API_KEY

# Run full pipeline
./run.sh

# Run individual stages
python schema_build.py          # Build schema from datasets/Synthea27Nj_5.4/
python data_damage.py           # Create damaged dataset
python data_heal.py             # Heal damaged dataset
python data_eval.py             # Evaluate normal dataset
python data_eval.py damaged     # Evaluate damaged dataset
python data_eval.py healed      # Evaluate healed dataset
```

## Architecture

**4-Stage Pipeline:**

1. **schema_build.py** - Scans CSV files, infers column types, calculates statistics (value distributions, uniqueness, ranges), uses Claude to generate regex patterns and documentation. Outputs `schema/tables/*.json` and `schema/schema.json`.

2. **data_damage.py** - Applies realistic data quality degradations (format variations, case changes, typos, regional differences) using Claude. Outputs to `datasets/Synthea27Nj_5.4_damaged/`.

3. **data_heal.py** - Restores damaged values using Claude's `heal_values` tool to match expected regex patterns while preserving semantic meaning. Generates confidence scores. Outputs to `datasets/Synthea27Nj_5.4_healed/`.

4. **data_eval.py** - Calculates quality metrics: regex accuracy, primary key uniqueness, foreign key consistency. Outputs `schema/schema_quality.json`.

**Key Technical Details:**
- Uses Claude 3.5 Sonnet with tool-use for structured outputs
- Implements prompt caching (`cache_control`) for cost optimization
- Data healing capped at 10 values per column (pagination not yet implemented)
- Empty strings preserved as missing values (`na_filter=False`)

## Data Flow

```
datasets/Synthea27Nj_5.4/ (original)
    → schema_build.py → schema/schema.json
    → data_damage.py  → datasets/Synthea27Nj_5.4_damaged/
    → data_heal.py    → datasets/Synthea27Nj_5.4_healed/
    → data_eval.py    → schema/schema_quality.json
```

## Important Notes

- README.md must be kept up to date with any significant project changes
- The `datasets/` and `schema/` directories are gitignored
- Requires ANTHROPIC_API_KEY in .env file
