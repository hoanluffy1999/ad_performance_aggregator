# Ad Performance Aggregator

A high-performance Rust CLI application that processes large CSV files (~1GB) to aggregate ad campaign performance data and generate top performer reports.

## Installation Guide

### Prerequisites

- [Rust](https://rustup.rs/) (latest stable version)
- Cargo (comes with Rust installation)

### Setup Steps

1. **Clone or navigate to the project directory:**
   ```bash
   cd ad_performance_aggregator
   ```

2. **Build the project (Release mode for optimal performance):**
   ```bash
   cargo build --release
   ```

3. **The executable will be located at:**
   - Windows: `target\release\ad_performance_aggregator.exe`
   - Linux/macOS: `target/release/ad_performance_aggregator`

## How to Run

### Basic Usage

```bash
# Windows (PowerShell/CMD)
.\target\release\ad_performance_aggregator.exe --input <path_to_csv> --output-dir <output_directory>

# Linux/macOS
./target/release/ad_performance_aggregator --input <path_to_csv> --output-dir <output_directory>
```

### Command-Line Arguments

| Argument | Short | Required | Default | Description |
|----------|-------|----------|---------|-------------|
| `--input` | `-i` | Yes | - | Input CSV file path (e.g., `ad_data.csv`) |
| `--output-dir` | `-o` | No | `.` | Output directory for generated reports |

### Examples

```bash
# NOTE: the sample `ad_data.csv` in this repository is currently provided as a
# ZIP archive. You will need to unzip it before passing the path to the
# executable. For example, on Windows:
#
# ```powershell
# Expand-Archive ad_data.csv.zip -DestinationPath .
# ```
#
# After extraction the following commands will work:
#
# Process ad_data.csv and output to current directory
ad_performance_aggregator -i ad_data.csv

# Specify custom output directory
ad_performance_aggregator --input ad_data.csv --output-dir ./reports

# Using the sample data in this repository (after unzipping)
ad_performance_aggregator -i C:\Users\hoan\Downloads\ad_data.csv\ad_data.csv -o C:\Users\hoan\Downloads\ad_data.csv
```

### Output Files

The application generates two CSV files:

1. **`top10_ctr.csv`** - Top 10 campaigns by highest Click-Through Rate (CTR)
2. **`top10_cpa.csv`** - Top 10 campaigns by lowest Cost Per Acquisition (CPA)

## Libraries Used

| Crate | Version | Purpose |
|-------|---------|---------|
| [`clap`](https://crates.io/crates/clap) | 4.x | CLI argument parsing with derive macros |
| [`csv`](https://crates.io/crates/csv) | 1.3.x | Streaming CSV parsing (memory efficient) |
| [`serde`](https://crates.io/crates/serde) | 1.x | Serialization/deserialization framework |

### Dependency Tree

```toml
[dependencies]
clap = { version = "4", features = ["derive"] }
csv = "1.3"
serde = { version = "1", features = ["derive"] }
```

## Performance Benchmarks

### File Size: ~1GB

| Metric | Value |
|--------|-------|
| **Processing Time** | ~30-60 seconds* |
| **Peak Memory Usage** | < 500 MB* |

> **Note:** Actual performance depends on:
> - CPU speed and number of cores
> - Disk I/O speed (SSD vs HDD)
> - Number of unique campaigns in the dataset
> - System RAM availability

### Memory Efficiency

This application uses **streaming processing** to handle large files:

- ✅ Does **NOT** load the entire file into memory
- ✅ Processes one record at a time using Rust iterators
- ✅ Uses `BufReader` for efficient disk I/O
- ✅ Memory usage scales with the number of **unique campaigns**, not file size

### Key Optimizations

1. **Streaming CSV Reader**: Uses `csv::Reader` with iterators for lazy evaluation
2. **Buffered I/O**: `BufReader` and `BufWriter` minimize disk operations
3. **HashMap Aggregation**: O(1) lookup for campaign aggregation
4. **Release Mode**: Always build with `--release` for production use

## Input CSV Format

The input file must have the following columns:

| Column | Type | Description |
|--------|------|-------------|
| `campaign_id` | String | Unique campaign identifier |
| `date` | String | Date of the record (YYYY-MM-DD) |
| `impressions` | Integer | Number of ad impressions |
| `clicks` | Integer | Number of clicks |
| `spend` | Float | Ad spend amount |
| `conversions` | Integer | Number of conversions |

## Output CSV Format

### top10_ctr.csv & top10_cpa.csv

| Column | Type | Description |
|--------|------|-------------|
| `campaign_id` | String | Campaign identifier |
| `impressions` | Integer | Total impressions |
| `clicks` | Integer | Total clicks |
| `spend` | Float | Total spend |
| `conversions` | Integer | Total conversions |
| `ctr` | Float | Click-Through Rate (clicks/impressions) |
| `cpa` | Float/Null | Cost Per Acquisition (spend/conversions) |

## Key Rust Concepts Demonstrated

This project showcases important Rust features for developers learning the language:

1. **Ownership & Borrowing** - No garbage collector, automatic memory management
2. **Result<T, E>** - Explicit error handling instead of exceptions
3. **Iterators** - Lazy evaluation for memory-efficient streaming
4. **Pattern Matching** - Exhaustive `match` expressions
5. **Serde** - Compile-time code generation for serialization

---

*Built with Rust for maximum performance and memory safety.*
# ad_performance_aggregator
