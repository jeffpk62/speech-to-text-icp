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

# The customization interface
{: #customization}

{{site.data.keyword.ibmwatson}} {{site.data.keyword.speechtotextshort}}: Customer Care offers a customization interface that you can use to augment its speech recognition capabilities. You can use customization to improve the accuracy of speech recognition requests by customizing a base model for your domain and audio. Language model customization is generally available for all languages; acoustic model customization is beta functionality for all languages. For more information, see [Language support for customization](#languageSupport).
{: shortdesc}

Speech recognition works the same either with or without a custom model. When you use a custom model for speech recognition, you can use all of the input and output parameters that are normally available with a recognition request. For more information, see [Input features](/docs/services/speech-to-text-icp/input.html), [Output features](/docs/services/speech-to-text-icp/output.html), and the [Parameter summary](/docs/services/speech-to-text-icp/summary.html).

## Language model customization
{: #customLanguage}

The service was developed with a broad, general audience in mind. The service's base vocabulary contains many words that are used in everyday conversation. The general model provides sufficiently accurate recognition for many applications. But it can lack knowledge of specific terms that are associated with particular domains.

The *language model customization* interface can improve the accuracy of speech recognition for domains such as medicine, law, information technology, and others. By using language model customization, you can expand and tailor the vocabulary of a base model to include domain-specific terminology. You create a custom language model and provide corpora and words specific to your domain. Once you train the custom language model on your enhanced vocabulary, you can use it for customized speech recognition.

-   [Creating a custom language model](/docs/services/speech-to-text-icp/language-create.html)
-   [Using a custom language model](/docs/services/speech-to-text-icp/language-use.html)

## Acoustic model customization
{: #customAcoustic}

Similarly, the service was developed with a base acoustic model that works well for various audio characteristics. But in cases like the following, adapting a base model to suit your audio can improve speech recognition:

-   Your acoustic channel environment is unique. For example, the environment is noisy, microphone quality or positioning are suboptimal, or the audio suffers from far-field effects.
-   Your speakers' speech patterns are atypical. For example, a speaker talks abnormally fast or the audio includes casual conversations.
-   Your speakers' accents are pronounced. For example, the audio includes speakers who are talking in a non-native or second language.

The *acoustic model customization* interface can adapt a base model to your environment and speakers. You create a custom acoustic model and provide example audio that closely matches the acoustic signature of the audio that you want to transcribe. Once you train the custom acoustic model with your audio resources, you can use it for customized speech recognition.

-   [Creating a custom acoustic model](/docs/services/speech-to-text-icp/acoustic-create.html)
-   [Using a custom acoustic model](/docs/services/speech-to-text-icp/acoustic-use.html)

## Using acoustic and language customization together
{: #combined}

Using a custom acoustic model alone can improve the service's recognition capabilities. But if transcriptions or related corpora are available for your example audio, you can use that data to further improve the quality of speech recognition based on the custom acoustic model.

By creating a custom language model that complements your custom acoustic model, you can enhance speech recognition by using the two models together. When you train a custom acoustic model, you can specify a custom language model that includes transcriptions of the audio resources or a vocabulary of domain-specific words from the resources. Similarly, when you transcribe audio, the service accepts a custom language model, a custom acoustic model, or both. For more information, see [Using custom acoustic and custom language models together](/docs/services/speech-to-text-icp/acoustic-both.html).

## Language support for customization
{: #languageSupport}

Language and acoustic model customization are available for all languages. The following table documents the level at which the service supports customization for each language.

-   *GA* indicates that the interface is generally available for production use.
-   *Beta* indicates that the interface is available as a beta offering.
<!--
-   *Not supported* means that the interface is not available for that language.-->

You can use both broadband and narrowband models with any supported language.

<table>
  <caption>Table 1. Language support for customization</caption>
  <tr>
    <th style="text-align:left; vertical-align:bottom; width 24%">
      Language
    </th>
    <th style="text-align:center; vertical-align:bottom; width 37%">
      Support for language model customization
    </th>
    <th style="text-align:center; vertical-align:bottom; width 37%">
      Support for acoustic model customization
    </th>
  </tr>
  <tr>
    <td>Japanese</td>
    <td style="text-align:center">GA</td>
    <td style="text-align:center">Beta</td>
  </tr>
  <tr>
    <td>Korean</td>
    <td style="text-align:center">GA</td>
    <td style="text-align:center">Beta</td>
  </tr>
  <tr>
    <td>US English</td>
    <td style="text-align:center">GA</td>
    <td style="text-align:center">Beta</td>
  </tr>
</table>

You can also use the `GET /v1/models` and `GET /v1/models/{model_id}` methods to check whether a language model supports language model customization. If the base model supports language model customization, the `supported_features` field of the methods' output for the model sets the `custom_language_model` field to `true`.

## Ownership of custom models
{: #customOwner}

{{site.data.keyword.speechtotextshort}}: Customer Care is currently a single-tenant solution. Each cluster has only a single instance of the service, and that instance has only one ID and one API key. You use that API key for all requests to the service.

Because the service uses a single common API key for all authenticated requests, all custom language and custom acoustic models are owned by the same service instance. You must use the API key for your service instance to make a request to create, manage, or use a custom model or its resources.

## Information security
{: #customSecurity}

You can associate a customer ID with data that is added or updated for custom language and custom acoustic models. You associate a customer ID with corpora, custom words, and audio resources by passing the `X-Watson-Metadata` header with the following methods:

-   `POST /v1/customizations/{customization_id}/corpora/{corpus_name}`
-   `POST /v1/customizations/{customization_id}/words`
-   `PUT /v1/customizations/{customization_id}/words/{word_name}`
-   `POST /v1/acoustic_customizations/{customization_id}/audio/{audio_name}`

If necessary, you can then delete the data by using the `DELETE /v1/user_data` method. For more information, see [Information security](/docs/services/speech-to-text-icp/information-security.html).
