---

copyright:
  years: 2018, 2019
lastupdated: "2019-03-07"

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

# Making requests to the service
{: #making-requests}

{{site.data.keyword.ibmwatson}} {{site.data.keyword.speechtotextshort}}: Customer Care on {{site.data.keyword.cloud}} Private offers the following interfaces:

-   *The synchronous HTTP interface*, which provides access to all of the service's functionality, including the customization interfaces. The service accepts requests over the Secure Sockets Layer (SSL) or Transport Layer Security (TLS) protocol. All URLs for requests to the HTTP interface begin with the `https` protocol specification.
-   *The asynchronous HTTP interface*, which provides methods only for speech recognition. The service accepts requests over the SSL or TLS protocol.
-   *The batch-processing HTTP interface*, which provides methods for submitting multiple audio files for speech recognition. The interface includes speech analytics to learn detailed information about conversations and their individual speakers. The service accepts requests over the SSL or TLS protocol.
-   *The WebSocket interface*, which offers only speech recognition. The service accepts requests over the WebSocket Secure protocol. All URLs begin with the `wss` protocol specification.

Regardless of the interface that you use, you must use the API key for your {{site.data.keyword.speechtotextshort}}: Customer Care cluster to make a secure request to the service.

## Obtaining your API key
{: #apiKey}

The {{site.data.keyword.speechtotextshort}}: Customer Care service uses API keys for authenticated requests. The service is a single-tenant solution. Each cluster has only a single instance of the service, and that instance has only one ID and one API key. That single service instance owns all resources for the cluster. All requests for the same cluster and service instance use the same API key.

You must use the API key for your service instance to make a secure, authenticated request to the service. You authenticate a request by passing the API key with the request. The key always begins with the string `icp-`.

You can learn the API key for your cluster by using either of the following approaches:

-   *From a shell,* issue the following command:

    ```bash
    API_KEY_$(kubectl -n speech-services get secret \
    speech-to-text-serviceid-secret \
    -o go-template='{{ index .data "api_key" | base64decode }}')
    ```
    {: pre}

-   *From a browser,* access the following URL:

    **https://{icp_cluster_host}:443/console/configuration/secrets/kube-system/speech-to-text-serviceid-secret**

     On the page that is displayed, click the **unlock** icon under **api_key**. The API key is shown in the **Data** column in the field labeled **api_key**.

## Making an authenticated HTTP request
{: #httpRequest}

The examples in the documentation use the `curl` command to call methods of the {{site.data.keyword.speechtotextshort}}: Customer Care HTTP interface from the command line. You pass the same information when you make a request programmatically from an application. The basic format of an HTTP request includes the following components:

```bash
curl -X {http_method} -u "apikey:{apikey}"
"https://{icp_cluster_host}{:port}/speech-to-text/api/v1/{service_method}"
```
{: pre}

The components provide the following information. The `{}` (braces) indicate variable strings that you must replace with literal values. Do not include the braces in the actual command.

-   `{http_method}` (optional) specifies the HTTP request method for the example: `POST`, `PUT`, `GET`, or `DELETE`. The request method is required with `curl` if it is anything other than `GET`.
-   `{apikey}` (required) is the API key for the service instance in your {{site.data.keyword.speechtotextshort}}: Customer Care cluster. See [Obtaining your API key](#apiKey).
-   `{icp_cluster_host}` (required) is the name or IP address of the host on which the {{site.data.keyword.speechtotextshort}}: Customer Care cluster is deployed.
-   `{:port}` (optional) is the port number at which the service listens for requests on the specified host. The default port number is `443`, which is used for secure communications by the Secure Sockets Layer (SSL) or Transport Layer Security (TLS) interface on the host. You can omit the port number if your cluster uses the default value. Otherwise, you must specify the port number. If you specify a port number, you must precede it with a `:` (colon).
-   `{service_method}` (required) is the method of the {{site.data.keyword.speechtotextshort}}: Customer Care service that you are calling. For example, you use the `recognize` method to make a synchronous HTTP request to the service.

Many methods have longer names and include path parameters that you must specify as part of the request. Most examples also include request headers, query parameters, and other values. Substitute literal values for any variables that are enclosed in braces. Enter all other elements of an example exactly as shown.

You can also log in to your {{site.data.keyword.cloud_notm}} Private cluster by using the {{site.data.keyword.cloud_notm}} Private command-line interface and issue commands from there. For more information, see [Using the {{site.data.keyword.cloud_notm}} Private command-line interface](/docs/services/speech-to-text-icp/install-config.html#usingCLI).

During installation of {{site.data.keyword.speechtotextshort}}: Customer Care, you can opt to use a self-signed certificate rather than an SSL certificate that has been validated by a certificate authority. If you use a self-signed certificate, you need to configure your client to trust self-signed certificates. For example, you can use the `-k` (`--insecure`) option with `curl` commands to bypass the tool's verification of SSL certificates.

Install the version of the `curl` command for your operating system from [curl.haxx.se ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://curl.haxx.se/){: new_window}. Install the version that supports the Secure Sockets Layer (SSL) protocol. Make sure to include the installed binary file on your `PATH` environment variable.
{: note}

You can also log into your {{site.data.keyword.speechtotextshort}}: Customer Care cluster by using the {{site.data.keyword.cloud_notm}} Private command-line interface and issue commands from there. For more information, see  [Using the {{site.data.keyword.cloud_notm}} Private command-line interface](/docs/services/speech-to-text-icp/install-config.html#usingCLI).
{: note}

## Making an authenticated WebSocket request
{: #websocketRequest}

You can call the WebSocket interface only from application code. You cannot call the WebSocket interface from the command line. You establish a WebSocket connection to the service at the following endpoint

```bash
wss://{icp_cluster_host}/speech-to-text/api/v1/recognize
```
{: pre}

The `{icp_cluster_host}` is required to specify the name or IP address of the host on which the cluster is deployed. For information about making authenticated requests to the WebSocket interface, see [The WebSocket interface](/docs/services/speech-to-text-icp/websockets.html).

You cannot use JavaScript to call the WebSocket interface from a browser. The `watson-token` parameter that is available with the `/v1/recognize` method does not accept API keys. See the [Known limitations](/docs/services/speech-to-text-icp/release-notes.html#limitations) in the release notes for information about working around this limitation.
{: important}
