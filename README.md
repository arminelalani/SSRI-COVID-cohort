# SSRI-COVID-COHORT-RECONSTRUCTION
Mortality Risk Among Patients With COVID-19 Prescribed Selective Serotonin Reuptake Inhibitor Antidepressants

## Publication Citation:

Oskotsky T, Marić I, Tang A, et al. Mortality Risk Among Patients With COVID-19 Prescribed Selective Serotonin Reuptake Inhibitor Antidepressants. JAMA Network Open. 2021;4(11):e2133090. doi:10.1001/jamanetworkopen.2021.33090.

## Group Members:

- Arefin Keya

- Faris Raza

- Matthew Anderson

- Armineh Rezagholilalani

## Study Overview and Cohort Construction:

This retrospective cohort study examined whether prescription of selective serotonin reuptake inhibitors (SSRIs) was associated with mortality among adults with COVID-19. The original study used electronic health record data from 87 health care centers in the United States and included patients diagnosed with COVID-19 between January and September 2020, with follow-up lasting up to eight months.

We reproduced two cohorts in UCSF ATLAS:

Patients with COVID-19 and recorded SSRI exposure near the time of COVID-19 diagnosis.
Patients with COVID-19 and no observed SSRI exposure.
Cohort 1: SSRI-Exposed Patients 

### Cohort Entry Event:

The cohort entry date was the earliest qualifying COVID-19 event for each patient between January 1, 2020, and September 30, 2020.

A patient could enter the cohort through any of the following:

A confirmed COVID-19 diagnosis represented by the standard OMOP COVID-19 concept corresponding to the ICD-10-CM diagnosis code U07.1.
A positive or detected molecular SARS-CoV-2 test, including nucleic acid amplification tests.
A positive or detected SARS-CoV-2 immunoassay or antigen test.
The original publication identified COVID-19 using a laboratory-confirmed SARS-CoV-2 test, including nucleic acid amplification tests and immunoassays, and/or ICD-10 code U07.1.

### Inclusion Criteria:

Patients were required to meet all of the following criteria:

- Age 18 years or older at the index event.
- Known gender or sex assigned at birth.
- Known race.
- Known ethnicity.
- At least one recorded SSRI Drug Exposure starting between 10 days before and 7 days after the COVID-19 index date.

The SSRI concept set included the following standard OMOP ingredients and their descendants:

- Escitalopram
- Paroxetine
- Sertraline
- Fluoxetine
- Citalopram
- Vortioxetine
- Fluvoxamine
- Vilazodone

Salt-specific formulations from the publication were represented using their corresponding standard OMOP ingredient concepts. For example, paroxetine hydrochloride and paroxetine mesylate were both represented under paroxetine.

### Cohort Exit:

Cohort exit was defined as 240 days after the index COVID-19 event.

This represented a maximum follow-up period of approximately eight months. Death was not used as a censoring event because death was the primary outcome in the original study.

### ATLAS Cohort Definitions link:

https://atlas-rda.ucsf.edu/WebAPI/cohortdefinition/577

### Limitations:

#### 1) Medication Exposure Definition: 

The largest difference between our UCSF ATLAS implementation and the original publication involved medication exposure.
The publication defined SSRI exposure using medication orders that were active or completed and were not designated as PRN or "as needed." Patients with inactive, discontinued, or unknown order status were excluded.
In UCSF ATLAS, exposure was identified using OMOP Drug Exposure records. The available interface did not allow us to confirm the order status.

#### 2) Differences Between Data Sources 

The original study used the Cerner Real World Data COVID-19 database, which included records from 87 health care centers across the United States.
Our cohorts were created using UCSF OMOP data. Differences in patient population, geographic coverage, clinical settings, coding practices, laboratory-result mapping, medication-data transformation, and demographic completeness may lead to differences between our cohort and the published cohort.

#### 3) Missing or Unmapped Data 

Some source records may have been missing or may not have mapped to standard OMOP concepts.

This may affect:
- Race
- Ethnicity
- COVID-19 test results
- Medication exposure
- Previous medication history

The absence of a recorded SSRI exposure in the control cohort means that no SSRI was observed in the available UCSF data. It does not prove that a patient never used an SSRI outside the health system or before the available observation period.

## Instructions to Run the Code

We first created the cohort definition in ATLAS and generated the SQL using the Microsoft SQL Server dialect. The exported SQL file was then opened in Visual Studio Code within the UCSF Research Analysis Environment (RAE) and run using the MSSQL extension.

Before running the code, we modified the ATLAS-generated script. We removed the final `DELETE` and `INSERT INTO` statements because they were designed to write the cohort into a permanent target cohort table, which was not needed for this assignment. We also removed the final cleanup statements so that the temporary tables, especially `#final_cohort`, would remain available for validation. At the beginning of the script, we added `DROP TABLE IF EXISTS` statements for all temporary tables so that the code could be run multiple times without errors from tables created during a previous run.

After running the full script, we added validation queries to count the total cohort rows and the number of unique people in `#final_cohort`. The final cohort contained 74 rows and 74 unique people.

For the bonus clinical notes analysis, we first identified the UCSF tables needed to link cohort members to their notes. We joined `#final_cohort` to `OMOP_DEID.ucsf.person` using `person_id`, then linked `person.person_source_value` to `CDW_Notes.notes.note_metadata.patientepicid`. We linked the note metadata to `CDW_Notes.notes.note_text` using `deid_note_key`. We then added aggregate queries to count the total note rows, the number of distinct notes, and the number of cohort members with at least one clinical note. The analysis returned 64,241 distinct notes for all 74 cohort members.

All patient-level data and note text were reviewed only inside the secure UCSF RAE environment and were not included in the repository.

## Self-Assessment

| Component | Score | Justification |
|---|---:|---|
| Repository link submitted; README includes the publication title, citation, and group members | 2/2 | The repository link was submitted, and the README includes the publication title, citation, and group members. |
| README describes the cohort and includes the ATLAS cohort link | 2/2 | The README describes the cohort entry event, inclusion criteria, cohort exit, limitations, and provides the ATLAS cohort link. |
| Repository contains the code and files required to complete the analysis | 1/1 | The repository includes the complete Microsoft SQL Server code used to generate and validate the cohort. |
| README contains adequate instructions to reproduce the analysis | 2/2 | The README explains how the SQL was generated in ATLAS, modified, executed in UCSF RAE, and validated. |
| IC AWS Secure cost requirement | 0/0 | The analysis was completed in UCSF RAE rather than IC AWS Secure, so no AWS costs were incurred. |
| Oral presentation is one minute or less | 1/1 | The oral summary was prepared to remain within the one-minute limit. |
| Bonus: Code produces clinical-notes data for the cohort | 2/2 bonus | The SQL links cohort members to the UCSF clinical-notes tables and calculates the number of distinct notes and cohort members with available notes. |
| **Total** | **8/8 + 2 bonus points** | |
