# LA County Noisy Address Dataset

This repository contains the raw LA County addressing dataset and multiple processed subsets of **noisy addresses**, along with their grouping, representative selection, and validation results.

---

## Dataset Structure

```text
Noisy_dataset/
├── noisy_addresses/
│   ├── Complete_but_phantom/
│   │   ├── fraction_split/
│   │   │   ├── With_fraction/
│   │   │   │   ├── Complete_But_Phantom_addresses_representative_with_fraction_[metadata]_1040rows.csv
│   │   │   │   └── Complete_But_Phantom_addresses_representative_with_fraction_[validationResult]_1040rows.csv
│   │   │   └── Without_fraction/
│   │   │       ├── complete_but_phantom_without_fraction__[metadata]_5512rows.csv
│   │   │       └── complete_but_phantom_without_fraction__[validationResult]_5512rows.csv
│   │   ├── Complete_But_Phantom_addresses_all_[metadata]_91180rows.csv
│   │   ├── Complete_But_Phantom_addresses_representative_[metadata]_6552rows.csv
│   │   └── Complete_But_Phantom_addresses_representative_[validatedResult]_6552rows.csv
│   └── Incomplete/
│       ├── fraction_split/
│       │   ├── no fraction/
│       │   │   ├── missingUnit/
│       │   │   │   ├── Incomplete_addresses_no_fraction&missingUnit_[metadata]_1755rows.csv
│       │   │   │   └── Incomplete_addresses_no_fraction&missingUnit_[validationResults]_1755rows.csv
│       │   │   ├── others/
│       │   │   │   ├── Incomplete_addresses_no_fraction&others_[metadata]_554rows.csv
│       │   │   │   └── Incomplete_addresses_no_fraction&others_[validationResults]_554rows.csv
│       │   │   ├── Incomplete_addresses_no_fraction_[metadata]_2309rows.csv
│       │   │   └── Incomplete_addresses_no_fraction_[validationResults]_2309rows.csv
│       │   └── with fraction/
│       │       ├── Incomplete_addresses_with_fraction_[metadata]_1857rows.csv
│       │       └── Incomplete_addresses_with_fraction_[validationResults]_1857rows.csv
│       ├── All_incomplete_addresses_[metadata]_140,640rows.csv
│       ├── All_incomplete_addresses_representatives_[metadata]_4166rows.csv
│       └── All_incomplete_addresses_representatives_[validationResults]_4166rows.csv
├── eGIS_Addressing_raw__2700971rows_51cols.csv
├── raw_data_with_U_3122rows_52cols_[metadata].csv
└── raw_data_without_U_2697849rows_52cols_[metadata].csv
```
---

## Dataset Statistics

- Raw dataset: 2,700,971 rows
- Raw data with `U`: 3,122 rows
- Raw data without `U`: 2,697,849 rows
- Complete_but_phantom (all): 91,180 rows
- Complete_but_phantom representatives: 6,552 rows
- Incomplete (all): 140,640 rows
- Incomplete representatives: 4,166 rows

---

## 1. Raw Dataset

### `eGIS_Addressing_raw__2700971rows_51cols.csv`

- Full LA County address dataset
- Approximately 2.7 million records
- Includes structured address components and derived fields

**Key fields:**
- `Number`: main street number
- `StreetName`: main street name
- `PostType`: street type, such as Street, Avenue, Road, Lane, or Court
- `UnitType`: unit descriptor, such as Unit, Suite, or Number
- `UnitName`: unit value
- `zipcode`: ZIP code
- `PostComm1`: primary postal/community field
- `FullAddress`: full address string
- `AIN`: Assessor's Identification Number (parcel ID) used by the county to uniquely identify a property; this attribute is optional and may be missing in some records  
- `geometry_wkt`: geometry in WKT format

---

### `raw_data_with_U_3122rows_52cols_[metadata].csv`

Subset of raw data containing addresses with `U` pattern

These records are separated because the `U` token represents utility-related facilities and therefore requires special handling.

---

### `raw_data_without_U_2697849rows_52cols_[metadata].csv`

Subset of raw data excluding `U` pattern

## 2. Noisy Address Categories
The categorization is based entirely on Google Address Validation results, separating incompleteness from validation-based inconsistency.

All noisy data is stored under: 

```text
noisy_addresses/
```

Two categories:
### `Complete_but_phantom`

Addresses in this category are those that:

- appear structurally complete in the source data
- are not marked as incomplete by validation (`addressComplete = TRUE`)
- but still fail to meet validation quality requirements

Specifically, a record is assigned to this category if one or more of the following conditions are observed:

- `validationGranularity` is not within acceptable levels  
  (e.g., not in `PREMISE`, `PREMISE_PROXIMITY`, or `SUB_PREMISE`)

- `possibleNextAction` is not `ACCEPT`  
  (e.g., `CONFIRM`, `FIX`, or other non-acceptable actions)

- other rule-based conditions derived from validation outputs  
  (e.g., recorded in the `invalid_reason` field)
### `Incomplete`

