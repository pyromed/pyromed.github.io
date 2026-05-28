# Dataset: Plant Recruitment and Regeneration Inventory

## Overview
This dataset captures plant recruitment (seedling and sapling regeneration) data across multiple sampling plots. It tracks the abundance of specific plant species categorized into four distinct growth, height, or size classes to analyze forest recovery and vegetation structure.

## File Details
* **File Name:** `MANFV_Recruitment.csv` 
* **Format:** CSV (Comma-Separated Values)
* **Temporal Scope:** Data collected in early 2024.

---

## Data Dictionary (Column Descriptions)

| Column Name | Data Type | Description | Example / Allowed Values |
| :--- | :--- | :--- | :--- |
| **SiteID** | String | Broad geographic site identifier. | `MANFV` |
| **PlotID** | String | Unique identifier for the specific sampling plot within the site. | `MANFV01`, `MANFV02` |
| **InventoryDate** | Date | The date the recruitment survey was conducted (MM/DD/YYYY). | `4/11/2024` |
| **Species** | String | 4-letter genus/species abbreviation code for the observed plant. | `Piha`, `Quil`, `Oleu`, `Cesi` |
| **Class_1** | Integer | Count of individuals in Size/Height Class 1 (e.g., < 10 cm). | `2` |
| **Class_2** | Integer | Count of individuals in Size/Height Class 2 (e.g., 10-30 cm). | `15` |
| **Class_3** | Integer | Count of individuals in Size/Height Class 3 (e.g., 30-50 cm). | `20` |
| **Class_4** | Integer | Count of individuals in Size/Height Class 4 (e.g., > 50 cm / saplings). | `3` |

---

## Species Reference Key
*[Note: You may want to update these definitions based on your specific regional flora. The examples below are common ecological codes for European/Mediterranean species.]*

* **Piha**: *Pinus halepensis* (Aleppo Pine)
* **Quil**: *Quercus ilex* (Holm Oak)
* **Oleu**: *Olea europaea* (Wild Olive)
* **Cesi**: *Cistus albidus* / *Cistus silviifolius* (Rockrose species)

---

## Size/Height Class Breakdown
*[Fill this section in with your project's exact protocol dimensions. For example:]*
* **Class 1:** Seedlings from 0 to 10 cm in height.
* **Class 2:** Seedlings/Saplings from 11 to 30 cm in height.
* **Class 3:** Saplings from 31 to 50 cm in height.
* **Class 4:** Saplings > 50 cm in height with a DBH < X cm.
