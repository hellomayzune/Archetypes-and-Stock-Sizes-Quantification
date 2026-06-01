# README
This *README* describes the following contents.

- [Project information](#project-information)
    - [Sharing, access and project information](#sharing-access-and-project-information)
    - [Database information](#database-information)
- [Repository contents](#repository-contents)
    - [Archetype legend](#archetype-legend)
    - [Area quantification](#area-quantification)
    - [Import data](#import-data)
    - [Export data for archetype classification](#export-data-for-archetype-classification)
    - [Example Python code  for EPC archetypes](#example-python-code--for-epc-archetypes)
    - [Different code format in Verisk compared to EPC](#different-code-format-in-verisk-compared-to-epc)



# Project information
-   *Project Title*: Archetypes and Stock Sizes Quantification
- *Funder*: This is funded by Research England as part of the South Yorkshire Sustainability Centre at the University of Sheffield.
- *Project website*: [South Yorkshire Sustainability Centre](https://www.sysustainabilitycentre.org)


## Sharing, access and project information
Cite this data product as

>Zune M. Archetypes-and-Stock-Sizes-Quantification. [GitHub Repositories](https://github.com/hellomayzune/Archetypes-and-Stock-Sizes-Quantification)

The methodological information of this work can be found in

> Zune, M., Arbabi, H., Densley Tingley, D. (November 26, 2025). Regional Retrofit, Net-Zero Aspirations, and their Whole Life Carbon Burden. Available at SSRN: https://ssrn.com/abstract=5909647. doi: [10.2139/ssrn.5909647](http://dx.doi.org/10.2139/ssrn.5909647)

> Zune M. Towards net-zero archetypes: The performance of system-only, fabric-only, staged and whole-house retrofit. Building Services Engineering Research & Technology. 2025;47(2):195–225. doi: [10.1177/01436244251403042](https://doi.org/10.1177/01436244251403042)


The source database of this work can be found in

> Get Energy Performance of Buildings Data 2026 [cited 2026 May 14]. Available from: [GOV.UK.](https://get-energy-performance-data.communities.gov.uk)

>3D Visual Intelligence: UKBuildings, Verisk. 2024 [cited 2024 November 1]. Available from:  [Verisk](https://www.verisk.com/en-gb/3d-visual-intelligence/products/ukbuildings/)


## Database information
- Source Data: EPC and Verisk
- Format of database: *.csv*, *.xlsx* and *.ipynb*
- Language: English
- Required data space: Less than 150 MB
- README file date created: 14 May 2026.
- README file date modified: To update 
- Programming language: The code is written in `Python` using `Pandas` version 2.2.3.
- License: *MIT open source*

[Back To The Top](#readme)

---

# Repository contents
## Archetype legend
- *SD-V*: large and complex footprint semi-detached house (e.g., Victorian design style)
- *SD-R*: small and rectangular footprint semi-detached house
- *SD-S*: small and complex  footprint semi-detached house
- *B-SD*: small and rectangular or complex footprint semi-detached bungalow
- *B-DT*: large and complex footprint detached bungalow
- *DT-V*:  large and complex footprint detached house (e.g., Victorian design style)
- *DT-R*: small and rectangular footprint detached house
- *DT-L*: large and complex footprint detached house (e.g., storey below 2)
- *T-GM*: large and complex footprint mid-terrace (e.g., Georgian design style)
- *T-GE*:  large and complex footprint end-terrace (e.g., Georgian design style)
- *T-VM*:  small and complex footprint mid-terrace (e.g., Victorian design style)
- *T-VE*: small and complex footprint end-terrace (e.g., Victorian design style)
- *T-RM*: small and rectangular footprint mid-terrace
- *T-RE*: small and rectangular footprint end-terrace

## Area quantification
- **SD-R, Area** <= 100
- **SD-S, Area** > 100 & < 150
- **SD-V, Area**  >= 150
- **TR-E, TR-M, Area** <= 110
- **TV-E, TV-M, Area** > 110 & < 200
- **TG-E, TG-M, Area** >= 200

[Back To The Top](#readme)
## Import data
- File name: **EPC_SouthYorkshire_Clean_202604.csv** 
    - This file is created from the EPC database for South Yorkshire. The shape of the original database is *528925, 94*
    - Data fields used in the *.ipynb*
        - certificate_number (to link with original EPC database)
        - property_type
        - built_form
        - construction_age_band
        - floor_height
        - flat_storey_count
        - total_floor_area

- File name: **Verisk_SouthYorkshire_Correct_premise_floor_count.csv** 
    - This file is exported from the Verisk database for South Yorkshire. The shape of the original database is *614589, 32*
    - Data fields used in the *.ipynb*
        - fid (to link with original GIS database)
        - id (to link with original GIS database)
        - premise_type
        - premise_age 
        - height
        - premise_floor_count
        - premise_area 
        - building_area 
        - gross_area 

## Export data for archetype classification
- EPC database: **SY_domestic_EPC_Archetypes_ERIS.ipynb** 
- Verisk database: **SY_domestic_Verisk_Archetype_ERIS.ipynb** 
- New data feild: `Archetype`

[Back To The Top](#readme)

## Example Python code  for EPC archetypes

- File name: **SY_domestic_EPC_Archetypes_ERIS.ipynb** 
- Import file: **EPC_SouthYorkshire_Clean_202604.csv** 

- Example coldes for Bungalow
```js
// Dataframe for Bungalow Only
dfb = dfALL[(dfALL.property_type == "Bungalow")]
// Define archetype codes
dfb['Archetype'] = np.where(dfb['built_form'] == 'Detached', 'B-DT', 'B-SD')
```

- Example coldes for Detached Houses
```js
// Dataframe for House Only
df = dfALL[(dfALL.property_type == "House")]
// Select "Detached" under Houses
dfd = df[(df.built_form == "Detached")]
// Define code
cond_DT = [dfd['total_floor_area'] <= 150, dfd['total_floor_area'] > 150]
choice_DT = ['DT-R', 'DT-L or DT-V']
dfd['Archetype'] = np.select(cond_DT, choice_DT, default='Unknown')

// We considered DT-L has 1~2 storey, and DT-V has more than 2 storey.
// Get to know storey count
flat_storey_count_list = dfd['flat_storey_count'].unique()
print("\nflat_storey_count with index numbers:")
for i, N_flat_storey_count in enumerate(flat_storey_count_list):
    count_FSC = len(dfd[dfd['flat_storey_count'] == N_flat_storey_count ])
    print(f"{i}: {N_flat_storey_count} ({count_FSC} records)")
```

- Example coldes for Semi-detached Houses

```js
// Select "Semi-Detached" under Houses
dfs = df[(df.built_form == "Semi-Detached")]
cond_SD = [
    dfs['total_floor_area'] <= 100,
    (dfs['total_floor_area'] > 100) & (dfs['total_floor_area'] < 150),
    dfs['total_floor_area'] >= 150
]
choice_SD = ['SD-R', 'SD-S', 'SD-V']
dfs['Archetype'] = np.select(cond_SD, choice_SD, default='Unknown')
```

- Example coldes for Terrace Houses

```js
// Select "End-Terrace" under Houses
dfe = df[(df.built_form == "End-Terrace")]
cond_TE = [
    dfe['total_floor_area'] <= 110,
    (dfe['total_floor_area'] > 110) & (dfe['total_floor_area'] < 200),
    dfe['total_floor_area'] >= 200
]
choice_TE = ['TR-E', 'TV-E', 'TG-E']
dfe['Archetype'] = np.select(cond_TE, choice_TE, default='Unknown')
```

- Example coldes for Terrace Houses

```js
// Select "Mid-Terrace" under Houses
dfm = df[(df.built_form == "Mid-Terrace")]
cond_TM = [
    dfm['total_floor_area'] <= 110,
    (dfm['total_floor_area'] > 110) & (dfm['total_floor_area'] < 200),
    dfm['total_floor_area'] >= 200
]
choice_TM = ['TR-M', 'TV-M', 'TG-M']
dfm['Archetype'] = np.select(cond_TM, choice_TM, default='Unknown')
```

## Different code format in Verisk compared to EPC

- File name: **SY_domestic_Verisk_Archetype_ERIS.ipynb** 
- Import file: **Verisk_SouthYorkshire_Correct_premise_floor_count.csv** 
- Different format in Versisk compared to EPC
    - Verisk does not specified House or Bungalow
    - Floor count and height is therefore required to define House and Bungalow.
    - Verisk has unspecified end- and mid-terrace.