Addresses in this category are identified based on the `addressComplete` field returned by the validation results.

A record is assigned to this category if:

- `addressComplete != TRUE`

This indicates that the validation system determines the address to be incomplete, meaning that one or more required components are missing or cannot be confidently resolved.


## 3. Complete_but_phantom
```text
Complete_but_phantom/
├── Complete_But_Phantom_addresses_all_[metadata]_91180rows.csv
├── Complete_But_Phantom_addresses_representative_[metadata]_6552rows.csv
├── Complete_But_Phantom_addresses_representative_[validatedResult]_6552rows.csv
└── fraction_split/
```
Addresses that appear structurally complete but are invalid after validation.
---

### 3.1 All Records

#### `Complete_But_Phantom_addresses_all_[metadata]_91180rows.csv`

- Full dataset for this category
- Includes grouping information

**Important columns:**
- ``group_id``: group identifier
- ``group_size``: number of records in group
- ``merge_key``: normalized grouping key
- ``FullAddress``: original address

---

### 3.2 Representative Records

#### `Complete_But_Phantom_addresses_representative_[metadata]_6552rows.csv`

- One representative per group

---

### 3.3 Validation Results

#### `Complete_But_Phantom_addresses_representative_[validatedResult]_6552rows.csv`

- Google Address Validation results

**Key fields:**
- `input_address`: address string submitted to the validation system  
- `validationGranularity`: level of address resolution returned by validation, indicating how precisely the address can be matched (e.g., `PREMISE`, `PREMISE_PROXIMITY`, `SUB_PREMISE`, or coarser levels)  
- `possibleNextAction`: suggested next step provided by the validation system, indicating whether the address can be accepted as-is or requires further confirmation or correction (e.g., `ACCEPT`, `CONFIRM`, `FIX`)  
- `addressComplete`: boolean indicator of whether the address is considered complete by the validation system (`TRUE` or `Null`)  

### 3.4 Fraction Split

```text
fraction_split/
├── With_fraction/
└── Without_fraction/
```

Representative records in this category are further divided based on whether the address contains fractional components, such as `1/2`, `1/4`, or similar patterns.

Fractional addresses are handled separately because they often introduce ambiguity during parsing and validation. For example, fractional values may represent sub-units or informal address extensions, and may not consistently recognized by validation systems.

#### With Fraction
- 1040 rows
- Contains addresses with fractional components (e.g., 123 1/2 Main St)
- Includes both metadata and validation results
- These records are typically more challenging to validate due to non-standard formatting and interpretation

#### Without Fraction
- 5512 rows
- Contains standard address formats without fractional components
- Includes both metadata and validation results
- These records generally follow more conventional address structures and are easier to process and validate

---

## 4. Incomplete
```text
Incomplete/
├── All_incomplete_addresses_[metadata]_140,640rows.csv
├── All_incomplete_addresses_representatives_[metadata]_4166rows.csv
├── All_incomplete_addresses_representatives_[validationResults]_4166rows.csv
└── fraction_split/
```
This folder contains the subset of addresses identified as incomplete, meaning that one or more important address components are missing.

Examples may include missing house number, street name, ZIP code, or unit information.

The purpose of this folder is to preserve:
- the full set of incomplete addresses,
- the grouping structure among similar incomplete patterns,
- one representative address per group, and
- the corresponding validation results for representatives.

### 4.1 Full Incomplete Dataset

#### `All_incomplete_addresses_[metadata]_140,640rows.csv`
This file contains the entire collection of incomplete addresses extracted from the raw dataset.

It includes:
- all incomplete address records,
- original metadata fields.

### 4.2 Incomplete Representative Records
#### `All_incomplete_addresses_representatives_[metadata]_4166rows.csv`
This file contains one representative address per group from the incomplete-address dataset.

The goal of this representative file is to avoid validating every record individually when many records share the same or highly similar incomplete pattern.
Each row therefore stands for one incomplete-address group.

This file includes:
- the representative address string,
- the corresponding group identifier
- original metadata associated with the selected representative.

---

### 4.3 Validation Results

#### `All_incomplete_addresses_representatives_[validationResults]_4166rows.csv`
This file stores the Google Address Validation API results for the representative incomplete addresses.

- Validation Results

### 4.4 Fraction Split
Some incomplete addresses contain fraction-like unit expressions, such as `1/2`, which are common in real address data and may behave differently during parsing and validation.
To support more targeted analysis, the representative set is further divided into two subsets:
```text
fraction_split/
├── with fraction/
└── no fraction/
```

#### with fraction
- 1857 rows

#### no fraction
- 2309 rows

### 4.5 Further Split of the No-Fraction Subset:
The no fraction/ subset is further divided into two subcategories:

```text
no fraction/
├── missingUnit/
└── others/
```

- `missingUnit`: 1755 rows  
This subset contains records where the incompleteness is associated with missing unit information or patterns related to unit-level omission.

- `others`: 554 rows  
This subset contains the remaining no-fraction incomplete representatives

Each subset contains both:
- `[metadata]`
- `[validationResults]`
