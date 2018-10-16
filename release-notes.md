---

copyright:
  years: 2015, 2018
lastupdated: "2018-10-16"

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

# Release notes
{: #release-notes}

The following versions of {{site.data.keyword.ibmwatson}} {{site.data.keyword.speechtotextshort}}: Customer Care on IBM&reg; Cloud Private are available. Information includes new features and changes for each version of the product and any known limitations.
{: shortdesc}

## Known limitations
{: #limitations}

{{site.data.keyword.speechtotextshort}}: Customer Care has the following known limitation.

-   **[1.0.1, 1.0.2]** You cannot use JavaScript to call the WebSocket interface from a browser. The `watson-token` parameter that is available with the `/v1/recognize` method does not accept API keys, and you cannot pass request headers from JavaScript. To work around this limitation, you can do the following:
    -   Call the WebSocket interface from outside of a browser. You can call the interface from any language that supports WebSockets. Refer to information in [The WebSocket interface](/docs/services/speech-to-text-icp/websockets.html) for guidance when working with another language.

        The Watson SDKs provide the simplest way to call the WebSocket interface from another language. For information about using the WebSocket interface with the Node.js, Java, Python, and Ruby SDKs, see the [API reference ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://console.bluemix.net/apidocs/speech-to-text-icp){: new_window}.
    -   Use the synchronous or asynchronous HTTP interfaces to perform speech recognition.

## Version 1.0.2 (9 October 2018)
{: #v102}

-   The name of the Kubernetes secret for {{site.data.keyword.speechtotextshort}}: Customer Care has changed. To learn the API key for your cluster, you now use the string `speech-to-text-serviceid-secret` instead of `ibm-wc-speech-to-text-gdpr-data-deletion-serviceid-secret`. For more information, see [Obtaining your API key](/docs/services/speech-to-text-icp/making-requests.html#apiKey).
-   The {{site.data.keyword.speechtotextshort}}: Customer Care and IBM Watson Assistant services no longer share a namespace on IBM Cloud Private. Therefore, they no longer share the Authentication module; it is installed independently and automatically for {{site.data.keyword.speechtotextshort}}: Customer Care. This issue was referred to only in the Helm chart **README.md** file for the service.
-   The `customization_id` parameter of the speech recognition methods is deprecated and will be removed in a future release. To specify a custom language model for a speech recognition request, use the `language_customization_id` parameter instead. This change applies to the following methods:
    -   `/v1/recognize` for WebSocket requests
    -   `POST /v1/recognize` for synchronous HTTP requests (including multipart requests)
    -   `POST /v1/recognitions` for asynchronous HTTP requests

## Version 1.0.1 (21 September 2018)
{: #v101}

The initial release of the product.

### Differences from IBM Speech to Text
{: #v101-differences}

{{site.data.keyword.speechtotextshort}}: Customer Care is an on-premise version of the public {{site.data.keyword.speechtotextfull}} service. For information about the {{site.data.keyword.speechtotextshort}} service on the public IBM Cloud, see [About Speech to Text ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://console.bluemix.net/docs/services/speech-to-text/index.html#about){: new_window}.

{{site.data.keyword.speechtotextshort}}: Customer Care on IBM&reg; Cloud Private differs from the public {{site.data.keyword.speechtotextfull}} service in the following ways. You might find this information helpful if you are already familiar with the {{site.data.keyword.speechtotextshort}} service on the public IBM Cloud.

-   The endpoints for {{site.data.keyword.speechtotextshort}}: Customer Care are specific to your IBM Cloud Private cluster. For more information, see [Making an authenticated HTTP request](/docs/services/speech-to-text-icp/making-requests.html#httpRequest) and [Making an authenticated WebSocket request](/docs/services/speech-to-text-icp/making-requests.html#websocketRequest).
-   You use a common API key for all authenticated requests to {{site.data.keyword.speechtotextshort}}: Customer Care. Each cluster includes a single service instance. This instance has one API key and owns all of the resources in that cluster. For more information, see [Obtaining an API key](/docs/services/speech-to-text-icp/making-requests.html#apiKey).
-   {{site.data.keyword.speechtotextshort}}: Customer Care supports only US English, Japanese, and Korean models. For all three languages, the service supports both both broadband and narrowband models, language model customization (GA), and acoustic model customization (beta). For more information, see [Languages and models](/docs/services/speech-to-text-icp/input.html#models) and [Language support for customization](/docs/services/speech-to-text-icp/custom.html#languageSupport).
-   All custom language and custom acoustic models are owned by the single service instance for your {{site.data.keyword.speechtotextshort}}: Customer Care cluster. For more information, see [Ownership of custom models](/docs/services/speech-to-text-icp/custom.html#customOwner).
-   {{site.data.keyword.speechtotextshort}}: Customer Care does not perform any request logging. You do not need to use the `X-Watson-Learning-Opt-Out` request header.
-   {{site.data.keyword.speechtotextshort}}: Customer Care does not support Watson tokens. You cannot use the `X-Watson-Authorization-Token` request header to authenticate with the service.
