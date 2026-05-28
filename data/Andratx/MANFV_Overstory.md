# Dataset: Overstory Inventory and Tree Metrics

## Overview
This dataset contains individual tree-level structural data collected during the forest inventory. It records taxonomic species classifications, health status, stem dimensions (DBH, height, crown features), bark thickness across cardinal directions, specific damage codes, and local spatial coordinates (Easting, Northing, Distance, Elevation) for canopy-forming and sub-canopy individuals.

## File Details
* **File Name:** `Overstory.csv` <!-- Replace with your actual file name -->
* **Format:** CSV (Comma-Separated Values)
* **Temporal Scope:** Early 2024 (February to mid-February)

---

## Data Dictionary (Column Descriptions)

### 1. Identifiers & Taxonomy
| Column Name | Data Type | Description | Example / Allowed Values |
| :--- | :--- | :--- | :--- |
| **SiteID** | String | Broad geographic site identifier. | `MANFV` |
| **PlotID** | String | Unique identifier for the specific sampling plot. | `MANFV01`, `MANFV02` |
| **TreeID** | String | Globally unique tracking code for each individual tree. | `MANFV01001` |
| **InventoryDate** | Date | The date the individual tree metrics were collected (DD-Mmm-YY). | `2-Feb-24` |
| **Species** | String | 4-letter genus/species abbreviation code. | `Piha` (Pinus halepensis), `Quil` (Quercus ilex), `Oleu` (Olea europaea) |

### 2. Condition & Structural Dimensions
| Column Name | Data Type | Description | Example / Allowed Values |
| :--- | :--- | :--- | :--- |
| **CondClass** | Integer | Condition/vigor classification code (e.g., 1 = Healthy/Alive, 9 = Dead snag, 4/6/7 = various degrees of decay or burn damage). | `1`, `4`, `9` |
| **Count** | Integer | Stem count or cluster tracker represented by the data row. | `1` |
| **DBH_cm** | Float | **Diameter at Breast Height:** Measured in centimeters at approximately 1.3 meters above the ground surface. | `58.5` |
| **Ht_m** | Float | **Total Tree Height:** Vertical height of the apex above ground level (in meters). Omitted for broken/dead snags. | `12.2` |
| **CBH_m** | Float | **Crown Base Height:** Height from the ground to the lowest continuous live branch forming the canopy layer (in meters). | `3.0` |

### 3. Bark Characteristics & Damages
| Column Name | Data Type | Description | Example / Allowed Values |
| :--- | :--- | :--- | :--- |
| **BarkThic_...** / **BarkThic_...** | Float | *[Partially hidden headers]* Bark thickness measurements taken in specific cardinal directions (e.g., North, East) in centimeters. | `1.3`, `1.2` |
| **BarkThickness_S_cm** | Float | Bark thickness measured specifically on the South-facing side of the stem (in centimeters). | `1.6` |
| **BarkThickness_W_cm** | Float | Bark thickness measured specifically on the West-facing side of the stem (in centimeters). | `1.0` |
| **Damage** | String | Categorical codes indicating visible pathogen, physical, or fire damage indicators. | `8`, `8;9` |

### 4. Spatial Position Metrics
| Column Name | Data Type | Description | Example / Allowed Values |
| :--- | :--- | :--- | :--- |
| **Easting** | Integer | Easting UTM spatial coordinate (Zone 31N) calculated for the specific tree stem location. | `449580` |
| **Northing** | Integer | Northing UTM spatial coordinate (Zone 31N) calculated for the specific tree stem location. | `4383462` |
| **Distance** | Integer | Horizontal distance from the plot center pin to the center of the tree trunk (in meters). | `5`, `13` |
| **Elevation** | Float | Elevation above sea level for the tree location base (in meters). | `258.9` |

---

## Methodological & Data Notes
* **Structural Blanks:** Notice that for dead trees/snags (indicated by `CondClass` values like `9`), variables such as `Ht_m`, `CBH_m`, and bark configurations are left blank. This reflects standard protocol when structural deterioration prevents accurate measurement.
* **Highlighted Spatial Blocks:** Certain coordinates and distances are highlighted within the source spreadsheet interface; these denote spatial boundaries or verification checkpoints checked against the plot boundaries during field data verification.
