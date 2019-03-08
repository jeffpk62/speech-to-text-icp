---

copyright:
  years: 2018, 2019
lastupdated: "2019-03-07"

subcollection: speech-to-text-icp

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

# Using a custom acoustic model
{: #acousticUse}

Once you create and train your custom acoustic model, you can use it in speech recognition requests. You use the `acoustic_customization_id` query parameter to specify the custom acoustic model for a request, as shown in the following examples. You must issue the request with service credentials for the instance of the service that owns the model.
{: shortdesc}

You can also specify a custom language model to be used with the request, which can increase transcription accuracy. For more information, see [Using custom language and custom acoustic models during speech recognition](/docs/services/speech-to-text-icp/acoustic-both.html#useBothRecognize).

You can create multiple custom acoustic models for the same or different domains or environments. However, you can specify only one custom acoustic model at a time with the `acoustic_customization_id` parameter.

-   For the [WebSocket interface](/docs/services/speech-to-text-icp/websockets.html), use the `/v1/recognize` method. The specified custom model is used for all requests that are sent over the connection.

    ```javascript
    var token = {authentication-token};
    var wsURI = 'wss://{icp_cluster_host}/speech-to-text/api/v1/recognize'
      + '?watson-token=' + token
      + '&model=en-US_NarrowbandModel'
      + '&acoustic_customization_id={customization_id}';
    var websocket = new WebSocket(wsURI);
    ```
    {: codeblock}
-   For the [synchronous HTTP interface](/docs/services/speech-to-text-icp/http.html), use the `POST /v1/recognize` method. The specified custom model is used for that request.

    ```bash
    curl -X POST -u "apikey:{apikey}"
    --header "Content-Type: audio/flac"
    --data-binary @audio-file1.flac
    "https://{icp_cluster_host}{:port}/speech-to-text/api/v1/recognize?acoustic_customization_id={customization_id}"
    ```
    {: pre}
-   For the [asynchronous HTTP interface](/docs/services/speech-to-text-icp/async.html), use the `POST /v1/recognitions` method. The specified custom model is used for that request.

    ```bash
    curl -X POST -u "apikey:{apikey}"
    --header "Content-Type: audio/flac"
    --data-binary @audio-file.flac
    "https://{icp_cluster_host}{:port}/speech-to-text/api/v1/recognitions?acoustic_customization_id={customization_id}"
    ```
    {: pre}
-   For the [batch-processing HTTP interface](/docs/services/speech-to-text-icp/batch.html), use the `POST /v1/batches` method. The specified custom model is used for all input files of the request.

    ```bash
    curl -X POST -u "apikey:{apikey}"
    -form input_credentials_file=@{path}my_cos_credentials.json
    -form input_bucket_location=us-geo
    -form input_bucket_name=my_cos_bucket
    "https://{icp_cluster_host}{:port}/speech-to-text/api/v1/batches?function=recognize&acoustic_customization_id={customization_id}"
    ```
    {: pre}

You can omit the language model from the request if the custom model is based on the default model, `en-US_BroadbandModel`. Otherwise, you must use the `model` parameter to specify the base model, as shown for the WebSocket example. A custom model can be used only with the base model for which it is created.
