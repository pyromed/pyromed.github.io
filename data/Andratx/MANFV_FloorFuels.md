# Dataset: Forest Floor Fuels, Litter, and Duff Depth

## Overview
This dataset contains surface fuel loading information collected via planar intersect sampling (Brown’s Transects). It quantifies downed woody debris (DWD) categorized by time-lag fuel classes alongside systematic point measurements of litter ($T$) and duff ($D$) depth profile measurements across cardinally oriented plot transects.

## File Details
* **File Name:** `MANFV_FloorFuels.csv`
* **Format:** CSV (Comma-Separated Values)
* **Methodology:** Planar Intersect Method (Brown's Transects)

---

## Data Dictionary (Column Descriptions)

| Column Name | Data Type | Description | Example / Allowed Values |
| :--- | :--- | :--- | :--- |
| **SiteID** | String | Broad geographic site identifier. | `MANFV` |
| **PlotID** | String | Unique identifier for the specific sampling plot. | `MANFV01` |
| **InventoryDate** | Date | The date the fuel inventory was conducted (MM/DD/YYYY). | `4/11/2024` |
| **Crew** | String | Initials of the field crew responsible for data collection. | `AAT` |
| **Transect** | String | Compass direction of the transect line originating from plot center. | `NE`, `SE`, `SW`, `NW` |
| **SlopePct** | Integer | The slope incline along the transect line, expressed as a percentage. | `12` |
| **Tally_1hr** | Integer | Count of fine woody debris intersecting the transect tape with a diameter between 0.0 – 0.64 cm (0 – 0.25 inches). | `3` |
| **Tally_10hr** | Integer | Count of fine woody debris intersecting the transect tape with a diameter between 0.64 – 2.54 cm (0.25 – 1 inch). | `1` |
| **Tally_100hr** | Integer | Count of coarse woody debris intersecting the transect tape with a diameter between 2.54 – 7.62 cm (1 – 3 inches). | `0` |
| **Sum_Dian** / **Sum_Dian** | Float | *[Duplicate headers in preview]* Typically holds calculations or diameters for 1000-hr+ heavy fuels ($>3$ inches). | `90.25`, `216.64` |
| **T_0.5** to **T_13.5** | Float | **Litter Depth (Trash/Top organic layer):** Vertical thickness of the loose, undecomposed organic material layer measured at designated intervals (e.g., 0.5m, 1.5m, up to 13.5m) along the transect line. | `1.5` |
| **D_0.5** to **D_13.5** | Float | **Duff Depth:** Vertical thickness of the fermenting and partially decomposed organic material layer located directly below the litter layer, measured at designated intervals. | `0.25` |
| **Comments** | String | Contextual field notes detailing anomalies, obstacle locations, or surrounding conditions. | `trail close`, `@10,5m main` |

---

## Methodological Notes

### Time-Lag Fuel Definition Summary:
* **1-Hour Fuels:** Twigs up to 1/4 inch diameter. Highly responsive to immediate relative humidity changes.
* **10-Hour Fuels:** Wood 1/4 inch to 1 inch diameter. 
* **100-Hour Fuels:** Wood 1 inch to 3 inches diameter.
* **Litter ($T$):** Un-rotted dead leaves, needles, twigs, and bark resting on the forest floor surface.
* **Duff ($D$):** Highly decomposed organic material that has begun blending with underlying mineral soil.

### Depth Sampling Points:
 Paired litter and duff measurements ($T\_x$ and $D\_x$) are recorded in meters at the specific locations along the transect tape dictated by the column header numeric suffix (e.g., `T_0.5` represents the litter depth at exactly $0.5\text{ meters}$).
