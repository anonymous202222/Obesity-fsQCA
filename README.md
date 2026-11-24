# Food Supply, Socioeconomic Drivers and Obesity in Sub-Saharan Africa

An R Markdown analysis (`analysis.Rmd`) that combines FAO food balance sheets, World Bank-style socioeconomic indicators, and NCD-RisC obesity prevalence estimates for Sub-Saharan African (SSA) countries. It describes trends from 1990 to 2022 and then uses **fuzzy-set Qualitative Comparative Analysis (fsQCA)** to identify combinations of conditions associated with high (and low) obesity prevalence in 1992 and 2022.

## What the analysis does

1. **Food supply data (FAO Food Balance Sheets)**
   - Loads two FAO datasets, a historic one (1990–2013) and a newer one (2010–2022), and cleans and reshapes each to long format.
   - Keeps selected SSA countries plus a "World" series, and harmonises country names (e.g. `Congo` → `Congo, Rep.`, `United Republic of Tanzania` → `Tanzania`).
   - Converts food supply from kg/capita/year to g/capita/day.
   - Groups individual food items into food groups: Cereals, Starchy Roots, Fruit and vegetables, Pulses, Nuts & seeds, Animal Sources (meat, eggs, dairy, fish), Sweets and Sugar, and Oils.

2. **Merging the two FAO series**
   - Compares the overlapping years (2010–2013) of the two datasets, calculates an offset ratio per region and food group, and applies it to the older series so the 1990–2022 series is continuous.

3. **Trend plots**
   - Food supply trends for SSA (average), Kenya and the World.
   - Comparison against EAT-Lancet reference ranges for 1992 and 2022 (SSA and global).
   - Bar graphs of food group shares.

4. **Socioeconomic drivers**
   - Cleans indicator data: employment in agriculture, female employment in services, urban population, arable land, food imports and exports, cereal yield, fertilizer consumption, GDP per capita (PPP), population growth, access to clean cooking fuels.
   - Cleans and merges GNI per capita data.
   - Plots driver trends for SSA, Kenya and the World.

5. **Obesity data (NCD-RisC)**
   - Cleans the country-level and world-level age-standardised BMI/obesity estimates, combines genders, and plots trends.

6. **Correlation analysis**
   - Spearman correlations between obesity prevalence and all numeric variables, for 2022 and for 1992.

7. **fsQCA (packages `QCA` and `SetMethods`)**
   - Calibrates the outcome and conditions into fuzzy sets using percentile-based (10th/50th/90th) thresholds. GNI uses World Bank income group thresholds.
   - Runs necessity analysis for high and low obesity.
   - Builds truth tables (`incl.cut = 0.80`, `n.cut = 2`, `pri.cut = 0.7`) and derives complex, parsimonious and intermediate solutions, with directional expectations for the intermediate solution.
   - Includes a sensitivity analysis and a check of raw values for the cases in each pathway.
   - Repeated for **2022** and **1992**, and for both the presence (high obesity) and absence (low obesity) of the outcome.

## Requirements

- R and RStudio (the notebook uses `View()` calls, which need RStudio)
- R packages:

  ```r
  install.packages(c(
    "dplyr", "tidyverse", "ggplot2", "corrr", "readxl", "tidyr",
    "gridExtra", "patchwork", "corrplot", "Hmisc", "mgcv",
    "minerva", "igraph", "reshape2", "purrr", "readr",
    "QCA", "SetMethods"
  ))
  ```

## Data

The data files are not included in the script and must be placed in a data folder. The notebook expects this layout:

```
DATA_DIR/
├── FoodBalanceSheetsHistoric_E_All_Data.xlsx     # FAOSTAT, 1990–2013
├── FoodBalanceSheets_E_All_Data_new.xlsx         # FAOSTAT, 2010–2022
├── drivers/
│   ├── All countries.xlsx                        # socioeconomic indicators
│   └── GNI.SSA.World.xlsx                        # GNI per capita
└── obesity/
    ├── NCD_RisC_Lancet_2024_BMI_age_standardised_country.csv
    └── NCD_RisC_Lancet_2024_BMI_age_standardised_world (2).csv
```

Sources:

- **FAOSTAT Food Balance Sheets**: https://www.fao.org/faostat/en/#data/FBS
- **World Bank indicators** (drivers and GNI): https://data.worldbank.org
- **NCD-RisC** (obesity/BMI estimates): https://ncdrisc.org

## How to run

1. Place the data files as shown above.
2. Define the two path variables at the top of the notebook (they are used throughout but not set in the file):

   ```r
   DATA_DIR <- "path/to/data"
   FIG_DIR  <- "path/to/figures"   # must exist; plots are saved here
   ```

3. Open `analysis.Rmd` in RStudio and run the chunks in order, or knit the document.

## Outputs

Figures are saved as PNG files in `FIG_DIR`:

| File | Content |
|------|---------|
| `Comparison.png` | Overlap comparison of the two FAO datasets |
| `supply trend.png` | Food supply trends (SSA, Kenya, World) |
| `EAT_ref.png` | Food group supply against EAT-Lancet reference ranges |
| `combined_plot.png` | Combined trend plot |
| `bar_graph1.png` | Food group bar graphs |
| `Drivers.png` | Socioeconomic driver trends |
| `obesity trend.png` | Obesity prevalence trends |

The correlation results and fsQCA tables (necessity, truth tables, solutions) are printed in the R console or notebook output rather than saved to files.

## Notes

- Some chunks are run interactively with `View()` and `print()` calls and can be skipped when re-running.
- Chunk order matters: later sections (fsQCA) depend on objects created in the data-cleaning sections.
- Alternative calibration thresholds (minimum/median/maximum) are left in as commented-out code in the 1992 section.
- The `QCA` and `SetMethods` packages are loaded in the fsQCA section rather than in the first packages chunk.


- This project is licensed under the GNU General Public License v3.0