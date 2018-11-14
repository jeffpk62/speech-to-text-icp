---

Copyright:
  years: 2015, 2018
lastupdated: "2018-11-13"

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

# About
{: #about}

**Service update:** *The {{site.data.keyword.speechtotextshort}}: Customer Care service was updated on November 12, 2018. The service now supports smart formatting as beta functionality for Japanese speech recognition. For more information, see [Version 1.0.4 (12 November 2018)](/docs/services/speech-to-text-icp/release-notes.html#v104b) in the release notes.*

{{site.data.keyword.ibmwatson}} {{site.data.keyword.speechtotextshort}}: Customer Care provides speech recognition capabilities for your IBM&reg; Cloud Private solutions. The service leverages machine learning to combine knowledge of grammar, language structure, and the composition of audio and voice signals to accurately transcribe the human voice. It continuously updates and refines its transcription as it receives more speech.
{: shortdesc}

{{site.data.keyword.speechtotextshort}}: Customer Care is ideal for clients who need to extract high-quality speech transcripts from call center audio. Clients in industries such as financial services, healthcare, insurance, and telecommunication often need to develop cloud-native applications but are regulated or limited in their adoption of public cloud offerings. The service allows such clients to build on-premises applications for customer care, customer voice, agent assistance, and other solutions.

For information about installing and configuring {{site.data.keyword.speechtotextshort}}: Customer Care on IBM Cloud Private, see [Installing and configuring the service](/docs/services/speech-to-text-icp/install-config.html).

**Note:** {{site.data.keyword.speechtotextshort}}: Customer Care is an on-premise version of the public {{site.data.keyword.speechtotextfull}} service. For information about the {{site.data.keyword.speechtotextshort}} service on the public IBM Cloud, see [About Speech to Text ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://console.bluemix.net/docs/services/speech-to-text/index.html#about){: new_window}.

## Supported interfaces
{: #interfaces}

{{site.data.keyword.speechtotextshort}}: Customer Care offers three functionally equivalent interfaces for speech recognition:

-   A [WebSocket interface](/docs/services/speech-to-text-icp/websockets.html) for establishing persistent, full-duplex, low-latency connections with the service.
-   An [HTTP interface](/docs/services/speech-to-text-icp/http.html) for synchronous calls to the service.
-   An [asynchronous HTTP interface](/docs/services/speech-to-text-icp/async.html) for non-blocking calls to the service.

The service also provides a [customization interface](/docs/services/speech-to-text-icp/custom.html) that you can use to tune speech recognition for your language and acoustic requirements. You can expand the vocabulary of a model with domain-specific terminology or adapt a model for the acoustic characteristics of your audio. SDKs are also available to simplify your use of the service's interfaces in various programming languages.

-   For information about making requests with each of the {{site.data.keyword.speechtotextshort}}: Customer Care interfaces, see [Making requests to the service](/docs/services/speech-to-text-icp/making-requests.html).
-   For examples of basic speech recognition requests with each of the service's interfaces, see [Making a recognition request](/docs/services/speech-to-text-icp/basic-request.html).
-   For a high-level description of application development with the service, see [Overview for developers](/docs/services/speech-to-text-icp/developer-overview.html).

## Input features
{: #inputFeatures}

The service's interfaces share common input features for transcribing speech to text:

-   [Audio formats](/docs/services/speech-to-text-icp/audio-formats.html) - You can transcribe Ogg or Web Media (WebM) audio with the Opus or Vorbis codec, MP3 (or MPEG), Waveform Audio File Format (WAV), Free Lossless Audio Codec (FLAC), Linear 16-bit Pulse-Code Modulation (PCM), mu-law (or u-law) audio, and basic audio.
-   [Languages and models](/docs/services/speech-to-text-icp/input.html#models) - You can use transcribe audio by using broadband or narrowband models. Use broadband for audio that is sampled at a minimum rate of 16 kHz. Use narrowband for audio that is sampled at a minimum rate of 8 kHz.
-   [Audio transmission](/docs/services/speech-to-text-icp/input.html#transmission) - You can pass as much as 100 MB of audio to the service with a single request (you must send at least 100 bytes of audio). You can pass the audio as a continuous stream of data chunks or as a one-shot delivery that passes all of the data at one time. With streaming, the service enforces [timeouts](/docs/services/speech-to-text-icp/input.html#timeouts) to preserve resources.

## Output features
{: #outputFeatures}

The interfaces also support the following common output features:

-   [Speaker labels](/docs/services/speech-to-text-icp/output.html#speaker_labels) recognize different speakers from audio in US English or Japanese. The transcription labels each speaker's contributions to a multi-participant conversation. (Beta functionality.)
-   [Keyword spotting](/docs/services/speech-to-text-icp/output.html#keyword_spotting) identifies spoken phrases that match specified keyword strings with a user-defined level of confidence. Keyword spotting is especially useful when individual phrases from the audio are more important than the full transcription. For example, a customer support system might identify keywords to determine how to route user requests.
-   [Interim results](/docs/services/speech-to-text-icp/output.html#interim) return continually refined hypotheses as transcription progresses. The service returns final results when transcription is complete. Interim results are available only with the WebSocket interface.
-   [Maximum alternatives](/docs/services/speech-to-text-icp/output.html#max_alternatives) provide possible alternative transcripts. The service indicates final results in which it has the greatest confidence.
-   [Word alternatives](/docs/services/speech-to-text-icp/output.html#word_alternatives) request alternative words that are acoustically similar to the words of a transcript.
-   [Word confidence](/docs/services/speech-to-text-icp/output.html#word_confidence) returns confidence levels for each word of a transcript.
-   [Word timestamps](/docs/services/speech-to-text-icp/output.html#word_timestamps) return timestamps for the start and end of each word of a transcript.
-   [Profanity filtering](/docs/services/speech-to-text-icp/output.html#profanity_filter) censors profanity from US English transcripts.
-   [Smart formatting](/docs/services/speech-to-text-icp/output.html#smart_formatting) converts dates, times, numbers, currency values, phone numbers, and internet addresses into more readable, conventional forms in final transcripts. You can also provide keyword phrases to include certain punctuation symbols in final transcripts. The feature is supported for US English and Japanese audio. (Beta functionality.)

## Language support
{: #languages}

The service offers models for the following languages: Japanese, Korean, and US English. The service does not support all features for all languages. Moreover, it supports some features as generally available (GA) for production use and others as beta offerings for different languages.

-   The WebSocket and HTTP interfaces are generally available for all languages.
-   The service offers both broadband models and narrowband models for all languages. For more information, see [Languages and models](/docs/services/speech-to-text-icp/input.html#models).
-   Some speech recognition features are available only for some languages. For more information, see [Input features](/docs/services/speech-to-text-icp/input.html), [Output features](/docs/services/speech-to-text-icp/output.html), and the [Parameter summary](/docs/services/speech-to-text-icp/summary.html).
-   The language model customization interface is generally available for all languages. For more information, see [Language support for customization](/docs/services/speech-to-text-icp/custom.html#languageSupport).
-   The acoustic model customization interface is available as beta functionality for all languages. For more information, see [Language support for customization](/docs/services/speech-to-text-icp/custom.html#languageSupport).

## Try out the service
{: #tryOut}

For examples of {{site.data.keyword.speechtotextshort}}: Customer Care in action, see

-   A [quick demo ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://speech-to-text-demo.ng.bluemix.net/){: new_window} that transcribes text from streaming audio input or from a file that you upload.
-   Applications in the {{site.data.keyword.watson}} Developer Cloud [Starter Kits ![External link icon](../../icons/launch-glyph.svg "External link icon")](http://www.ibm.com/watson/developercloud/starter-kits.html){: new_window} that demonstrate the service.
-   The {{site.data.keyword.IBM_notm}} {{site.data.keyword.watson}} blog post [Getting robots to listen: Using {{site.data.keyword.watson}} {{site.data.keyword.speechtotextshort}} service ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/blogs/watson/2016/07/getting-robots-listen-using-watsons-speech-text-service/){: new_window} that shows how to use the service's WebSocket interface with Python to extract speech from audio. The post provides a thorough tutorial that demonstrates the code and steps involved.
