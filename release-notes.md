---

copyright:
  years: 2015, 2018
lastupdated: "2018-11-15"

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

The following versions of {{site.data.keyword.ibmwatson}} {{site.data.keyword.speechtotextshort}}: Customer Care on IBM&reg; Cloud Private are available. Information includes new features and changes for each version of the product and any known limitations.
{: shortdesc}

## Known limitations
{: #limitations}

{{site.data.keyword.speechtotextshort}}: Customer Care has the following known limitation.

-   **[1.0.0 - 1.0.4]** You cannot use JavaScript to call the WebSocket interface from a browser. The `watson-token` parameter that is available with the `/v1/recognize` method does not accept API keys. To work around this limitation, you can do the following:
    -   Call the WebSocket interface from outside of a browser. You can call the interface from any language that supports WebSockets. Refer to information in [The WebSocket interface](/docs/services/speech-to-text-icp/websockets.html) for guidance when working with another language.

        The Watson SDKs provide the simplest way to call the WebSocket interface from another language. For information about using the WebSocket interface with the Node.js, Java, Python, and Ruby SDKs, see the [API reference ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://{DomainName}/apidocs/speech-to-text-icp){: new_window}.
    -   Use the synchronous or asynchronous HTTP interfaces to perform speech recognition.

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

    **Important:** When you use the `curl` command to make a speech recognition request with the HTTP interface, you must specify the audio format with the `Content-Type` header, specify `"Content-Type: application/octet-stream"`, or specify `"Content-Type:"`. If you omit the header entirely, `curl` uses a default value of `application/x-www-form-urlencoded`. Most of the examples in this documentation continue to specify the format for speech recognition requests regardless of whether it's required.

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

## Version 1.0.1 (9 October 2018)
{: #v101}

-   The name of the Kubernetes secret for {{site.data.keyword.speechtotextshort}}: Customer Care has changed. To learn the API key for your cluster, you now use the string `speech-to-text-serviceid-secret` instead of `ibm-wc-speech-to-text-gdpr-data-deletion-serviceid-secret`. For more information, see [Obtaining your API key](/docs/services/speech-to-text-icp/making-requests.html#apiKey).
-   The {{site.data.keyword.speechtotextshort}}: Customer Care and IBM Watson Assistant services no longer share a namespace on IBM Cloud Private. Therefore, they no longer share the Authentication module; it is installed independently and automatically for {{site.data.keyword.speechtotextshort}}: Customer Care. This issue was referred to only in the Helm chart **README.md** file for the service.
-   The `customization_id` parameter of the speech recognition methods is deprecated and will be removed in a future release. To specify a custom language model for a speech recognition request, use the `language_customization_id` parameter instead. This change applies to the following methods:
    -   `/v1/recognize` for WebSocket requests
    -   `POST /v1/recognize` for synchronous HTTP requests (including multipart requests)
    -   `POST /v1/recognitions` for asynchronous HTTP requests

## Older releases
{: #older}

-   [Version 1.0.0 (21 September 2018)](#v100)

### Version 1.0.0 (21 September 2018)
{: #v100}

The initial release of the service.

{{site.data.keyword.speechtotextshort}}: Customer Care is an on-premise version of the public {{site.data.keyword.speechtotextfull}} service. For information about the {{site.data.keyword.speechtotextshort}} service on the public IBM Cloud, see [About Speech to Text ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://{DomainName}/docs/services/speech-to-text/index.html#about){: new_window}.

{{site.data.keyword.speechtotextshort}}: Customer Care on IBM&reg; Cloud Private differs from the public {{site.data.keyword.speechtotextfull}} service in the following ways. You might find this information helpful if you are already familiar with the {{site.data.keyword.speechtotextshort}} service on the public IBM Cloud.

-   The endpoints for {{site.data.keyword.speechtotextshort}}: Customer Care are specific to your IBM Cloud Private cluster. For more information, see [Making an authenticated HTTP request](/docs/services/speech-to-text-icp/making-requests.html#httpRequest) and [Making an authenticated WebSocket request](/docs/services/speech-to-text-icp/making-requests.html#websocketRequest).
-   You use a common API key for all authenticated requests to {{site.data.keyword.speechtotextshort}}: Customer Care. Each cluster includes a single service instance. This instance has one API key and owns all of the resources in that cluster. For more information, see [Obtaining an API key](/docs/services/speech-to-text-icp/making-requests.html#apiKey).
-   {{site.data.keyword.speechtotextshort}}: Customer Care supports only US English, Japanese, and Korean models. For all three languages, the service supports both both broadband and narrowband models, language model customization (GA), and acoustic model customization (beta). For more information, see [Languages and models](/docs/services/speech-to-text-icp/input.html#models) and [Language support for customization](/docs/services/speech-to-text-icp/custom.html#languageSupport).
-   All custom language and custom acoustic models are owned by the single service instance for your {{site.data.keyword.speechtotextshort}}: Customer Care cluster. For more information, see [Ownership of custom models](/docs/services/speech-to-text-icp/custom.html#customOwner).
-   {{site.data.keyword.speechtotextshort}}: Customer Care does not perform any request logging. You do not need to use the `X-Watson-Learning-Opt-Out` request header.
-   {{site.data.keyword.speechtotextshort}}: Customer Care does not support Watson tokens. You cannot use the `X-Watson-Authorization-Token` request header to authenticate with the service.
