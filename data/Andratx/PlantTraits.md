# Dataset: Plant Functional Traits and Flammability Metrics

## Overview
This dataset serves as a comprehensive trait database for plant species found within the study area. It aggregates taxonomic classifications, functional vegetative and regenerative traits (e.g., Raunkiaer life forms, leaf anatomy, resprouting mechanics), and physical flammability parameters (e.g., heat content, surface-area-to-volume ratio, bulk density) coupled with their respective literature references.

## File Details
* **File Name:** `Plant_Traits.csv` <!-- Replace with your actual file name -->
* **Format:** CSV (Comma-Separated Values)
* **Geographic Context:** Mediterranean ecosystem (species documented using regional Catalan/Balearic common names).

---

## Data Dictionary (Column Descriptions)

### 1. Taxonomy & Basic Identifiers
| Column Name | Data Type | Description | Example / Allowed Values |
| :--- | :--- | :--- | :--- |
| **CommonN** | String | Regional or local common name of the plant species. | `Càrritx`, `Margalló`, `Porrassa` |
| **Family** / **Family2** | String | Botanical family names (including historical or alternative family groupings). | `LILIACEAE`, `Poaceae` |
| **Genus** | String | Taxonomic genus classification. | `Ampelodesmos`, `Cyclamen` |
| **Species** | String | Specific epithet of the plant. | `mauritanicus`, `balearicum` |
| **Authority** | String | The author citation for the scientific name. | `L.`, `Willk.`, `Poiret T. Dyer` |
| **Code** | String | 4-letter operational code matching genus and species initials. | `Amma`, `Cyba`, `Asra` |
| **Group** | String | Major evolutionary plant division. | `monocot`, `dicot` |

### 2. Morphological & Functional Traits
| Column Name | Data Type | Description | Example / Allowed Values |
| :--- | :--- | :--- | :--- |
| **Raunkiaer** | String | Raunkiaer life-form classification system denoting the position of regenerating buds. | `geophyte`, `hemicryptophyte`, `nanophanero` |
| **Woodiness** | String | Structural tissue composition. | `herbaceous`, `woody` |
| **LeafType** | String | Structural leaf adaptation class. | `malacophyllous`, `sclerophyllous` |
| **LeafPheno** | String | Leaf phenology / persistence cycle. | `deciduous`, `evergreen` |
| **LeafShape** | String | Geometric shape profile of the leaves. | `linear`, `broad` |
| **LeafSize** | String | Leaf size classification category based on surface area. | `mesophyll`, `notophyll`, `microphyll` |
| **GrowthForm** | String | Overall structural growth habit of the mature plant. | `forb`, `graminoid`, `large shrub` |
| **Clonality** | String | Type of vegetative expansion or structural arrangement. | `storage-organ`, `caespitos` (tufted), `rhizomatous` |
| **RootType** | String | Morphological structure of the root system. | `tuberous`, `fibrous` |
| **Dispersal** | String | Primary seed dispersal mechanism. | `autochory` (self), `anemochory` (wind), `endozoochory` (animal ingestion) |

### 3. Fire-Ecology & Flammability Traits
| Column Name | Data Type | Description | Example / Allowed Values |
| :--- | :--- | :--- | :--- |
| **Mechanis** | String | Post-fire regeneration strategy. | `resprouter`, `seeder` |
| **Ref.Mechan** | String | Literature reference validating the assigned fire response mechanism. | `Lloret et al.`, `Coca and...` |
| **Awsh** / **Bwsh** / **Ratio** | Float | Custom structural or architectural ratios used in fuel modeling. | `0.96`, `0.71` |
| **Ref. fuels** | String | Primary literature citation for fuel architecture parameters. | `Sánchez-Pinillos et al. (2021)` |
| **BD** | Float | **Bulk Density:** Oven-dry mass of fuel per unit of volume ($g/m^3$ or $kg/m^3$). | `2.62` |
| **Ref.BulkD** | String | Literature reference for the bulk density value. | `Sánchez-Pinillos et al. (2021)` |
| **SAV** | Float | **Surface-Area-to-Volume Ratio:** Higher values indicate finer fuels that ignite rapidly ($m^{-1}$ or $cm^{-1}$). | `3620`, `5740` |
| **Ref.SAV** | String | Literature reference for the SAV ratio calculation. | `Sánchez-Pinillos et al. (2021)` |
| **Heat** | Integer | **High Heat Value (HHV):** Total energy released during complete combustion ($J/g$ or $kJ/kg$). | `18250`, `19740` |
| **Ref.heat** | String | Literature reference for the calorific/heat content. | `Sánchez-Pinillos et al. (2021)` |
| **minFMC** | Float | **Minimum Fuel Moisture Content:** Lowest threshold percentage of moisture content observed in live tissue under drought/fire conditions. | `50`, `63.9`, `20` |
| **Ref.FMC** | String | Literature reference verifying the minimum fuel moisture values. | `Vilà et al.`, `Domenech et al.` |

---

## Technical Column Clarifications
* **Partially Hidden Headers (`Afbt`, `Bfbt`, `Cfbt`, `Dfbt`, `CR`):** These columns are placeholders or custom coefficient attributes associated with biomass distribution equations or structural crown traits (e.g., Crown Ratio or fuel layer fractions). If used in downstream modeling, document their exact mathematical definitions here.
