# Lab 05 - Cloud Natural Language API: Qwik Start

## Lab Summary

This lab focused on using the Cloud Natural Language API to perform entity recognition on a snippet of text. The lab demonstrated how to create a dedicated service account and credentials for API access, connect to a provisioned Compute Engine instance via SSH, and send an entity analysis request to extract people, locations, and events from natural language text along with associated metadata and salience scores.

## Lab Objective

The objective of this lab was to learn how to:

- Create an API key (service account credentials) for the Cloud Natural Language API.
- Use the Cloud Natural Language API to extract entities (e.g. people, places, and events) from a snippet of text.

## Tools & Services Used

- Google Cloud Platform (GCP)
- Cloud Natural Language API
- Compute Engine
- IAM Service Accounts
- Cloud Shell / gcloud CLI

## Key Steps Performed

## 1. Set the Project Environment Variable

Set an environment variable with the project ID for use throughout the lab:

```bash
export GOOGLE_CLOUD_PROJECT=$(gcloud config get-value core/project)
```

## 2. Created a Service Account

Created a dedicated service account to access the Natural Language API:

```bash
gcloud iam service-accounts create my-natlang-sa \
  --display-name "my natural language service account"
```

## 3. Created Service Account Credentials

Generated a JSON key file for the new service account and saved it locally:

```bash
gcloud iam service-accounts keys create ~/key.json \
  --iam-account my-natlang-sa@${GOOGLE_CLOUD_PROJECT}.iam.gserviceaccount.com
```

## 4. Set the Application Credentials Environment Variable

Pointed the `GOOGLE_APPLICATION_CREDENTIALS` environment variable to the generated key file so subsequent API calls would authenticate using the new service account:

```bash
export GOOGLE_APPLICATION_CREDENTIALS="/home/USER/key.json"
```

## 5. Connected to the Provisioned Compute Engine Instance

Navigated to:

```text
Navigation menu > Compute Engine > VM Instances
```

Connected to the pre-provisioned Linux instance via the **SSH** button and remained in that SSH session for the rest of the lab.

## 6. Made an Entity Analysis Request

Submitted the following sample text to the Cloud Natural Language API for entity analysis:

```text
Michelangelo Caravaggio, Italian painter, is known for 'The Calling of Saint Matthew'
```

Ran the `gcloud ml language analyze-entities` command and redirected the output to a results file:

```bash
gcloud ml language analyze-entities --content="Michelangelo Caravaggio, Italian painter, is known for 'The Calling of Saint Matthew'." > result.json
```

## 7. Reviewed the Entity Analysis Results

Displayed the contents of the result file:

```bash
cat result.json
```

The response identified three entities, each with type, metadata, salience score, and mentions:

```json
{
  "entities": [
    {
      "name": "Michelangelo Caravaggio",
      "type": "PERSON",
      "metadata": {
        "wikipedia_url": "http://en.wikipedia.org/wiki/Caravaggio",
        "mid": "/m/020bg"
      },
      "salience": 0.83047235
    },
    {
      "name": "Italian",
      "type": "LOCATION",
      "metadata": {
        "mid": "/m/03rjj",
        "wikipedia_url": "http://en.wikipedia.org/wiki/Italy"
      },
      "salience": 0.13870546
    },
    {
      "name": "The Calling of Saint Matthew",
      "type": "EVENT",
      "metadata": {
        "mid": "/m/085_p7",
        "wikipedia_url": "http://en.wikipedia.org/wiki/The_Calling_of_St_Matthew_(Caravaggio)"
      },
      "salience": 0.030822212
    }
  ],
  "language": "en"
}
```

Reviewed the structure of the response, noting that for each entity the API returns:

- The **entity name and type** (e.g. PERSON, LOCATION, EVENT).
- **Metadata**, such as an associated Wikipedia URL where available.
- **Salience**, a value between 0 and 1 representing how central the entity is to the overall text.
- **Mentions**, showing each occurrence of the entity in the text, including its position (`beginOffset`) and mention type (PROPER or COMMON).

## Evidence/Screenshots

Screenshots/Evidence of work are provided in the screenshot folder.

## Results

- Successfully created a dedicated IAM service account and JSON key for Cloud Natural Language API access.
- Configured application default credentials via the `GOOGLE_APPLICATION_CREDENTIALS` environment variable.
- Connected to the provisioned Compute Engine instance via SSH.
- Submitted an entity analysis request to the Cloud Natural Language API using the `gcloud ml language analyze-entities` command.
- Successfully extracted three entities (a PERSON, a LOCATION, and an EVENT) from the sample text, each with associated metadata and salience scores.

## Reflection

This lab provided a concise but practical introduction to Google Cloud's pre-trained machine learning APIs. I learned how a dedicated service account and key file can be used to authenticate API requests independently of a user's personal credentials, which is a useful pattern for application-level access control. Running the entity analysis request demonstrated how the Cloud Natural Language API goes beyond simple keyword matching - identifying entity types, linking to external knowledge sources like Wikipedia, and scoring each entity's salience to indicate its relative importance within the text. This highlighted how managed NLP APIs can add structured understanding to unstructured text with a single API call, without requiring any custom model training.
