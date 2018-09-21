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

# Release notes
{: #release-notes}

The following versions of {{site.data.keyword.ibmwatson}} {{site.data.keyword.speechtotextshort}}: Customer Care on IBM&reg; Cloud Private are available. The following sections document the new features and changes that were included with each version of the product. They also document any known limitations for each version.
{: shortdesc}

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

### Known limitation
{: #v101-limitations}

This version of {{site.data.keyword.speechtotextshort}}: Customer Care has the following known limitation.

-   You cannot use JavaScript to call the WebSocket interface from a browser. The `watson-token` parameter that is available with the `/v1/recognize` method does not accept API keys, and you cannot pass request headers from JavaScript. To work around this limitation, you can do the following:
    -   Call the WebSocket interface from outside of a browser. You can call the interface from any language that supports WebSockets. Refer to information in [The WebSocket interface](/docs/services/speech-to-text-icp/websockets.html) for guidance when working with another language.

        The Watson SDKs provide the simplest way to call the WebSocket interface from another language. For information about using the WebSocket interface with the Node.js, Java, Python, and Ruby SDKs, see the [API reference ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://console.bluemix.net/apidocs/speech-to-text-icp){: new_window}.
    -   Use the synchronous or asynchronous HTTP interfaces to perform speech recognition.
