---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-29"

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

Regardless of the interface that you use, you must use the API key for your {{site.data.keyword.speechtotextshort}}: Customer Care cluster to make a secure request to the service. If you use a self-signed certificate, you need to disable SSL verification for requests to the service.

## Obtaining your API key
{: #apiKey}

The {{site.data.keyword.speechtotextshort}}: Customer Care service uses API keys for authenticated requests. The service is a single-tenant solution. Each cluster has only a single instance of the service, and that instance has only one ID and one API key. That single service instance owns all resources for the cluster. All requests for the same cluster and service instance use the same API key.

You must use the API key for your service instance to make a secure, authenticated request to the service. You authenticate a request by passing the API key with the request. The key always begins with the string `icp-`.

Use the following steps to learn your API key:

1.  Enter the following `kubectl` command:

    ```bash
    kubectl get secrets speech-to-text-serviceid-secret -o yaml
    ```
    {: pre}

    The output includes an `api_key` field that includes the base64 version of your API key.
1.  Decode the base64 version of the API key by entering the following command:

    ```bash
    echo "{base64_api_key}" | base64 -D
    ```
    {: pre}

    The command returns the API key as a string that begins with the characters `icp-`.
1.  Include the API key in `curl` requests to the service by passing the `-u` option of the `curl` command. For example, the following command performs basic speech recognition for a file named `audio-flac.flac`.

    ```bash
    curl -X POST -u "apikey:{apikey}" \
    --header "Content-Type: audio/flac" \
    --data-binary @{path_to_file}audio-file.flac \
    "https://{icp_cluster_host}{:port}/speech-to-text/api/v1/recognize"
    ```
    {: pre}

    For more information, see the [Getting started tutorial](/docs/services/speech-to-text-icp?topic=speech-to-text-icp-gettingStarted#transcribe) and the following section.

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

The examples in this documentation use the `curl` command to call the service's HTTP interfaces. For more information, see [Using the curl examples](/docs/services/speech-to-text-icp?topic=speech-to-text-icp-gettingStarted#getting-started-curl).
{: note}

## Making an authenticated WebSocket request
{: #websocketRequest}

You can call the WebSocket interface only from application code. You cannot call the WebSocket interface from the command line. You establish a WebSocket connection to the service at the following endpoint

```bash
wss://{icp_cluster_host}/speech-to-text/api/v1/recognize
```
{: pre}

The `{icp_cluster_host}` is required to specify the name or IP address of the host on which the cluster is deployed. For information about making authenticated requests to the WebSocket interface, see [The WebSocket interface](/docs/services/speech-to-text-icp?topic=speech-to-text-icp-websockets).

You cannot use JavaScript to call the WebSocket interface from a browser. The `watson-token` parameter that is available with the `/v1/recognize` method does not accept API keys. See the [Known limitations](/docs/services/speech-to-text-icp?topic=speech-to-text-icp-release-notes#limitations) in the release notes for information about working around this limitation.
{: important}

## Disabling SSL verification
{: #SSLverification}

All Watson services use SSL (or TLS) for secure connections and communications between the client and server. The connection is verified against the local certificate store to ensure authentication, integrity, and confidentiality.

{{site.data.keyword.speechtotextshort}}: Customer Care installs a self-signed certificate. This certificate cannot be successfully verified by default. You can do one of the following to successfully connect to a service instance:

-   Add the self-signed certificate to the truststore for your user agent.

    For example, to make secure requests with `curl`, add the certificate to the truststore for `curl`. To make secure requests from your browser, add the certificate to the truststore for the browser.
-   Disable SSL verification.

    Disabling SSL verification compromises the security of the connection and data. It is highly discouraged. Disable SSL only if absolutely necessary, and take steps to enable SSL as soon as possible.
    {: important}

    -   To disable SSL verification for a `curl` request, use the `--insecure` (`-k`) option with the request. This option directs the command to bypass the tool's verification of SSL certificates.
    -   To disable SSL verification for a WebSocket request, use the appropriate approach for your client library or use one of the {{site.data.keyword.ibmwatson}} {{site.data.keyword.speechtotextshort}} SDKs.

For more information about disabling SSL verification for calls to the service, see *Disabling SSL verification* in the [API reference](https://{DomainName}/apidocs/speech-to-text-icp#disabling-ssl){: external}. The information includes examples for `curl` and for all of the SDKs.
