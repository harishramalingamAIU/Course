# Lab 06 - Speech-to-Text API: Qwik Start
AIzaSyD7R1XqQ0tS9xABDyn6-GtxK72NPcrnI_s

## Lab Summary

This lab focused on using the Speech-to-Text API to transcribe a pre-recorded audio file into text. The lab demonstrated how to generate a restricted API key, build a JSON request body referencing an audio file stored in Cloud Storage, and call the Speech-to-Text API via `curl` from an SSH session to retrieve a text transcription along with a confidence score.

## Lab Objective

The objective of this lab was to learn how to:

- Create an API key.
- Create a Speech-to-Text API request.
- Call the Speech-to-Text API.

## Tools & Services Used

- Google Cloud Platform (GCP)
- Cloud Speech-to-Text API
- Compute Engine
- APIs & Services (Credentials)
- Cloud Storage (sample audio file)
- Cloud Shell / SSH / curl

## Key Steps Performed

## 1. Created an API Key

Navigated to:

```text
Navigation menu > APIs & services > Credentials
```

Created a new credential:

```text
+ Create credentials > API key
```

Restricted the key to only the required API:

```text
API restrictions: Cloud Speech-to-Text API
```

Clicked **Create**, copied the generated API key, and closed the dialog.

## 2. Connected to the Provisioned VM and Stored the API Key

Navigated to:

```text
Navigation menu > Compute Engine > VM instances
```

Connected to the `linux-instance` via the **SSH** button to open an interactive shell, and remained in this SSH session for the rest of the lab.

Saved the API key as an environment variable to avoid repeating it in every request:

```bash
export API_KEY=<YOUR_API_KEY>
```

## 3. Created the Speech-to-Text API Request

Used a pre-recorded sample audio file hosted on Cloud Storage:

```text
gs://cloud-samples-tests/speech/brooklyn.flac
```

Created an empty request file:

```bash
touch request.json
```

Opened the file in `nano` for editing:

```bash
nano request.json
```

Added the request body, specifying the audio encoding, language, and source file URI:

```json
{
  "config": {
      "encoding":"FLAC",
      "languageCode": "en-US"
  },
  "audio": {
      "uri":"gs://cloud-samples-tests/speech/brooklyn.flac"
  }
}
```

Saved and closed the file using `Ctrl+X`, then `Y`, then `Enter`.

The `config` object tells the API how to interpret the audio (`encoding: FLAC`, the format used for `.raw` audio files, and `languageCode: en-US`), while the `audio` object points the API to the file's location in Cloud Storage. `encoding` is the only required config parameter, though additional parameters are available.

## 4. Called the Speech-to-Text API

Sent the request using `curl`, passing the request body and the API key:

```bash
curl -s -X POST -H "Content-Type: application/json" --data-binary @request.json \
"https://speech.googleapis.com/v1/speech:recognize?key=${API_KEY}"
```

Received a transcription response:

```json
{
  "results": [
    {
      "alternatives": [
        {
          "transcript": "how old is the Brooklyn Bridge",
          "confidence": 0.9311077
        }
      ],
      "resultEndTime": "1.770s",
      "languageCode": "en-us"
    }
  ],
  "totalBilledTime": "2s",
  "requestId": "1573191382345569448"
}
```

The `transcript` field contained the API's text transcription of the audio file, while the `confidence` field (a value close to 1) indicated a high degree of certainty in the transcription's accuracy.

Noted that the request used the synchronous `recognize` method, suitable for short, complete audio files. The Speech-to-Text API also supports asynchronous and streaming recognition methods for longer audio or real-time, in-progress speech.

## 5. Saved the API Response

Re-ran the same `curl` command, redirecting the output to a local file for later reference:

```bash
curl -s -X POST -H "Content-Type: application/json" --data-binary @request.json \
"https://speech.googleapis.com/v1/speech:recognize?key=${API_KEY}" > result.json
```

## Evidence/Screenshots

Screenshots/Evidence of work are provided in the screenshot folder.

## Results

- Successfully created an API key restricted to the Cloud Speech-to-Text API.
- Connected to the provisioned Compute Engine instance via SSH and stored the API key as an environment variable.
- Built a valid JSON request referencing a FLAC-encoded audio file stored in Cloud Storage.
- Successfully called the Speech-to-Text API and received an accurate text transcription with a high confidence score.
- Saved the API response to a local `result.json` file.

## Reflection

This lab provided a focused, practical introduction to Google Cloud's Speech-to-Text API. I learned how to scope an API key to a specific service for better security, and how a simple JSON request combining an encoding configuration with a Cloud Storage audio URI is enough to perform accurate speech transcription via a single REST call. The returned confidence score highlighted how the API communicates its own certainty alongside the transcription itself, which is useful for downstream filtering or human review in real applications. This also clarified the distinction between synchronous recognition, used here for a short, complete audio clip, and the asynchronous/streaming methods better suited to longer or real-time audio inputs.
