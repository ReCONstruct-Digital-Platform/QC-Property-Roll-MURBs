# Property Roll Data MURBs Analysis

## Definitions/Details


- Evaluation unit or eval unit, refers specifically to one <RLUEx> entry in the property roll data XML, and 1 line in the extracted CSVs. I may use 'unit' more loosely to describe i.e. different units in an apartment building - which may or may not conrespond to one eval unit.
- `owner_type` -> Statut aux fins d’imposition scolaire (RL0201Hx). Tells you if a physical or moral person owns the eval unit. 
- `owner_status` -> SConditions d’inscription (RL0201U). Tells you if the owner is the landowner, a co-owner, owner of a building on public land, etc.
- `phys_link` -> Lien physique (RL0309A). Gives info about typology.
- `num_dwelling` -> Nombre de logements (RL0311A). A dwelling is defined as "une maison, un appartement, un ensemble de pièces ou une
seule pièce où une personne peut tenir feu et lieu. Il comporte une entrée par l’extérieur ou par un hall commun, des installations
sanitaires ainsi qu’une cuisine ou une installation pour cuisiner. Ces installations disposent de l’eau courante et sont fonctionnelles, même de façon temporaire."
- `num_rental` -> Nombre de chambres locatives (RL0312A). A rental is defined as "Est un lieu d’habitation autre qu’un logement, formé d’une seule pièce et physiquement installé aux fins d’une location exclusive au même occupant pour des périodes d’au moins 30 jours consécutifs."
- `num_adr_inf` -> Numéro inférieur (RL0101Ax). The lowest number of a building in the eval unit. See [p224 in the MEFQ](https://www.mamh.gouv.qc.ca/fileadmin/publications/evaluation_fonciere/manuel_evaluation_fonciere/2022/MEFQ_2022.pdf)
- `num_adr_sup` -> Numéro supérieur (RL0101Cx). The highest number of a building in the eval unit. See [p224 in the MEFQ](https://www.mamh.gouv.qc.ca/fileadmin/publications/evaluation_fonciere/manuel_evaluation_fonciere/2022/MEFQ_2022.pdf)

See more field definitions (in french) here: https://www.donneesquebec.ca/recherche/dataset/061c8cb7-ca4e-45be-a990-61fce7e7d2dc/resource/6f2599be-e49d-4b9a-8702-b12ad0f56141/download/gui_donneesrolesformatouvert_vf20220627.pdf


 <br/>

## Breakdown of CUBF = 1000
Within evaluation units with `CUBF = 1000`, we have the following:

- Eval units with duplicate `(lat, lng, address, muni)`: 

    We call `num_duplicates` the count of these evaluation units. They can be further divided into:

    - Those with `num_duplicates = sum(num_dwelling)` (**248,713** individual units aggregated into **20,054** MURBs) 
      - This means that a MURB has been broken down into individual dwellings in the property roll. I.e. each dwelling is a evaluation unit, with a owner, value, etc.
      - 99% of these are "divided co-ownerships" (copropriété divise), which are condos I think.
      - They be cleanly aggregated into a overall evaluation unit representing the whole building.

       <br/>

      See files:
      - [MURBs_Aggregated_duplicates_good_num_dwell.xlsx](excels/MURBs_Aggregated_duplicates_good_num_dwell.xlsx)
      - [MURBs_Disaggregated_duplicates_good_num_dwell.xlsx](excels/MURBs_Disaggregated_duplicates_good_num_dwell.xlsx) 

    <br/>

    - Those with `num_duplicates != sum(num_dwelling)` (**1,203** individual units aggregated into **69** MURBs)
      - This means that at least one of the duplicate entries has a `num_dwelling` > 1.
      - A lot of these have `sum(num_dwelling)` very close to `num_duplicates`.
      - A few have large differences: 
        - Example: [350 rue ELEANOR](https://www.google.com/maps/place/350+Rue+Eleanor,+Montr%C3%A9al,+QC+H3C+0T5/@45.4930209,-73.5625139,18.63z/data=!4m6!3m5!1s0x4cc91a60df0ed583:0x17ba132001f96c9!8m2!3d45.4930274!4d-73.5624173!16s%2Fg%2F11h60f0t32?entry=ttu) has 148 duplicates and a sum of 277 dwellings. It has 147 individually listed unit, and another eval unit with 130 dwellings, which seems to represent the whole building (minus 17 units I guess). The owner status is 3 ('co-owner') for all entries, with a mix of moral and physical owners.
        - Example: [10 8e avenue in Deux-Montagnes](https://www.google.com/maps/place/10+8e+Ave,+Deux-Montagnes,+QC+J7R+0L7/@45.5356689,-73.883711,19.29z/data=!4m6!3m5!1s0x4cc9254703a35141:0x52e91c6f1af0cce7!8m2!3d45.5353881!4d-73.883721!16s%2Fg%2F11fy9667tg?entry=ttu) has 28 duplicates and a sum of 262 dwellings. It has 27 indiviudal units listed independently (all on the 8th floor) and another entry with 235 dwellings. Here the owner status is 1 ('land owner') for the 235 dwelling entry vs 3 ('co-owner') for the indivually listed units and the physical link is 1 ('detached') vs 5 ('integrated') for the indivually listed units.

      See files:
        <br/>
        - [MURBs_Aggregated_duplicates_bad_num_dwell.xlsx](excels/MURBs_Aggregated_duplicates_bad_num_dwell.xlsx)
        - [MURBs_Disaggregated_duplicates_bad_num_dwell.xlsx](excels/MURBs_Disaggregated_duplicates_bad_num_dwell.xlsx)
      
   <br/>

- Eval units with unique `(lat, lng, address, muni)`:
    - Those with `num_dwelling > 1 ` (**386,900** results)
      - Whole building is considered as a single evaluation unit. No aggregating needed. 
      - Some suspicious data in there, but most seems OK.
        - Example: [100 Rue de Gaspé](https://www.google.com/maps/place/100+Rue+de+Gasp%C3%A9,+Verdun,+QC+H3E+1E5/@45.4528865,-73.5459205,18.15z/data=!4m6!3m5!1s0x4cc9103d90f25651:0x32fc3abc60c869db!8m2!3d45.4531365!4d-73.5455417!16s%2Fg%2F11b8zdqrxc?entry=ttu) is listed as having 3100 dwellings, which seems a bit much. Visually, I count 12 floors with maybe 12 apts on each side, so 12 * 24 = 288 dwellings.
        - Example: [29 rue BECKETT](https://goo.gl/maps/Aqp8v3S2MB3rgJjz8) apparently has 925 dwellings, but doesn't even seem to exist on Google maps...
      - A lot have two numbers in the address, in the `num_adr_inf` and `num_adr_sup` fields. This means the evaluation unit considers more than one building. We can't determine precisely how many since only the lowest and highest numbers associated to buildings in the eval unit are given.
      
      <br/>

      See file:
        - [MURBs_Unique.xlsx](excels/MURBs_Unique.xlsx)
    

     <br/>

    - Those with `num_dwelling = 1` (**2,092,801** results)
      - Single family homes. **EXCLUDED**


 <br/>

So, overall I believe we can say there are 20,054 + 69 + 386,900 = 407,023 MURBs in Quebec. 

We'll probably have to manually go through the 69 weird ones to reduce them to a single entry, which is very doable. Given this, we'll be able to know the number of units in each.

 <br/>


## Further analysis - Retrofit Condidates

I believe it might be interesting to look at some fields we haven't thught a lot about yet:
- those related to the owner
  - `owner_type` tells us if a physical or moral person owns the unit
  - `owner_status` tells us if it's a divided co-ownership or other

- those related to construction type:
  - `phys_link` can tell us about the typology, i.e. row-houses, semi-detached, detached, etc.



 <br/>
 <br/>

# SQL Commands to extract each group

Using the database obtained by running this: https://github.com/ReCONstruct-Digital-Platform/QC-Prop-Roll

**WARNING**: The resulting CSVs can't be opened directly using Excel or you will lose precision on the lat/lng and ID fields.
You should use the 'Data > Import from Text/CSV' option instead, and change the field type for ID, lat and lng to Text instead of Numeric.

 <br/>

## Duplicates with `num_duplicates = sum(num_dwelling)`:

### Aggregated (20,054 results)
```
\copy (select lat, lng, count(*) as num_duplicates, sum(num_dwelling) as sum_dwellings, address, muni, const_yr, const_yr_real, nghbr_unit, avg(lot_area) as avg_lot_area, avg(floor_area) as avg_floor_area, avg(value) as avg_val, avg(prev_value) as avg_prev_val from roll where cubf = 1000 group by lat, lng, address, muni, const_yr, const_yr_real, nghbr_unit having count(*) > 1 and sum(num_dwelling) = count(*) order by count(*) desc) to 'MURBs_dups_good_num_dwell_aggregated.csv' csv header;
```

### Disaggregated (248,713 results)
```
\copy (select id, roll.lat, roll.lng, num_duplicates, sum_dwellings, roll.address, apt_num, roll.muni, arrond, cubf, nghbr_unit, const_yr, const_yr_real, owner_date, owner_type, owner_status, num_rental, lot_lin_dim, lot_area, max_floors, floor_area, phys_link, const_type, value, prev_value from roll inner join (select address, muni, lat, lng, count(*) as num_duplicates, sum(num_dwelling) as sum_dwellings from roll where cubf = 1000 group by lat, lng, address, muni having count(*) > 1 and sum(num_dwelling) = count(*) order by count(*) desc) as murbs on roll.lat=murbs.lat and roll.lng = murbs.lng and roll.address=murbs.address and roll.muni = murbs.muni WHERE cubf = 1000 order by murbs.num_duplicates desc, address, nullif(regexp_replace(apt_num, '\D', '', 'g'), '')::int) to 'MURBs_dups_good_num_dwell_disaggregated.csv' csv header;
```
248,497 of 248,713 (>99%) have owner_status = 3, which is owner of a divided co-ownership = individually owner condos.


 <br/>

## Duplicates with `num_duplicates != sum(num_dwelling)`:


### Aggregated (69 results)
```
\copy (select lat, lng, count(*) as num_duplicates, sum(num_dwelling) as sum_dwellings, address, muni, const_yr, const_yr_real, nghbr_unit, avg(lot_area) as avg_lot_area, avg(floor_area) as avg_floor_area, avg(value) as avg_val, avg(prev_value) as avg_prev_val from roll where cubf = 1000 group by lat, lng, address, muni, const_yr, const_yr_real, nghbr_unit having count(*) > 1 and sum(num_dwelling) != count(*) order by count(*) desc) to  'MURBs_dups_bad_num_dwell_aggregated.csv' csv header;
```


### Disaggregated (1,203 results)
```
\copy (select id, roll.lat, roll.lng, num_duplicates, sum_dwellings, num_dwelling, roll.address, apt_num, roll.muni, arrond, cubf, owner_date, owner_type, owner_status, const_yr, const_yr_real, num_rental, lot_lin_dim, lot_area, max_floors, floor_area, phys_link, const_type, value, prev_value from roll join (select lat, lng, address, muni, count(*) as num_duplicates, sum(num_dwelling) as sum_dwellings from roll where cubf = 1000 group by lat, lng, address, muni having count(*) > 1 and sum(num_dwelling) != count(*) order by count(*) desc) as murbs on roll.address=murbs.address and roll.muni = murbs.muni and roll.lat=murbs.lat and roll.lng = murbs.lng WHERE cubf = 1000 order by murbs.num_duplicates desc, address, nullif(regexp_replace(apt_num, '\D', '', 'g'), '')::int) to 'MURBs_dups_bad_num_dwell_disaggregated.csv' csv header;
```

 <br/>

## Unique entries:

### MURBs `num_dwelling > 1` (386,900 results)

```
\copy (select id, lat, lng, num_dwelling, num_rental, roll.address, apt_num, num_adr_inf, num_adr_sup, roll.muni, arrond, nghbr_unit, const_yr, const_yr_real, owner_date, owner_type, owner_status, phys_link, const_type, lot_lin_dim, lot_area, max_floors, floor_area, value, prev_value from roll INNER JOIN (select address, muni, count(*) from roll where cubf = 1000 group by lat, lng, address, muni having count(*) = 1) as murbs on roll.address = murbs.address and roll.muni = murbs.muni WHERE CUBF = 1000 and num_dwelling > 1 order by num_dwelling desc, address, nullif(regexp_replace(apt_num, '\D', '', 'g'), '')::int) to 'MURBs_unique.csv' csv header;
```


### Single Family Homes `num_dwelling = 1` (2,092,801 results) 

Not Relevant here, but if interested.

```
\copy (select id, lat, lng, roll.address, apt_num, roll.muni, arrond, nghbr_unit, const_yr, const_yr_real, owner_date, owner_type, owner_status, lot_lin_dim, lot_area, max_floors, floor_area, phys_link, const_type, num_rental, value, prev_value from roll INNER JOIN (select address, muni, count(*) as num_duplicates from roll where cubf = 1000 group by lat, lng, address, muni having count(*) = 1) as murbs on roll.address = murbs.address and roll.muni = murbs.muni WHERE CUBF = 1000 and num_dwelling = 1 order by address, nullif(regexp_replace(apt_num, '\D', '', 'g'), '')::int) to 'SingleFamilyHomes.csv' csv header;
```

