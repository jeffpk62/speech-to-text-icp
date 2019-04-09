---

copyright:
  years: 2018, 2019
lastupdated: "2019-04-09"

subcollection: speech-to-text-icp

---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:tip: .tip}
{:note: .note}
{:deprecated: .deprecated}
{:important: .important}
{:pre: .pre}
{:codeblock: .codeblock}
{:screen: .screen}
{:javascript: .ph data-hd-programlang='javascript'}
{:java: .ph data-hd-programlang='java'}
{:python: .ph data-hd-programlang='python'}
{:swift: .ph data-hd-programlang='swift'}

# Release notes
{: #release-notes}

The following versions of {{site.data.keyword.ibmwatson}} {{site.data.keyword.speechtotextshort}}: Customer Care on {{site.data.keyword.icpfull}} are available. Information includes new features and changes for each version of the product and any known limitations.
{: shortdesc}

## Known limitations
{: #limitations}

{{site.data.keyword.speechtotextshort}}: Customer Care has the following known limitation.

-   **[1.0.0 - 1.1.0]** You cannot use JavaScript to call the WebSocket interface from a browser. The `watson-token` parameter that is available with the `/v1/recognize` method does not accept API keys. To work around this limitation, you can do the following:
    -   Call the WebSocket interface from outside of a browser. You can call the interface from any language that supports WebSockets. Refer to information in [The WebSocket interface](/docs/services/speech-to-text-icp/websockets.html) for guidance when working with another language.

        The {{site.data.keyword.watson}} SDKs provide the simplest way to call the WebSocket interface from another language. For information about using the WebSocket interface with the Node.js, Java, Python, and Ruby SDKs, see the [API reference ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://{DomainName}/apidocs/speech-to-text-icp){: new_window}.
    -   Use the synchronous, asynchronous, or batch-processing HTTP interface to perform speech recognition.
-   **[1.1.0]** When you use an {{site.data.keyword.cos_full}} server with the batch-processing interface, the service does not currently support the Identity and Access Management (IAM) credentials format. The service requires that you use credentials in the hash-based message authentication code (HMAC) format.

    [{{site.data.keyword.cloud_notm}} Object Storage credentials format](/docs/services/speech-to-text-icp/batch.html#batchCOSCredentialsIBM) incorrectly describes the use of IAM credentials. You must use HMAC credentials instead. For more information about using HMAC credentials, see the following documentation:
    -   [IAM vs HMAC ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://cloud.ibm.com/docs/services/cloud-object-storage/iam?topic=cloud-object-storage-service-credentials#iam-vs-hmac){: new_window}
    -   [Using HMAC credentials ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://cloud.ibm.com/docs/services/cloud-object-storage/hmac?topic=cloud-object-storage-using-hmac-credentials#using-hmac-credentials){: new_window}

    The next release of the service will support the IAM credentials format as documented.
-   **[1.1.0]** When you request speech analytics with the batch-processing HTTP interface, the service can sometimes omit the `conversation_speech_analytics` results from an updated **.json** file. This is an infrequent issue that will be addressed in a future release.

## Version 1.1.0 (18 January 2019)
{: #v110}

{{site.data.keyword.speechtotextshort}}: Customer Care version 1.1.0 includes many significant additions to the existing service.

### New batch-processing HTTP interface with speech analytics
{: #v110-batches}

The service now supports a new batch-processing interface for speech recognition with optional speech analytics. Batch processing accepts multiple audio files in various audio formats with a single request. The interface reads audio data from and writes results to buckets in any Amazon S3-compatible Cloud Object Storage server, such as the Minio Cloud Object Storage server that comes with {{site.data.keyword.cloud}} Private or the {{site.data.keyword.cos_full}} server.

The interface provides two forms of speech recognition:

-   The same speech recognition capabilities that are available with the service's existing WebSocket and HTTP interfaces.
-   Speech analytics for the conversations and speakers from the audio files. The service further analyzes its speech recognition results to produce detailed information about the topics of overall conversation and the tone, sentiment, keywords, and topics of individual speakers.

The batch-processing interface includes the following five methods:

-   `POST /v1/batches` creates a batch-processing job for a collection of files.
-   `GET /v1/batches` lists information about all batch jobs.
-   `GET /v1/batches/{batch_id}` gets information about a specified batch job.
-   `PUT /v1/batches/{batch_id}` updates a specified batch job. Currently, the only supported operation is to cancel a job.
-   `DELETE /v1/batches/{batch_id}` deletes a batch job, canceling the job if it is not already complete.

In addition to the API, the service includes the {{site.data.keyword.speechtotextshort}} Admin Console GUI for monitoring the status of batch-processing jobs. For information about using the batch-processing interface and speech analytics, see the following documentation:

-   [The batch-processing HTTP interface](/docs/services/speech-to-text-icp/batch.html)
-   [Speech analytics](/docs/services/speech-to-text-icp/analytics.html)
-   [API reference ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://{DomainName}/apidocs/speech-to-text-icp){: new_window}

#### Usage requirements
{: #v110-batches-requirements}

The batch-processing interface has the following usage requirements:

-   Batch processing and speech analytics are resource-intensive operations that consume additional processing resources, especially for large amounts of audio. Consider installing additional Virtual Processor Cores (VPCs) to use the batch-processing interface and speech analytics. For more information, see [Installing and configuring the service](/docs/services/speech-to-text-icp/install-config.html).
-   The interface requires the use of COS input and output buckets for communicating audio files and results with the service. It supports any Amazon S3-compatible Cloud Object Storage server. For more information, see [Using Cloud Object Storage](/docs/services/speech-to-text-icp/batch.html#batchCOS). For information about specifying COS credentials and buckets with a request, see [COS parameters](/docs/services/speech-to-text-icp/batch.html#batchCreateCOS).
-   The interface supports only audio files whose format (MIME type) and characteristics (such as sampling rate) the service can derive. For more information, see [Providing audio files](/docs/services/speech-to-text-icp/batch.html#batchAudio).
-   The interface supports US English audio only.

#### Usage differences
{: #v110-batches-differences}

The speech recognition features of the batch-processing interface currently differ from the rest of the interface in a few ways. A future release will address all of these differences to synchronize the service's speech recognition behavior.

-   The interface supports only a subset of the speech recognition parameters that are available with the service's existing interfaces. Specifically, it does not support the following parameters:
    -   `base_model_version`
    -   `grammar_name`
    -   `keywords`
    -   `keywords_threshold`
    -   `max_alternatives`
    -   `word_alternatives_threshold`

    The service always uses the default values for these parameters, regardless of whether you specify them. For more information, see [Speech recognition parameters](/docs/services/speech-to-text-icp/batch.html#batchCreateRecognition).
-   The interface always sets the `timestamps` and `speaker_labels` parameters to `true` for all batch-processing requests, with or without speech analytics. For more information, see [Speech recognition parameters](/docs/services/speech-to-text-icp/batch.html#batchCreateRecognition).
-   The interface embeds a copy of the corresponding `speaker_labels` object into each element of the `timestamps` field. The duplicate speaker labels information is not needed. For more information, see [Understanding the results of a batch job](/docs/services/speech-to-text-icp/batch.html#batchResults).

### New grammar support
{: #v110-grammars}

The service now supports grammars for speech recognition. Grammars are available as beta functionality for all languages that support language model customization. You can add grammars to a custom language model and use them to restrict the set of phrases that the service can recognize from audio. You can define a grammar in Augmented Backus-Naur Form (ABNF) or XML Form.

The grammars interface includes the following four methods:

-   `POST /v1/customizations/{customization_id}/grammars/{grammar_name}` adds a grammar file to a custom language model.
-   `GET /v1/customizations/{customization_id}/grammars` lists information about all grammars for a custom model.
-   `GET /v1/customizations/{customization_id}/grammars/{grammar_name}` returns information about a specified grammar for a custom model.
-   `DELETE /v1/customizations/{customization_id}/grammars/{grammar_name}` removes an existing grammar from a custom model.

You can use a grammar for speech recognition with the WebSocket and the synchronous and asynchronous HTTP interfaces. Use the `language_customization_id` and `grammar_name` parameters to identify the custom model and the grammar that you want to use. Currently, you can use only a single grammar with a speech recognition request.

For more information about grammars, see the following documentation:

-   [Using grammars with custom language models](/docs/services/speech-to-text-icp/grammar.html)
-   [Understanding grammars](/docs/services/speech-to-text-icp/grammar-understand.html)
-   [Adding a grammar to a custom language model](/docs/services/speech-to-text-icp/grammar-add.html)
-   [Using a grammar for speech recognition](/docs/services/speech-to-text-icp/grammar-use.html)
-   [Managing grammars](/docs/services/speech-to-text-icp/grammar-manage.html)
-   [Example grammars](/docs/services/speech-to-text-icp/grammar-examples.html)
-   [API reference ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://{DomainName}/apidocs/speech-to-text-icp){: new_window}

### New languages and models
{: #v110-models}

The service now supports both broadband and narrowband French and Spanish language models, as well as a new US English narrowband model:

-   `fr-FR_BroadbandModel` and `fr-FR_NarrowbandModel`
-   `es-ES_BroadbandModel` and `es-ES_NarrowbandModel`. The new Spanish models support speaker labels and smart formatting.
-   `en-US_ShortForm_NarrowbandModel`. The new US English language model is intended for use in Interactive Voice Response and Automated Customer Support solutions. The new model supports all of the features that are available with the existing US English models.

All new models support language model customization (GA) and acoustic model customization (beta). For more information, see [Language support for customization](/docs/services/speech-to-text-icp/custom.html#languageSupport).

### New numeric redaction feature
{: #v110-redaction}

A new numeric redaction feature is now available to mask numbers that have three or more consecutive digits. Redaction is intended to remove sensitive personal information, such as credit card numbers, from transcripts. You enable the feature by setting the `redaction` parameter to `true` on a recognition request. The feature is beta functionality that is available for US English, Japanese, and Korean only. For more information, see [Numeric redaction](/docs/services/speech-to-text-icp/output.html#redaction).

### Additional changes
{: #v110-additional}

-   The service now supports audio in the G.729 (`audio/g729`) format. The service supports only G.729 Annex D for narrowband audio. For more information about supported audio formats, see [Audio formats](/docs/services/speech-to-text-icp/audio-formats.html).
-   The maximum amount of audio that you can add to a custom acoustic model has increased from 50 hours to 100 hours.

## Version 1.0.4 (12 November 2018)
{: #v104b}

The service now supports smart formatting for Japanese speech recognition. Previously, the service supported smart formatting for US English only. The feature is beta functionality for both supported languages. For more information, see [Smart formatting](/docs/services/speech-to-text-icp/output.html#smart_formatting).

## Version 1.0.4 (26 October 2018)
{: #v104}

-   The `Content-Type` header is now optional for speech recognition requests. The service now automatically detects the audio format (MIME type) of most audio. You must continue to specify the content type for the following formats:
    -   `audio/basic`
    -   `audio/l16`
    -   `audio/mulaw`

    Where indicated, the content type that you specify for these formats must include the sampling rate and can optionally include the number of channels and the endianness of the audio. For all other audio formats, you can omit the content type or specify a content type of `application/octet-stream` to have the service auto-detect the format.

    When you use the `curl` command to make a speech recognition request with the HTTP interface, you must specify the audio format with the `Content-Type` header, specify `"Content-Type: application/octet-stream"`, or specify `"Content-Type:"`. If you omit the header entirely, `curl` uses a default value of `application/x-www-form-urlencoded`. Most of the examples in this documentation continue to specify the format for speech recognition requests regardless of whether it's required.
    {: important}

    This change applies to the following methods:
    -   `/v1/recognize` for WebSocket requests. The `content-type` field of the text message that you send to initiate a request over an open WebSocket connection is now optional.
    -   `POST /v1/recognize` for synchronous HTTP requests. The `Content-Type` header is now optional. (For multipart requests, the `part_content_type` field of the JSON metadata is also now optional.)
    -   `POST /v1/recognitions` for asynchronous HTTP requests. The `Content-Type` header is now optional.

    For more information, see [Audio formats](/docs/services/speech-to-text-icp/audio-formats.html).
-   The argument to the `-n` option of the `kubectl` command that you enter to learn your API key has changed. Because the {{site.data.keyword.speechtotextshort}}: Customer Care service now runs in its own namespace with its own instance of the Authorization module, you enter `speech-services` as the argument instead of `kube-system`:

    ```bash
    API_KEY_$(kubectl -n speech-services get secret \
    speech-to-text-serviceid-secret \
    -o go-template='{{ index .data "api_key" | base64decode }}')
    ```
    {: pre}

    For more information, see [Obtaining your API key](/docs/services/speech-to-text-icp/making-requests.html#apiKey).

## Older releases
{: #older}

-   [Version 1.0.1 (9 October 2018)](#v101)
-   [Version 1.0.0 (21 September 2018)](#v100)

### Version 1.0.1 (9 October 2018)
{: #v101}

-   The name of the Kubernetes secret for {{site.data.keyword.speechtotextshort}}: Customer Care has changed. To learn the API key for your cluster, you now use the string `speech-to-text-serviceid-secret` instead of `ibm-wc-speech-to-text-gdpr-data-deletion-serviceid-secret`. For more information, see [Obtaining your API key](/docs/services/speech-to-text-icp/making-requests.html#apiKey).
-   The {{site.data.keyword.speechtotextshort}}: Customer Care and {{site.data.keyword.watson}} Assistant services no longer share a namespace on {{site.data.keyword.cloud_notm}} Private. Therefore, they no longer share the Authentication module; it is installed independently and automatically for {{site.data.keyword.speechtotextshort}}: Customer Care. This issue was referred to only in the Helm chart **README.md** file for the service.
-   The `customization_id` parameter of the speech recognition methods is deprecated and will be removed in a future release. To specify a custom language model for a speech recognition request, use the `language_customization_id` parameter instead. This change applies to the following methods:
    -   `/v1/recognize` for WebSocket requests
    -   `POST /v1/recognize` for synchronous HTTP requests (including multipart requests)
    -   `POST /v1/recognitions` for asynchronous HTTP requests

### Version 1.0.0 (21 September 2018)
{: #v100}

The initial release of the service.

{{site.data.keyword.speechtotextshort}}: Customer Care is an on-premises version of the public {{site.data.keyword.speechtotextfull}} service. For information about the {{site.data.keyword.speechtotextshort}} service on the public {{site.data.keyword.cloud_notm}}, see [About {{site.data.keyword.speechtotextshort}} ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://{DomainName}/docs/services/speech-to-text/index.html#about){: new_window}.

{{site.data.keyword.speechtotextshort}}: Customer Care on {{site.data.keyword.icpfull_notm}} differs from the public {{site.data.keyword.speechtotextshort}} service in the following ways. You might find this information helpful if you are already familiar with the {{site.data.keyword.speechtotextshort}} service on the public {{site.data.keyword.cloud_notm}}.

-   The endpoints for {{site.data.keyword.speechtotextshort}}: Customer Care are specific to your {{site.data.keyword.icpfull_notm}} cluster. For more information, see [Making an authenticated HTTP request](/docs/services/speech-to-text-icp/making-requests.html#httpRequest) and [Making an authenticated WebSocket request](/docs/services/speech-to-text-icp/making-requests.html#websocketRequest).
-   You use a common API key for all authenticated requests to {{site.data.keyword.speechtotextshort}}: Customer Care. Each cluster includes a single service instance. This instance has one API key and owns all of the resources in that cluster. For more information, see [Obtaining an API key](/docs/services/speech-to-text-icp/making-requests.html#apiKey).
-   {{site.data.keyword.speechtotextshort}}: Customer Care supports only US English, Japanese, and Korean models. For all three languages, the service supports both both broadband and narrowband models, language model customization (GA), and acoustic model customization (beta). For more information, see [Languages and models](/docs/services/speech-to-text-icp/models.html) and [Language support for customization](/docs/services/speech-to-text-icp/custom.html#languageSupport).
-   All custom language and custom acoustic models are owned by the single service instance for your {{site.data.keyword.speechtotextshort}}: Customer Care cluster. For more information, see [Ownership of custom models](/docs/services/speech-to-text-icp/custom.html#customOwner).
-   {{site.data.keyword.speechtotextshort}}: Customer Care does not perform any request logging. You do not need to use the `X-Watson-Learning-Opt-Out` request header.
-   {{site.data.keyword.speechtotextshort}}: Customer Care does not support Watson tokens. You cannot use the `X-Watson-Authorization-Token` request header to authenticate with the service.
