# Lab 04 - Alteryx Designer Cloud: Qwik Start

## Lab Summary

This lab focused on using Cloud Dataprep by Alteryx Designer Cloud (Trifacta) to visually explore, clean, transform, and join real-world datasets without writing code. The lab used 2016 United States Federal Election Commission (FEC) data, walking through importing candidate and campaign contribution datasets, correcting mismatched data types, filtering and wrangling records, joining two datasets on a common key, and producing a summarized, renamed results table of campaign contribution metrics by candidate.

## Lab Objective

The objective of this lab was to learn how to use Dataprep to complete the following tasks:

- Import data.
- Correct mismatched data.
- Transform data.
- Join data.

## Tools & Services Used

- Google Cloud Platform (GCP)
- Cloud Dataprep by Alteryx Designer Cloud (Trifacta)
- Cloud Storage
- Cloud Shell / gcloud CLI

## Key Steps Performed

## 1. Created a Cloud Storage Bucket

Created a Cloud Storage bucket to support the Dataprep workflow:

```text
Navigation menu > Cloud Storage > Buckets > Create bucket
```

Used a unique bucket name, left other settings at their defaults, and unchecked **Enforce public access prevention on this bucket**.

## 2. Initialized Cloud Dataprep

Created the Dataprep service identity from Cloud Shell:

```bash
gcloud beta services identity create --service=dataprep.googleapis.com
```

Navigated to Alteryx Designer Cloud:

```text
Navigation menu > View All Products > Analytics > Alteryx Designer Cloud
```

Completed the first-time setup flow:

- Accepted the Google Dataprep Terms of Service.
- Authorized sharing account information with Trifacta (Agree and Continue).
- Clicked **Allow** to grant Trifacta access to project data.
- Signed in to Cloud Dataprep using the lab student username.
- Clicked **Allow** to grant Dataprep access to the lab Google Cloud account.
- Accepted the Trifacta Terms of Service.
- Clicked **Continue** on the First Time Setup screen to create the default storage location.

Alteryx Designer Cloud opened, confirming successful initialization.

## 3. Created a Flow

Created a new flow to organize the datasets and transformation recipes:

```text
Flows icon > Create > Blank Flow
```

Named and described the flow:

```text
Name:        FEC-2016
Description: United States Federal Elections Commission 2016
```

## 4. Imported Datasets

Imported source datasets from a public Cloud Storage path:

```text
Add Datasets > Import Datasets > Cloud Storage
```

```text
Path: gs://spls/gsp105
```

Navigated into the `us-fec/` folder and added two datasets:

```text
cn-2016.txt          -> renamed "Candidate Master 2016"
itcont-2016-orig.txt -> renamed "Campaign Contributions 2016"
```

Clicked **Import & Add to Flow** to add both datasets to the `FEC-2016` flow.

## 5. Prepped the Candidate File

Opened the **Candidate Master 2016** dataset's recipe in the Transformer grid view.

**Filtered to the relevant year range (Column5):**

Widened Column5 to expose the year distribution and selected the tallest bin (year 2016), creating a row-keep step:

```text
Keep rows where (DATE(2015, 1, 1) <= column5) && (column5 < DATE(2020, 1, 1))
```

Added the suggested step to the recipe.

**Corrected a data type mismatch (Column6 - State):**

Identified that Column6 was flagged as a "State" type but contained non-state values (such as "US", paired with "P" in Column7). Resolved the mismatch by changing Column6's type from "State" to "String", clearing the mismatch indicator.

**Filtered to presidential candidates (Column7):**

Used the Column7 histogram to identify and select the "P" (Presidential) bin, filtering out other candidate types (such as "H"). Added the resulting Keep Rows step to the recipe.

## 6. Wrangled the Contributions File and Joined It to the Candidates File

Selected the **Campaign Contributions 2016** dataset from the flow and opened a new recipe for it.

**Removed extra delimiters:**

Added a new step using the Wrangle language to strip stray pipe-delimiter characters from all columns:

```text
replacepatterns col: * with: '' on: `{start}"|"{end}` global: true
```

**Joined the datasets:**

Added a new "Join" step and selected **Candidate Master 2016** as the dataset to join with **Campaign Contributions 2016**.

Configured the join key using Dataprep's suggested key mapping:

```text
Join key: column2 = column11
```

Reviewed the matched key columns, selected all columns from both datasets to include in the joined output, and added the join step to the recipe.

## 7. Summarized the Data

Added a pivot/aggregation step to summarize contribution metrics grouped by candidate identity and party:

```text
pivot value:sum(column16),average(column16),countif(column16 > 0) group: column2,column24,column8
```

This produced a summary table showing, for each candidate, the total contribution sum, average contribution, and count of positive contributions, grouped by candidate ID, candidate name, and party affiliation.

## 8. Renamed Columns

Added a manual column-renaming step to make the summary table easier to interpret:

```text
rename type: manual mapping: [column24,'Candidate_Name'], [column2,'Candidate_ID'], [column8,'Party_Affiliation'], [sum_column16,'Total_Contribution_Sum'], [average_column16,'Average_Contribution_Sum'], [countif,'Number_of_Contributions']
```

Added a final step to round the average contribution figure to a whole number:

```text
set col: Average_Contribution_Sum value: round(Average_Contribution_Sum)
```

The final recipe produced a clean, readable results table showing each presidential candidate's ID, name, party affiliation, total contributions, average contribution, and number of contributions.

## Evidence/Screenshots

Screenshots/Evidence of work are provided in the screenshot folder.

## Results

- Successfully created a Cloud Storage bucket and initialized Cloud Dataprep with a default storage location.
- Imported and renamed two FEC 2016 datasets (Candidate Master and Campaign Contributions) into a single flow.
- Filtered the Candidate Master dataset to 2016 records and corrected a State/String data type mismatch in Column6.
- Filtered candidate records down to presidential candidates only.
- Cleaned delimiter artifacts in the Campaign Contributions dataset and joined it to the Candidate Master dataset on a common key.
- Aggregated contribution data by candidate ID, name, and party affiliation, producing sum, average, and count metrics.
- Renamed and rounded the final summary columns to produce a clean, presentable results table.

## Reflection

This lab provided hands-on experience with code-free, visual data wrangling using Cloud Dataprep. I learned how Dataprep's column-level type indicators and mismatch highlighting make it straightforward to spot and resolve data quality issues, such as a "State" column improperly containing non-state values. Building up a transformation recipe step by step - filtering, correcting types, cleaning delimiters, joining, aggregating, and renaming - illustrated how complex ETL-style workflows can be assembled interactively while remaining fully reproducible and serverless. The join and pivot steps in particular reinforced how Dataprep can combine and summarize large real-world datasets (FEC candidate and contribution records) into a clear, decision-ready summary table without writing any SQL or pipeline code.
