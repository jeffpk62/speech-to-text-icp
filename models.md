---

copyright:
  years: 2015, 2019
lastupdated: "2019-01-07"

---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:tip: .tip}
{:important: .important}
{:note: .note}
{:deprecated: .deprecated}
{:pre: .pre}
{:codeblock: .codeblock}
{:screen: .screen}
{:javascript: .ph data-hd-programlang='javascript'}
{:java: .ph data-hd-programlang='java'}
{:python: .ph data-hd-programlang='python'}
{:swift: .ph data-hd-programlang='swift'}

# Languages and models
{: #models}

The {{site.data.keyword.ibmwatson}} {{site.data.keyword.speechtotextshort}}: Customer Care service supports speech recognition in multiple languages. For all interfaces, you can use the `model` parameter to specify a language model for a speech recognition request. The model indicates the language in which the audio is spoken and the rate at which it is sampled. Choosing the correct model for your application is important.
{: shortdesc}

The service supports two models for each language:

-   A *broadband model* for audio that is sampled at greater than or equal to 16 kHz. Use the broadband model for responsive, real-time applications (for example, for live-speech applications).
-   A *narrowband model* for audio that is sampled at 8 kHz. Use the narrowband model for offline decoding of telephone speech, which is where this sampling rate is typically used.

The service automatically adjusts the sampling rate of your audio to match the model that you specify. For more information, see [Sampling rate](/docs/services/speech-to-text-icp/audio-formats.html#samplingRate).

Conversion of speech to text might not be perfect. Speech recognition technology is used successfully in many domains and applications. However, in addition to audio quality, speech recognition is sensitive to nuances of human speech, such as regional accents and differences in pronunciation, and might not always transcribe audio input correctly. Consider using customization to improve speech recognition for your audio. For more information, see [The customization interface](/docs/services/speech-to-text-icp/custom.html).
{: tip}

## Supported language models
{: #modelsList}

Table 1 lists the supported models for each language. If you omit the `model` parameter from a request, the service uses the US English broadband model, `en-US_BroadbandModel`, by default.

<table>
  <caption>Table 1. Supported language models</caption>
  <tr>
    <th style="text-align:left">Language</th>
    <th style="text-align:center">Broadband model</th>
    <th style="text-align:center">Narrowband model</th>
  </tr>
  <tr>
    <td>French</td>
    <td style="text-align:center"><code>fr-FR_BroadbandModel</code></td>
    <td style="text-align:center"><code>fr-FR_NarrowbandModel</code></td>
  </tr>
  <tr>
    <td>Japanese</td>
    <td style="text-align:center"><code>ja-JP_BroadbandModel</code></td>
    <td style="text-align:center"><code>ja-JP_NarrowbandModel</code></td>
  </tr>
  <tr>
    <td>Korean</td>
    <td style="text-align:center"><code>ko-KR_BroadbandModel</code></td>
    <td style="text-align:center"><code>ko-KR_NarrowbandModel</code></td>
  </tr>
  <tr>
    <td>Spanish</td>
    <td style="text-align:center"><code>es-ES_BroadbandModel</code></td>
    <td style="text-align:center"><code>es-ES_NarrowbandModel</code></td>
  </tr>
  <tr>
    <td>US English</td>
    <td style="text-align:center"><code>en-US_BroadbandModel</code></td>
    <td style="text-align:center"><code>en-US_NarrowbandModel</code></br>
      <code>en-US_ShortForm_NarrowbandModel</code></td>
  </tr>
</table>

The US English model `en-US_ShortForm_NarrowbandModel` is intended for use in Interactive Voice Response (IVR) and Automated Customer Support solutions. The model is tuned to recognize the short utterances, such as digits, spellings, months, and so on, that frequently occur in such solutions. It can improve speech recognition for related applications. Use the model that is most appropriate for your application.

The batch-processing interface currently accepts only US English audio.
{: note}

### Language model example
{: #modelsExample}

The following example HTTP request uses the model `en-US-NarrowbandModel` for speech recognition:

```bash
curl -X POST -u "apikey:{apikey}"
--header "Content-Type: audio/flac"
--data-binary @{path}audio-file.flac
"https://{icp_cluster_host}{:port}/speech-to-text/api/v1/recognize?model=en-US_NarrowbandModel"
```
{: pre}

## Listing models
{: #listModels}

The HTTP interface provides two methods for listing information about the supported models:

-   The `GET /v1/models` method lists information about all available models.
-   The `GET /v1/models/{model_id}` method lists information about a specified model.

Both methods return the following information about a model:

-   `name` is the name of the model that you use in a request.
-   `language` is the language identifier of the model (for example, `en-US`).
-   `rate` identifies the sampling rate (minimum acceptable rate for audio) that is used by the model in Hertz.
-   `url` is the URI for the model.
-   `description` provides a brief description of the model.
-   `supported_features` describes the additional service features that are supported with the model:
    -   `custom_language_model` is a boolean that indicates whether you can create custom language models that are based on the model.
    -   `speaker_labels` indicates whether you can use the `speaker_labels` parameter with the model.
    -   `speech_analytics` indicates whether you can use the `speech_analytics` parameter with the model.

### Example requests and responses
{: #listExample}

The following example lists all models that are supported by the service:

```bash
curl -X GET -u "apikey:{apikey}"
"https://{icp_cluster_host}{:port}/speech-to-text/api/v1/models"
```
{: pre}

```javascript
{
  "models": [
    {
      "name": "en-US_BroadbandModel",
      "language": "en-US",
      "url": "https://{icp_cluster_host}/speech-to-text/api/v1/models/en-US_BroadbandModel",
      "rate": 16000,
      "supported_features": {
        "custom_language_model": true,
        "speaker_labels": true,
        "speech_analytics": true
      },
      "description": "US English broadband model."
    },
    {
      "name": "ko-KR_BroadbandModel",
      "language": "ko-KR",
      "url": "https://{icp_cluster_host}/speech-to-text/api/v1/models/ko-KR_BroadbandModel",
      "rate": 16000,
      "supported_features": {
        "custom_language_model": true,
        "speaker_labels": false,
        "speech_analytics": false
      },
      "description": "Korean broadband model."
    },
    . . .
  ]
}
```
{: codeblock}

The following example shows information about the US English broadband model. The model supports both language model customization and speakers labels.

```bash
curl -X GET -u "apikey:{apikey}"
"https://{icp_cluster_host}{:port}/speech-to-text/api/v1/models/en-US_BroadbandModel"
```
{: pre}

```javascript
{
  "rate": 16000,
  "name": "en-US_BroadbandModel",
  "language": "en-US",
  "url": "https://{icp_cluster_host}/speech-to-text/api/v1/models/en-US_BroadbandModel",
  "supported_features": {
    "custom_language_model": true,
    "speaker_labels": true,
    "speech_analytics": true
  },
  "description": "US English broadband model."
}
```
{: codeblock}
