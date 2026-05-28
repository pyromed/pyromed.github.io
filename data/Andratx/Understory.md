# Dataset: Understory Cover and Vegetation Structure

## Overview
This dataset contains line-intercept transect measurements used to quantify understory vegetation cover, composition, and structural characteristics (width and height). It records the specific intervals where vegetation species or ground cover types (e.g., bare soil) intersect established transect lines within each sampling plot.

## File Details
* **File Name:** `Understory.csv` <!-- Replace if your file name is slightly different -->
* **Format:** CSV (Comma-Separated Values)
* **Sampling Method:** Line-Intercept / Belt Transect Method

---

## Data Dictionary (Column Descriptions)

| Column Name | Data Type | Description | Example / Allowed Values |
| :--- | :--- | :--- | :--- |
| **SiteID** | String | Broad geographic site identifier. | `MANFV` |
| **PlotID** | String | Unique identifier for the specific sampling plot. | `MANFV29` |
| **Transect** | String | Directional orientation of the transect tape within the plot. | `NESW` (North-East to South-West), `NWSE` (North-West to South-East) |
| **Species** | String | 4-letter plant species code or ground cover type descriptor. | `Amma`, `soil` |
| **Start_m** | Float | The starting point of the species or cover patch along the transect tape (in meters). | `0.4` |
| **End_m** | Float | The ending point of the species or cover patch along the transect tape (in meters). | `0.6` |
| **Width_m** | Float | The perpendicular width or patch size of the vegetation intercept (in meters). Left blank for abiotic cover like soil. | `0.1` |
| **Ht_avg_m** | Float | The average vertical height of the vegetation patch (in meters). Left blank for abiotic cover. | `0.2` |
| **Date** | Date | The date the transect data was collected (MM/DD/YYYY). | `7/10/2024` |

---

## Species & Cover Reference Key
*[Note: Update this key based on your local botanical data. Below is a common Mediterranean example for the visible code.]*

* **Amma**: *Ampelodesmos mauritanicus* (Mauritania Diss Grass) — *Common fire-stimulated grass in Mediterranean ecosystems.*
* **soil**: Bare soil, ground substrate, or exposed earth (no vegetation intercept).

---

## Methodological Notes
* **Intercept Length Calculation:** To find the total linear cover length for a specific patch, subtract the start point from the end point: 
  $$\text{Intercept Length} = \text{End\_m} - \text{Start\_m}$$
* **Blanks:** Physical structure metrics (`Width_m` and `Ht_avg_m`) are exclusively recorded for live vegetation features and are omitted for ground cover entries like `soil`.
