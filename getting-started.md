---

copyright:
  years: 2015, 2018
lastupdated: "2018-09-20"

---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:tip: .tip}
{:pre: .pre}
{:codeblock: .codeblock}
{:screen: .screen}
{:javascript: .ph data-hd-programlang='javascript'}
{:java: .ph data-hd-programlang='java'}
{:python: .ph data-hd-programlang='python'}
{:swift: .ph data-hd-programlang='swift'}
{:download: .download}

# Getting started tutorial
{: #gettingStarted}

{{site.data.keyword.ibmwatson}} {{site.data.keyword.speechtotextshort}}: Customer Care transcribes audio to text to enable speech transcription capabilities for applications. This cURL-based tutorial can help you get started quickly with the service. The examples show you how to call the service's `POST /v1/recognize` method to request a transcript.
{: shortdesc}

## Before you begin
{: #before-you-begin}

Before you can you use {{site.data.keyword.speechtotextshort}}: Customer Care, you must first install and configure IBM&reg; Cloud Private. You then deploy {{site.data.keyword.speechtotextshort}}: Customer Care on your IBM Cloud Private installation.

You are now ready to call methods of the service's interface. The following cURL examples use the API key for your {{site.data.keyword.speechtotextshort}}: Customer Care cluster. To learn your API key, see [Obtaining your API key](/docs/services/speech-to-text-icp/making-requests.html#apiKey).

You pass the value of the API key with the `-u` (`--user`) option of the cURL command. Replace `{api_key}` with the value of the API key for your cluster. (Do not include the braces when you specify the API key.)

```bash
curl -u "apikey:{api_key}"
. . .
```
{: pre}

You can also log into your IBM Cloud Private cluster by using the IBM Cloud Private command-line interface and issue commands from there. For more information, see  [Using the IBM Cloud Private command-line interface](/docs/services/speech-to-text-icp/install-config.html#usingCLI).

**Note:** The examples use cURL to call methods of the HTTP interface. Install the version for your operating system from [curl.haxx.se ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://curl.haxx.se/){: new_window}. Install the version that supports the Secure Sockets Layer (SSL) protocol. Make sure to include the installed binary file on your `PATH` environment variable.

## Step 1: Transcribe audio with no options
{: #transcribe}

Call the `POST /v1/recognize` method to request a basic transcript of a FLAC audio file with no additional request parameters.

1.  Download the sample audio file <a target="_blank" href="https://watson-developer-cloud.github.io/doc-tutorial-downloads/speech-to-text/audio-file.flac" download="audio-file.flac">audio-file.flac <img src="../../icons/launch-glyph.svg" alt="External link icon" title="External link icon" class="style-scope doc-content"></a>.
1.  Issue the following command to call the service's `/v1/recognize` method for basic transcription with no parameters. The example uses the `Content-Type` header to indicate the type of the audio, `audio/flac`. The example uses the default language model, `en-US_BroadbandModel`, for transcription.
    -   Replace `{api_key}` with the value of the API key for your service cluster.
    -   Modify `{path_to_file}` to specify the location of the `audio-file.flac` file.

    ```bash
    curl -X POST -u "apikey:{api_key}" \
    --header "Content-Type: audio/flac" \
    --data-binary @{path_to_file}audio-file.flac \
    "https://{icp_cluster_host}{:port}/speech-to-text/api/v1/recognize"
    ```
    {: pre}

    The service returns the following transcription results:

    ```javascript
    {
      "results": [
        {
          "alternatives": [
            {
              "confidence": 0.8691191673278809,
              "transcript": "several tornadoes touch down as a line of
severe thunderstorms swept through colorado on sunday "
            }
          ],
          "final": true
        }
      ],
      "result_index": 0
    }
    ```
    {: codeblock}

## Step 2: Transcribe audio with options
{: #transcribeOptions}

Call the `POST /v1/recognize` method to transcribe the same FLAC audio file, but specify two transcription parameters.

1.  If necessary, download the sample audio file <a target="_blank" href="https://watson-developer-cloud.github.io/doc-tutorial-downloads/speech-to-text/audio-file.flac" download="audio-file.flac">audio-file.flac <img src="../../icons/launch-glyph.svg" alt="External link icon" title="External link icon" class="style-scope doc-content"></a>.
1.  Issue the following command to call the service's `/v1/recognize` method with two extra parameters. Set the `timestamps` parameter to `true` to indicate the beginning and end of each word in the audio stream. Set the `max_alternatives` parameter to `3` to receive the three most likely alternatives for the transcription. The example uses the `Content-Type` header to indicate the type of the audio, `audio/flac`, and the request uses the default model, `en-US_BroadbandModel`.
    -   Replace `{api_key}` with the value of the API key for your service cluster.
    -   Modify `{path_to_file}` to specify the location of the `audio-file.flac` file.

    ```bash
    curl -X POST -u "apikey:{api_key}" \
    --header "Content-Type: audio/flac" \
    --data-binary @{path_to_file}audio-file.flac \
    "https://{icp_cluster_host}{:port}/speech-to-text/api/v1/recognize?timestamps=true&max_alternatives=3"
    ```
    {: pre}

    The service returns the following results, which include timestamps and three alternative transcriptions:

    ```javascript
    {
      "results": [
        {
          "alternatives": [
            {
              "timestamps": [
                ["several":, 1.0, 1.51],
                ["tornadoes":, 1.51, 2.15],
                ["touch":, 2.15, 2.5],
                . . .
              ]
            },
            {
              "confidence": 0.8691191673278809,
              "transcript": "several tornadoes touch down as a line
of severe thunderstorms swept through colorado on sunday "
            },
            {
              "transcript": "several tornadoes touched down as a line
of severe thunderstorms swept through colorado on sunday "
            },
            {
              "transcript": "several tornadoes touch down is a line
of severe thunderstorms swept through colorado on sunday "
            }
          ],
          "final": true
        }
      ],
      "result_index": 0
    }
    ```
    {: codeblock}

## Next steps

-   Learn more about the interfaces and SDKs that are available for making speech recognition requests in the [Overview for developers](/docs/services/speech-to-text-icp/developer-overview.html).
-   Learn about making requests to the service in [Making requests to the service](/docs/services/speech-to-text-icp/making-requests.html).
-   See basic speech recognition requests for each of the service's interfaces in [Making a recognition request](/docs/services/speech-to-text-icp/basic-request.html).
-   Find detailed information about all methods of the service's interfaces in the [API reference ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://console.bluemix.net/apidocs/speech-to-text-icp){: new_window}.