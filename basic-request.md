---

copyright:
  years: 2018, 2019
lastupdated: "2019-02-28"

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

# Making a recognition request
{: #basic-request}

To request speech recognition with {{site.data.keyword.ibmwatson}} {{site.data.keyword.speechtotextshort}}: Customer Care, you need to provide only the audio that is to be transcribed. The service offers the same basic transcription capabilities with each of its interfaces.
{: shortdesc}

The following sections show basic transcription requests for each of the service's interfaces:

-   The examples submit a brief FLAC file named <a target="_blank" href="https://watson-developer-cloud.github.io/doc-tutorial-downloads/speech-to-text/audio-file.flac" download="audio-file.flac">audio-file.flac <img src="../../icons/launch-glyph.svg" alt="External link icon" title="External link icon"></a>.
-   The examples use the default language model, `en-US_BroadbandModel`.

[Understanding recognition results](/docs/services/speech-to-text-icp/basic-response.html) describes the service's response for these examples.

## Sending audio with a request
{: #basic-request-audio}

The audio that you pass to the service must be in one of the service's supported formats. For most audio, the service can automatically detect the format. For some audio, you must specify the format with the `Content-Type` or equivalent parameter. For more information, see [Audio formats](/docs/services/speech-to-text-icp/audio-formats.html). (For clarity, the following examples specify the audio format with all requests.)

The service's interfaces accept different maximum amounts of audio. You must send at least 100 bytes of audio with any request.

-   With the WebSocket and synchronous HTTP interfaces, you can pass a maximum of 100 MB of audio data with a single request.
-   With the batch-processing interface, you can pass a maximum of 100 MB of audio data for any individual audio file of a request.
-   With the asynchronous HTTP interface, you can pass a maximum of 1 GB of audio data with a request.

If you are recognizing large amounts of audio, you can manually divide the audio into smaller chunks. But it is usually more efficient and convenient to convert the audio to a compressed, lossy format. Compression can maximize the amount of data that you can send with a single request. Especially if the audio is in WAV or FLAC format, converting it to a lossy format can make an appreciable difference.

-   For more information about audio formats that use compression, see [Supported audio formats](/docs/services/speech-to-text-icp/audio-formats.html#formats).
-   For more information about the effects of compression and about converting your audio to a format that uses it, see [Data limits and compression](/docs/services/speech-to-text-icp/audio-formats.html#limits) and [Audio conversion](/docs/services/speech-to-text-icp/audio-formats.html#conversion).

## Using the WebSocket interface
{: #basic-request-websocket}

[The WebSocket interface](/docs/services/speech-to-text-icp/websockets.html) offers an efficient implementation that provides low latency and high throughput over a full-duplex connection. All requests and responses are sent over the same WebSocket connection. Because of their advantages, WebSockets are the preferred mechanism for speech recognition. For more information, see [Advantages of the WebSocket interface](/docs/services/speech-to-text-icp/developer-overview.html#advantages).

To use the WebSocket interface, you first use the `/v1/recognize` method to establish a connection with the service. You specify parameters such as the language model and any custom models that are to be used for requests that are sent over the connection. You then register event listeners to handle responses from the service. To make a request, you send a JSON text message that includes the audio format and any additional parameters. You pass the audio as a binary message (blob), and then send a text message to signal the end of the audio.

The following example provides JavaScript code that establishes a connection and sends the text and binary messages for a recognition request. The example does not include the code to install the event handlers.

```javascript
var token = {authentication-token};
var wsURI = 'wss://{icp_cluster_host}/speech-to-text/api/v1/recognize'
  + '?watson-token=' + token;
var websocket = new WebSocket(wsURI);

websocket.send(JSON.stringify({
  action: 'start',
  content-type: 'audio/flac'
}));
websocket.send(blob);
websocket.send(JSON.stringify({'action': 'stop'}));
```
{: codeblock}

You cannot use JavaScript to call the WebSocket interface from a browser. The `watson-token` parameter that is available with the `/v1/recognize` method does not accept API keys. For information about working around this limitation, see the [Known limitations](/docs/services/speech-to-text-icp/release-notes.html#limitations) in the release notes.
{: important}

## Using the synchronous HTTP interface
{: #basic-request-http}

[The synchronous HTTP interface](/docs/services/speech-to-text-icp/http.html) provides the simplest way to make a recognition request. You use the `POST /v1/recognize` method to make a request to the service. You pass the audio and all parameters with the single request.

The following `curl` example shows a basic HTTP recognition request:

```bash
curl -X POST -u "apikey:{apikey}"
--header "Content-Type: audio/flac"
--data-binary @audio-file.flac
"https://{icp_cluster_host}{:port}/speech-to-text/api/v1/recognize"
```
{: pre}

## Using the asynchronous HTTP interface
{: #basic-request-async}

[The asynchronous HTTP interface](/docs/services/speech-to-text-icp/async.html) provides a non-blocking interface for transcribing audio. You can use the interface with or without first registering a callback URL with the service. With a callback URL, the service sends callback notifications with job status and recognition results.

The interface uses HMAC-SHA1 signatures based on a user-specified secret to provide authentication and data integrity for its notifications. Without a callback URL, you must poll the service for job status and results. With either approach, you use the `POST /v1/recognitions` method to make a recognition request.

The following `curl` example shows a simple asynchronous HTTP recognition request. The request does not include a callback URL, so you must poll the service to get the job status and the resulting transcript.

```bash
curl -X POST -u "apikey:{apikey}"
--header "Content-Type: audio/flac"
--data-binary @audio-file.flac
"https://{icp_cluster_host}{:port}/speech-to-text/api/v1/recognitions"
```
{: pre}

## Using the batch-processing HTTP interface
{: #basic-request-batch}

[The batch-processing HTTP interface](/docs/services/speech-to-text-icp/batch.html) provides a means of processing many audio files with a single request. It supports many of the basic speech recognition capabilities and most of the audio formats that are supported by the other interfaces. In addition, it can also provide deeper speech analytics for the conversations and individual speakers of the audio files.

You use the `POST /v1/batches` method to make a batch-processing request to the service. You provide the audio files that are to be processed by placing them in a bucket in a Cloud Object Storage (COS) server. The service also writes its results to a bucket in the COS server. You must have a minimum set of permissions on the COS buckets that you use.

The service creates a batch-processing job and processes the input files in stages. The interface includes methods to get the status of batch jobs, to cancel a batch job, or to delete a batch job. The service also provides an Admin Console GUI to check the status of batch jobs.

The following `curl` example shows an HTTP recognition request that includes speech analytics for the batch-processing interface. The request uses a single COS bucket for both input and output. The audio file was previously copied to the COS bucket. The service writes its results for the file to the same bucket.

```bash
curl -X POST -u "apikey:{apikey}"
-form input_credentials_file=@{path}my_cos_credentials.json
-form input_bucket_location=us-geo
-form input_bucket_name=my_cos_bucket
"https://{icp_cluster_host}{:port}/speech-to-text/api/v1/batches?function=recognize&speech_analytics=true"
```
{: pre}
