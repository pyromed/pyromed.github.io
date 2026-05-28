# Dataset: Plot Inventory and Wildfire History

## Overview
This dataset contains spatial, environmental, and inventory data for specific geographic plots. It details topographical characteristics (elevation, slope, aspect), canopy metrics, fuel classifications, field crew information, and wildfire history (specifically tracking events from 1994 and 2013).

## File Details
* **File Name:** `MANFV_PlotInfo.csv` 
* **Format:** CSV (Comma-Separated Values)
* **Coordinate System:** UTM Zone 31N

---

## Data Dictionary (Column Descriptions)

| Column Name | Data Type | Description | Example / Allowed Values |
| :--- | :--- | :--- | :--- |
| **PlotID** | String | Unique identifier for each sampling plot. | `MANFV04` |
| **UTM** | String | Universal Transverse Mercator coordinate zone. | `31N` |
| **Easting_m** | Integer | Easting spatial coordinate in meters. | `448919` |
| **Northing_m** | Integer | Northing spatial coordinate in meters. | `4382430` |
| **Fire_94** | Boolean/Integer | Indicates if the plot was affected by the 1994 fire (1 = Yes, 0 = No). | `1` |
| **Fire_13** | Character | Burn severity or impact class from the 2013 fire. | `H` (High), `M` (Moderate), `0` (None) |
| **TimeSinceLast** | Integer | Years elapsed since the last recorded fire event. | `19` |
| **Features** | String | Special terrain or plot features. | `NT` (No Treatment/No Trees), `T` (Treatment/Trees) *[Adjust based on your project]* |
| **Elevation_m** | Float | Elevation above sea level in meters. | `251.1` |
| **Aspect_deg** | Integer | Compass direction that the slope faces (0° - 360°). | `212` |
| **Slope_pct** | Integer | Incline or steepness of the slope expressed as a percentage. | `15` |
| **Canopy_N** | Integer | Canopy density or coverage metric (North or total count). | `46` |
| **Canc** / **Canop** | Integer | *[Columns cut off in preview]* Likely sub-metrics of canopy cover or structure percentages. | `85`, `33`, `29` |
| **FuelClass** | String | Standardized fuel model or classification code for wildfire behavior modeling. | `TU1`, `SH1`, `GS3` |
| **Crew** | String | Initials of the field crew/identifiers responsible for data collection. | `AAT`, `AAT,SEH` |
| **InventoryDate** | Date | The date the plot data was collected or recorded. | `16-Apr-24` |

---

## Usage & Context
This dataset is designed for environmental analysis, wildfire risk modeling, and vegetation recovery tracking. 

### Key Project Notes:
* **Missing Data:** Where canopy or fuel metrics are zero (`0`), it generally correlates with high burn severity (`H`) from the `Fire_13` column, indicating complete vegetation loss.
* **Temporal Scope:** Field inventories were conducted between April and July 2024.
