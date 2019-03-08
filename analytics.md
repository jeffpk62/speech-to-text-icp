---

copyright:
  years: 2019
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

# Speech analytics
{: #analytics}

The batch-processing HTTP interface of {{site.data.keyword.ibmwatson}} {{site.data.keyword.speechtotextshort}}: Customer Care supports speech analytics, which provide deeper insight into the conversations and speakers from the processed audio files. The service further analyzes its speech recognition results to produce detailed information about the topics of overall conversation and the tone, sentiment, keywords, and topics of individual speakers.
{: shortdesc}

To produce speech analytics, the {{site.data.keyword.speechtotextshort}}: Customer Care service joins its own additional analysis with results from other {{site.data.keyword.watson}} services to produce a detailed picture of the audio content:

-   *Topic analysis* identifies discussion topics for both the overall conversation and individual speakers. The service generates the topics itself.
-   *Tone analysis* identifies emotional and language tones for individual speakers. The service bases its results on the analysis of the customer-engagement endpoint of the {{site.data.keyword.toneanalyzerfull}} service.
-   *Sentiment analysis* indicates whether the sentiment of individual speakers is negative, neutral, or positive. The service bases its results on the analysis of the {{site.data.keyword.nlufull}} service.
-   *Keyword analysis* identifies significant keywords for individual speakers, including the number of times each keyword appears and its relevance to the conversation. The service again bases its results on the analysis of the {{site.data.keyword.nlushort}} service.

For more information about these additional {{site.data.keyword.watson}} services, see the following documentation:

-   [About {{site.data.keyword.toneanalyzershort}} ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://{DomainName}/docs/services/tone-analyzer/index.html#about){: new_window}
-   [About {{site.data.keyword.nlushort}} ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://{DomainName}/docs/services/natural-language-understanding/index.html#about){: new_window}

Batch processing and speech analytics consume extra processing resources. Consider deploying more Virtual Processor Cores (VPCs) if you use these features. For more information, see [Installing and configuring the service](/docs/services/speech-to-text-icp/install-config.html). Batch processing and speech analytics are supported only for US English audio at this time.
{: important}

## How batch-processing with speech analytics works
{: #analyticsWorkflow}

From an interface perspective, batch-processing works the same with or without speech analytics. The service scans and processes each audio file in the input bucket. It populates the output bucket with at least one new **.json** or **.notice** file for each input file. For more information, see [How batch-processing works](/docs/services/speech-to-text-icp/batch.html#batchWorkflow).

You enable speech analytics by setting the `speech_analytics` query parameter of the `POST /v1/batches` method to `true`. This causes the service to perform additional processing of the initial speech recognition results:

-   The service scans and updates the **.json** file for each input audio file. It inserts speech analytics results for the complete transcript of the conversation from the input file and for each individual speaker turn from the conversation. It updates each **.json** file in place in the directory of the output bucket in which it exists. For more information, see [Additions to JSON results](#analyticsJSONResults).
-   The service produces two additional output files: **Conversation.zip** and **Speaker.zip**. These files contain cumulative conversation- and speaker-level results derived from the **.json** files for all input audio files. The service writes the **.zip** files to the root directory of the output bucket. For more information, see [Conversation results](#analyticsConversationResults) and [Speaker results](#analyticsSpeakerResults).

Otherwise, all descriptions and methods of the batch-processing interface apply just as they do without speech analytics. You can use the API to initiate, check the status of, cancel, or delete a batch-processing request. You can use the Admin Console GUI to check the status of all requests. For more information, see the batch-processing [Usage model](/docs/services/speech-to-text-icp/batch.html#batchUsage).

## Additions to JSON results
{: #analyticsJSONResults}

When the service processes a **.json** file for speech analytics, it embeds its additional results as two additional fields in the top-level `SpeechRecognitionResults` object:

-   `conversation_speech_analytics` provides conversation-level analytics that apply to the entire audio file. For more information, see [Conversation speech analytics](#analyticsJSONResultsConversation).
-   `speaker_turn_speech_analytics` provides speaker-level analytics that apply to individual speaker turns from the conversation. For more information, see [Speaker turn speech analytics](#analyticsJSONResultsSpeaker).

### Conversation speech analytics
{: #analyticsJSONResultsConversation}

Conversation speech analytics report information about the overall conversation from the audio file that generated the **.json** file. The `conversation_speech_analytics` field includes results that describe the topics that were discussed at the conversation level based on the complete transcript of the audio. The field contains a single object with one field, `topics`, which provides an array of topics derived from the conversation.

```javascript
"conversation_speech_analytics": {
  "topics": [
    {
      "cluster_id": "string",
      "model_id": "string",
      "topic_name": "string"
    }
  ]
}
```
{: codeblock}

Each element of the array includes the following fields with information about a different topic:

-   `cluster_id` is a string that uniquely identifies the cluster in the topic model for the results. It is meaningful for audio files that produce many topics. An example of a possible cluster ID is `"0"`.
-   `model_id` is a string that uniquely identifies the topic model for the results. It is again meaningful only for audio files that produce many topics. An examples of a possible model ID is `"1"`.
-   `topic_name` is a string that uniquely identifies the topic for the results. An example of a possible topic name is `"home_owner_email"`.

If the service lacks sufficient audio to identify any topics for the conversation, the fields contain the string `"unknown"`.

### Speaker turn speech analytics
{: #analyticsJSONResultsSpeaker}

Speaker turn speech analytics report information about the individual speakers from the audio file that generated the **.json** file. The `speaker_turn_speech_analytics` field includes an array of objects that further decompose the conversation-level analytics described in the previous section to unmask the tone, sentiment, keywords, and topics of individual speaker turns from the conversation. A turn is a continuous block of audio that is spoken by a single speaker.

```javascript
"speaker_turn_speech_analytics": [
  {
    "transcript": "string",
    "from": float,
    "to": float,
    "speaker": integer,
    "speaker_turn_id": "string",
    "speech_analytics": {
      "tone": { . . .},
      "sentiment:" { . . . },
      "keywords" [ . . . ],
      "topics": [ . . . ]
  },
  . . .
]
```
{: codeblock}

Each element of the array is an object that has the following six fields:

-   `transcript` is a string that includes one complete speaker turn from the conversation. A speaker turn is also referred to as an utterance.
-   `from` is a float that indicates the start time of the first word from the conversation that is included in the transcript for this speaker turn.
-   `to` is a float that indicates the end time of the last word from the conversation that is included in the transcript for this speaker turn.
-   `speaker` is an integer that uniquely identifies the speaker from the overall conversation. Speaker IDs are not guaranteed to be sequential, contiguous, or ordered.
-   `speaker_turn_id` is a string that uniquely identifies the speaker's turn in the conversation. The ID is guaranteed to be unique among all speaker turn IDs for the same batch-processing request.
-   `speech_analytics` is an object that has four fields that contain the detailed speaker-level analytics: `tone`, `sentiment`, `keywords`, and `topics`. The following sections describe these fields.

#### The tone field
{: #analyticsJSONResultsTone}

The `tone` field contains an object that has four fields:

-   `utterance_text` is a string that contains an abbreviated copy of the transcript for this speaker turn.
-   `utterance_id` is an integer that provides a unique identifier for this utterance.
-   `tones` is an array of objects that provides results for the most prevalent tones detected in the utterance. The array includes results for any tone whose score is at least `0.5`. The array is empty if no tone has a score that meets this threshold.
-   `error` is an optional string that includes a message if the utterance contains more than 500 characters. In this case, the utterance is not analyzed.

If the array in the `tones` field is not empty, each element of the array is an object that contains the following three fields:

-   `score` is a double the provides the score for the tone in the range of `0.5` to `1.0`. A score greater than `0.75` indicates a high likelihood that the tone is perceived in the utterance. The service returns results only for tones whose scores meet a minimum threshold of `0.5`.
-   `tone_id` is a string that provides a unique, non-localized identifier for the tone: `excited`, `frustrated`, `impolite`, `polite`, `sad`, `satisfied`, or `sympathetic`.
-   `tone_name` is a string that provides a user-visible, localized name for the tone.

The contents of these objects are the same as those of the `UtteranceAnalysis` object of the {{site.data.keyword.toneanalyzershort}} service. For more information, see the [{{site.data.keyword.toneanalyzershort}} API reference ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://{DomainName}/apidocs/tone-analyzer){: new_window}.

#### The sentiment field
{: #analyticsJSONResultsSentiment}

The `sentiment` field contains an object that has two fields:

-   `score` is a double that indicates the sentiment of the transcript in the range of `-1` (negative) to `1` (positive), with `0` indicating a neutral sentiment.
-   `label` is a string that identifies the sentiment as `negative`, `neutral`, or `positive`.

The contents of this object are similar to those of the `DocumentSentimentResults` object of the {{site.data.keyword.nlushort}} service. For more information, see the [{{site.data.keyword.nlushort}} API reference ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://{DomainName}/apidocs/natural-language-understanding){: new_window}.

#### The keywords field
{: #analyticsJSONResultsKeywords}

The `keywords` field contains an array of significant keywords that were extracted from the transcript. Each element of the array includes the following fields with information about a different keyword:

-   `text` is a string that contains the keyword, which can include one or more tokens.
-   `relevance` is a double that indicates how relevant the keyword is to the transcript. The score ranges from `0.0` (not relevant) to `1.0` (very relevant).
-   `count` is an integer that indicates how many times the keyword appears in the transcript.

The contents of these objects are similar to those of the `KeywordsResults` object of the {{site.data.keyword.nlushort}} service. For more information, see the [{{site.data.keyword.nlushort}} API reference ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://{DomainName}/apidocs/natural-language-understanding){: new_window}.

#### The topics field
{: #analyticsJSONResultsTopics}

The `topics` field contains an array of topics that were derived from the transcript. Each element of the array includes the following fields with information about a different topic:

-   `cluster_id` is a string that uniquely identifies the cluster in the topic model for the results.
-   `model_id` is a string that uniquely identifies the topic model for the results.
-   `topic_name` is a string that uniquely identifies the topic for the results.

These speaker-level fields have the same meaning as the corresponding conversation-level fields. If the service lacks sufficient audio to identify any topics for the transcript, the fields contain the string `"unknown"`.

### Example additions to output file
{: #analyticsJSONExample}

The following example is identical to the example in the previous chapter ([Example output file](/docs/services/speech-to-text-icp/batch.html#batchResultsExample)). It submits the same simple brief file, <a target="_blank" href="https://watson-developer-cloud.github.io/doc-tutorial-downloads/speech-to-text/audio-file.flac" download="audio-file.flac">audio-file.flac <img src="../../icons/launch-glyph.svg" alt="External link icon" title="External link icon"></a>, and uses the same COS bucket for both input and output. However, this example sets the `speech_analytics` query parameter to `true`.

```bash
curl -X POST -u "apikey:{apikey}"
--header "Content-Type: multipart/form-data"
--form input_credentials_file=@{path}my_cos_credentials.json
--form input_bucket_location=us-geo
--form input_bucket_name=my_cos_bucket
"https://{icp_cluster_host}{:port}/speech-to-text/api/v1/batches?function=recognize&speech_analytics=true"
```
{: pre}

The service updates the output file, **audio-file.flac.json**, with the following additional analytics results:

-   The {{site.data.keyword.speechtotextshort}}: Customer Care service cannot obtain meaningful `topics` from such brief audio. It labels both the conversation- and speaker-level topics `unknown`.
-   The {{site.data.keyword.toneanalyzershort}} service, similarly, cannot identify any meaningful `tones` from such a simple utterance.
-   The {{site.data.keyword.nlushort}} service, however, is able to indicate that the `sentiment` of the utterance is `neutral`. The service is also able to identify two strings as `keywords` with a high degree of confidence.

{{site.data.keyword.speechtotextshort}}: Customer Care inserts the speech analytics results into the speech recognition results where shown.

```javascript
{
  "result_index": 0,
  "speaker_labels": [
    . . .
    {
      "speaker": 0,
      "confidence": 0.5,
      "final": true,
      "from": 5.73,
      "to": 6.35
    }
  ],
  "conversation_speech_analytics": {
    "topics": [
      {
        "cluster_id": "unknown",
        "topic_name": "unknown",
        "model_id": "unknown"
      }
    ]
  },
  "speaker_turn_speech_analytics": [
    {
      "transcript": "several tornadoes touch down as a line of
severe thunderstorms swept through Colorado one Sunday",
      "speech_analytics": {
        "sentiment": {
          "score": 0,
          "label": "neutral"
        },
        "tone": {
          "tones": [],
          "utterance_text": "several tornadoes touch down as a line
of severe ",
          "utterance_id": 0
        },
        "keywords": [
          {
            "count": 1,
            "text": "line of severe thunderstorms",
            "relevance": 0.933034
          },
          {
            "count": 1,
            "text": "tornadoes",
            "relevance": 0.918196
          }
        ],
        "topics": [
          {
            "cluster_id": "unknown",
            "topic_name": "unknown",
            "model_id": "unknown"
          }
        ]
      },
      "speaker": 0,
      "from": 1.01,
      "speaker_turn_id": 0,
      "to": 6.35
    }
  ],
  "results": [
    {
      "final": true,
      "alternatives": [
        {
          "transcript": "several tornadoes touch down as a line of
severe thunderstorms swept through Colorado one Sunday ",
          "timestamps": [
            [
              "several",
              1.01,
              1.52,
              {
                "speaker": 0,
                "confidence": 0.5,
                "final": false,
                "from": 1.01,
                "to": 1.52
              }
            ],
            . . .
          ],
          "confidence": 0.933
        }
      ]
    }
  ]
}
```
{: codeblock}

## Conversation results
{: #analyticsConversationResults}

The **Conversation.zip** file contains information about the overall conversation from all of the input audio files for the request. Table 1 describes the files that the service includes in the top level of the **Conversation.zip** file. For each JSON file, the table includes the schemas that the service uses to define the contents of the file.

The files and their schemas are subject to change in a future release.
{: note}

<table>
  <caption>Table 1. Files in the Conversation.zip file</caption>
  <tr>
    <th style="text-align:left">File name</th>
    <th style="text-align:left">Description</th>
  </tr>
  <tr>
    <td style="vertical-align:top">
      **conversation_data.json**
    </td>
    <td>
      Input data for topic modeling. The data is converted from
      {{site.data.keyword.speechtotextshort}} and
      {{site.data.keyword.nlushort}} results. Each line of the file
      is a separate JSON object. This format accommodates topic-modeling
      analysis. The following JSON file describes the schemas that the file
      uses:
      <a target="_blank" href="https://watson-developer-cloud.github.io/doc-tutorial-downloads/speech-to-text/speech-analytics/conversation_data_schema.json" download="conversation_data_schema.json">conversation_data_schema.json <img src="../../icons/launch-glyph.svg" alt="External link icon" title="External link icon"></a>
    </td>
  </tr>
  <tr>
    <td style="vertical-align:top">
      **conversation_data.json.summary_model.json**
    </td>
    <td>
      Intermediate data for topic-modeling analysis. The data is
      generated from the file **conversation_data.json**. The following
      JSON file describes the schemas that the file uses:
      <a target="_blank" href="https://watson-developer-cloud.github.io/doc-tutorial-downloads/speech-to-text/speech-analytics/conversation_data_summary_model_schema.json" download="conversation_data_summary_model_schema.json">conversation_data_summary_model_schema.json <img src="../../icons/launch-glyph.svg" alt="External link icon" title="External link icon"></a>
  </tr>
  <tr>
    <td style="vertical-align:top">
      **conversation_aggregated_insights.json**
    </td>
    <td>
      Aggregated insights for the overall conversation. The data includes
      the distribution of overall sentiments and tones. It also includes
      the distribution of keywords, sentiments, and tones for each topic.
      The following JSON file describes the schemas that the file uses:
      <a target="_blank" href="https://watson-developer-cloud.github.io/doc-tutorial-downloads/speech-to-text/speech-analytics/conversation_aggregated_insights_schema.json" download="conversation_aggregated_insights_schema.json">conversation_aggregated_insights_schema.json <img src="../../icons/launch-glyph.svg" alt="External link icon" title="External link icon"></a>
    </td>
  </tr>
  <tr>
    <td style="vertical-align:top">
      **conversation_transcripts.json**
    </td>
    <td>
      Insights for each conversation. The data includes the aggregated
      tone, sentiment, and topic for the conversation. The following JSON
      file describes the schemas that the file uses:
      <a target="_blank" href="https://watson-developer-cloud.github.io/doc-tutorial-downloads/speech-to-text/speech-analytics/conversation_transcripts_schema.json" download="conversation_transcripts_schema.json">conversation_transcripts_schema.json <img src="../../icons/launch-glyph.svg" alt="External link icon" title="External link icon"></a>
    </td>
  </tr>
</table>

Table 2 describes the directories that the service includes in the **Conversation.zip** file. These directories contain files and subdirectories that contain detailed topic-modeling results.

<table>
  <caption>Table 2. Directories in the Conversation.zip file</caption>
  <tr>
    <th style="text-align:left">Directory name</th>
    <th style="text-align:left">Description</th>
  </tr>
  <tr>
    <td style="vertical-align:top">
      **conversation_data.json.topic_model**
    </td>
    <td>
      Hierarchical topic-modeling results.
    </td>
  </tr>
  <tr>
    <td style="vertical-align:top">
      **conversation_data.json.topic_predict**
    </td>
    <td>
      Topic assignment for each conversation.
    </td>
  </tr>

  <tr>
    <td style="vertical-align:top">
      **conversation_data.json.topic_word_mapping_sentences**
    </td>
    <td>
     Key topic word extraction for conversation sentences.
    </td>
  </tr>

  <tr>
    <td style="vertical-align:top">
      **conversation_data.json.topic_ngram_mapping_sentences**
    </td>
    <td>
      Key n-gram extraction for conversation sentences.
    </td>
  </tr>
</table>

## Speaker results
{: #analyticsSpeakerResults}

The **Speaker.zip** file contains information about the individual speakers from the overall conversation from all of the input audio files for the request. The information is similar to the conversation results. But the speaker results provide CSV input data for topic modeling.

Table 3 describes the files that the service includes in the **Speaker.zip** file. For each JSON file, the table includes the schemas that the service uses to define the contents of the file.

The files and their schemas are subject to change in a future release.
{: note}

<table>
  <caption>Table 3. Files in the Speaker.zip file</caption>
  <tr>
    <th style="text-align:left">File name</th>
    <th style="text-align:left">Description</th>
  </tr>
  <tr>
    <td style="vertical-align:top">
      **utterance_data.csv**
    </td>
    <td>
      Input data for topic modeling. The data is converted from
      {{site.data.keyword.speechtotextshort}} and
      {{site.data.keyword.nlushort}} results. The file includes
      the utterance ID, the sentiment in JSON format, the tone in
      JSON format, and the keywords in JSON format. The information
      is used for insight aggregation.
    </td>
  </tr>
  <tr>
    <td style="vertical-align:top">
      **utterance_data.csv.summary_model.json**
    </td>
    <td>
      Intermediate data for topic modeling. The data is generated from
      the file **utterance_data.csv**. The information is used for
      topic-modeling analysis. The following JSON file describes the
      schemas that the file uses:
      <a target="_blank" href="https://watson-developer-cloud.github.io/doc-tutorial-downloads/speech-to-text/speech-analytics/utterance_data.csv.summary_model_schema.json" download="utterance_data.csv.summary_model_schema.json">utterance_data.csv.summary_model_schema.json <img src="../../icons/launch-glyph.svg" alt="External link icon" title="External link icon"></a>
    </td>
  </tr>
  <tr>
    <td style="vertical-align:top">
      **utterance_aggregated_insights.json**
    </td>
    <td>
      Aggregated insights for utterances. The data includes the
      distribution of overall sentiments and tones. It also includes
      the distribution of keywords, sentiments, and tones for each topic.
      The following JSON file describes the schemas that the file uses:
      <a target="_blank" href="https://watson-developer-cloud.github.io/doc-tutorial-downloads/speech-to-text/speech-analytics/utterance_aggregated_insights_schema.json" download="utterance_aggregated_insights_schema.json">utterance_aggregated_insights_schema.json <img src="../../icons/launch-glyph.svg" alt="External link icon" title="External link icon"></a>
    </td>
  </tr>
  <tr>
    <td style="vertical-align:top">
      **utterance_transcripts.json**
    </td>
    <td>
      Insights for each utterance. The data includes the tone, sentiment,
      and topic name. The following JSON file describes the schemas that
      the file uses:
      <a target="_blank" href="https://watson-developer-cloud.github.io/doc-tutorial-downloads/speech-to-text/speech_analytics/utterance_transcripts_schema.json" download="utterance_transcripts_schema.json">utterance_transcripts_schema.json <img src="../../icons/launch-glyph.svg" alt="External link icon" title="External link icon"></a>
    </td>
  </tr>
</table>

Table 4 describes the directories that the service includes in the **Speaker.zip** file. These directories contain files and subdirectories that contain detailed topic-modeling results.

<table>
  <caption>Table 4. Directories in the Speaker.zip file</caption>
  <tr>
    <th style="text-align:left">Directory name</th>
    <th style="text-align:left">Description</th>
  </tr>
  <tr>
    <td style="vertical-align:top">
      **utterance_data.csv.topic_model**
    </td>
    <td>
      Hierarchical topic-modeling results.
    </td>
  </tr>
  <tr>
    <td style="vertical-align:top">
      **utterance_data.csv.topic_predict**
    </td>
    <td>
      Topic assignment for each utterance.
    </td>
  </tr>
  <tr>
    <td style="vertical-align:top">
      **utterance_data.csv.topic_word_mapping_sentences**
    </td>
    <td>
      Key topic word extraction for each utterance.
    </td>
  </tr>
  <tr>
    <td style="vertical-align:top">
      **utterance_data.csv.topic_ngram_mapping_sentences**
    </td>
    <td>
      Key n-gram extraction for each utterance.
    </td>
  </tr>
</table>
