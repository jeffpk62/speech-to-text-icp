---

Copyright:
  years: 2018, 2019
lastupdated: "2019-05-12"

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

# About
{: #about}

**Service update:** *The {{site.data.keyword.speechtotextshort}}: Customer Care service was updated on January 18, 2019. The service now offers multiple new features: a batch-processing HTTP interface that enables speech analytics for US English audio, beta grammar support with language model customization for all languages, support for French and Spanish broadband and narrowband models, and a short-form narrowband model for US English. It also offers a new parameter that redacts numbers with three or more digits from US English, Japanese, and Korean transcripts. For more information about these and all updates, see [Version 1.1.0 (18 January 2019)](/docs/services/speech-to-text-icp?topic=speech-to-text-icp-release-notes#v110) in the release notes.*

{{site.data.keyword.ibmwatson}} {{site.data.keyword.speechtotextshort}}: Customer Care provides speech recognition capabilities for your {{site.data.keyword.cloud}} Private solutions. The service leverages machine learning to combine knowledge of grammar, language structure, and the composition of audio and voice signals to accurately transcribe the human voice. It continuously updates and refines its transcription as it receives more speech.
{: shortdesc}

{{site.data.keyword.speechtotextshort}}: Customer Care is ideal for clients who need to extract high-quality speech transcripts from call center audio. Clients in industries such as financial services, healthcare, insurance, and telecommunication often need to develop cloud-native applications but are regulated or limited in their adoption of public cloud offerings. The service allows such clients to build on-premises applications for customer care, customer voice, agent assistance, and other solutions.

For information about installing and configuring {{site.data.keyword.speechtotextshort}}: Customer Care on {{site.data.keyword.icpfull}}, see [Installing and configuring the service](/docs/services/speech-to-text-icp?topic=speech-to-text-icp-install).

{{site.data.keyword.speechtotextshort}}: Customer Care is an on-premises version of the public {{site.data.keyword.speechtotextfull}} service. For information about the {{site.data.keyword.speechtotextshort}} service on the public {{site.data.keyword.cloud_notm}}, see [About {{site.data.keyword.speechtotextshort}} ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://{DomainName}/docs/services/speech-to-text?topic=speech-to-text-about#about){: new_window}.
{: note}

## Supported interfaces
{: #interfaces}

The {{site.data.keyword.speechtotextshort}}: Customer Care service offers multiple interfaces for speech recognition:

-   A [WebSocket interface](/docs/services/speech-to-text-icp?topic=speech-to-text-icp-websockets) for establishing persistent, full-duplex, low-latency connections with the service. You can pass a maximum of 100 MB of audio data to the service with a single request.
-   A [synchronous HTTP interface](/docs/services/speech-to-text-icp?topic=speech-to-text-icp-http) for basic HTTP calls to the service. You can pass a maximum .of 100 MB of audio data with a request.
-   An [asynchronous HTTP interface](/docs/services/speech-to-text-icp?topic=speech-to-text-icp-async) for non-blocking calls to the service. You can pass as much as 1 GB of audio data with a request.
-   A [batch-processing HTTP interface](/docs/services/speech-to-text-icp?topic=speech-to-text-icp-batch) for submitting multiple audio files to the service for speech recognition and speech analytics. You can pass a maximum of 100 MB of audio data with any file of a request. The service does not limit the cumulative size of all audio files or the number of files that you can submit.

The service also provides a [customization interface](/docs/services/speech-to-text-icp?topic=speech-to-text-icp-customization) that you can use to tune speech recognition for your language and acoustic requirements. You can expand the vocabulary of a model with domain-specific terminology or adapt a model for the acoustic characteristics of your audio. You can also add [grammars](/docs/services/speech-to-text-icp?topic=speech-to-text-icp-grammars) to restrict the phrases that the service can recognize.

-   For information about making requests with each of the {{site.data.keyword.speechtotextshort}}: Customer Care interfaces, see [Making requests to the service](/docs/services/speech-to-text-icp?topic=speech-to-text-icp-making-requests).
-   For examples of basic speech recognition requests with each of the service's interfaces, see [Making a recognition request](/docs/services/speech-to-text-icp?topic=speech-to-text-icp-basic-request).
-   For a high-level description of application development with the service, see [Overview for developers](/docs/services/speech-to-text-icp?topic=speech-to-text-icp-developerOverview).

SDKs are available in many programming languages to simplify your use of the service. For more information, see the [API reference ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://{DomainName}/apidocs/speech-to-text-icp){: new_window}.

## Input features
{: #inputFeatures}

The service's interfaces share many common input features for transcribing speech to text:

-   [Audio formats](/docs/services/speech-to-text-icp?topic=speech-to-text-icp-audio-formats) - You can transcribe Ogg or Web Media (WebM) audio with the Opus or Vorbis codec, MP3 (or MPEG), Waveform Audio File Format (WAV), Free Lossless Audio Codec (FLAC), Linear 16-bit Pulse-Code Modulation (PCM), G.729, mu-law (or u-law), and basic audio. By using a format that supports compression, you can maximize the amount of audio data that you can send with a request.
-   [Languages and models](/docs/services/speech-to-text-icp?topic=speech-to-text-icp-models) - You can transcribe audio by using broadband or narrowband models. Use broadband for audio that is sampled at a minimum rate of 16 kHz. Use narrowband for audio that is sampled at a minimum rate of 8 kHz.
-   [Audio transmission](/docs/services/speech-to-text-icp?topic=speech-to-text-icp-input#transmission) - You can pass audio as a continuous stream of data chunks or as a one-shot delivery that passes all of the data at one time. With streaming, the service enforces inactivity and session [timeouts](/docs/services/speech-to-text-icp?topic=speech-to-text-icp-input#timeouts).

## Output features
{: #outputFeatures}

Most interfaces also support the following common output features:

-   [Speech analytics](/docs/services/speech-to-text-icp?topic=speech-to-text-icp-analytics) provides information about conversations and speakers from multi-participant exchanges. The service further analyzes its speech recognition results to produce detailed information about the topics of overall conversation and the tone, sentiment, keywords, and topics of individual speakers. The feature is supported for US English only by the batch-processing interface.
-   [Speaker labels](/docs/services/speech-to-text-icp?topic=speech-to-text-icp-output#speaker_labels) recognize different speakers from US English, Japanese, or Spanish audio. The transcription labels each speaker's contributions to a multi-participant conversation. (Beta functionality.)
-   [Keyword spotting](/docs/services/speech-to-text-icp?topic=speech-to-text-icp-output#keyword_spotting) identifies spoken phrases that match specified keyword strings with a user-defined level of confidence. Keyword spotting is especially useful when individual phrases from the audio are more important than the full transcription. For example, a customer support system might identify keywords to determine how to route user requests.
-   [Interim results](/docs/services/speech-to-text-icp?topic=speech-to-text-icp-output#interim) return continually refined hypotheses as transcription progresses. The service returns final results when transcription is complete. Interim results are available only with the WebSocket interface.
-   [Maximum alternatives](/docs/services/speech-to-text-icp?topic=speech-to-text-icp-output#max_alternatives) provide possible alternative transcripts. The service indicates final results in which it has the greatest confidence.
-   [Word alternatives](/docs/services/speech-to-text-icp?topic=speech-to-text-icp-output#word_alternatives) request alternative words that are acoustically similar to the words of a transcript.
-   [Word confidence](/docs/services/speech-to-text-icp?topic=speech-to-text-icp-output#word_confidence) returns confidence levels for each word of a transcript.
-   [Word timestamps](/docs/services/speech-to-text-icp?topic=speech-to-text-icp-output#word_timestamps) return timestamps for the start and end of each word of a transcript.
-   [Smart formatting](/docs/services/speech-to-text-icp?topic=speech-to-text-icp-output#smart_formatting) converts dates, times, numbers, currency values, phone numbers, and internet addresses into more readable, conventional forms in final transcripts. For US English, you can also provide keyword phrases to include certain punctuation symbols in final transcripts. Smart formatting is supported for US English, Japanese, and Spanish audio. (Beta functionality.)
-   [Numeric redaction](/docs/services/speech-to-text-icp?topic=speech-to-text-icp-output#redaction) redacts, or masks, numeric data from a final transcript. Redaction is intended to remove sensitive personal information, such as credit card numbers, from transcripts. The feature is supported for US English, Japanese, and Korean audio. (Beta functionality.)
-   [Profanity filtering](/docs/services/speech-to-text-icp?topic=speech-to-text-icp-output#profanity_filter) censors profanity from US English transcripts.

## Language support
{: #languages}

The service offers models for the following languages: French, Japanese, Korean, Spanish, and US English. The service does not support all features for all languages. Moreover, it supports some features as generally available (GA) for production use and others as beta offerings for different languages.

-   The WebSocket and synchronous and asynchronous HTTP interfaces are generally available for all languages. The batch-processing HTTP interface is available for US English.
-   The service offers both broadband models and narrowband models for all languages. For more information, see [Languages and models](/docs/services/speech-to-text-icp?topic=speech-to-text-icp-models).
-   Some speech recognition features are available only for some languages and only with some interfaces. For more information, see the [Parameter summary](/docs/services/speech-to-text-icp?topic=speech-to-text-icp-summary).
-   The language model customization interface is generally available for all languages. The acoustic model customization interface is available as beta functionality for all languages. For more information, see [Language support for customization](/docs/services/speech-to-text-icp?topic=speech-to-text-icp-customization#languageSupport).

## Try out the service
{: #tryOut}

For examples of {{site.data.keyword.speechtotextshort}}: Customer Care in action, see

-   A [quick demo ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://speech-to-text-demo.ng.bluemix.net/){: new_window} that transcribes text from streaming audio input or from a file that you upload.
-   Applications in the {{site.data.keyword.ibmwatson}} [Starter Kits ![External link icon](../../icons/launch-glyph.svg "External link icon")](http://www.ibm.com/watson/developercloud/starter-kits.html){: new_window} that demonstrate the service.
-   The {{site.data.keyword.watson}} blog post [Getting robots to listen: Using {{site.data.keyword.watson}}'s {{site.data.keyword.speechtotextshort}} service ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/blogs/watson/2016/07/getting-robots-listen-using-watsons-speech-text-service/){: new_window} that shows how to use the service's WebSocket interface with Python to extract speech from audio. The post provides a thorough tutorial that demonstrates the code and steps involved.
