---

copyright:
  years: 2015, 2018
lastupdated: "2018-09-15"

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

# Managing custom words
{: #manageWords}

The customization interface includes the `POST /v1/customizations/{customization_id}/words` and `PUT /v1/customizations/{customization_id}/words/{word_name}` methods, which are used to add or modify words for a custom model. For more information, see [Add words to the custom model](/docs/services/speech-to-text-icp/language-create.html#addWords)). The interface also provides methods for listing the words of a custom language model and for deleting a word from a custom model.

## Listing words from a custom language model
{: #listWords}

The customization interface offers two methods for listing words from a custom language model:

-   The `GET /v1/customizations/{customization_id}/words` method lists information about the words from the custom model's words resource. The method includes two optional query parameters:
    -   The `word_type` parameter specifies which words are to be listed:

        -   `all` (the default) shows all words.
        -   `user` shows only custom words that were added or modified by the user.
        -   `corpora` shows only OOV words that were extracted from corpora.
    -   The `sort` parameter indicates how the words are to be ordered. The parameter accepts two arguments to indicate how the words are to be sorted: `alphabetical` and `count`. You can add an optional `+` or `-` to the front of an argument to indicate whether the results are to be sorted in ascending or descending order. By default, the method displays the words in ascending alphabetical order.
-   The `GET /v1/customizations/{customization_id}/words/{word_name}` method lists information about a single specified word from the model's words resource.

In addition to a `word` field that identifies the word, both methods return the following information about a word:

-   A `sounds_like` field that presents an array of pronunciations for the word. The array can include the sounds-like pronunciation that is automatically generated by the service if no sounds-like value is provided for the word. For more information, see [Using the sounds_like field](/docs/services/speech-to-text-icp/language-resource.html#soundsLike).
-   A `display_as` field that shows the spelling of the custom word that the service displays in transcriptions. The field contains an empty string if no display-as value is provided for the word, in which case the word is displayed as it is spelled. For more information, see [Using the display_as field](/docs/services/speech-to-text-icp/language-resource.html#displayAs).
-   A `count` field that indicates the number of times the word is found across all corpora. For example, if the word occurs five times in one corpus and seven times in another, its count is `12`. If you add a custom word to a model before it is added by any corpora, the count begins at `1`. If the word is added from a corpus first and later modified, the count reflects only the number of times it is found in corpora.

    > **Note:** For custom models that are created before the introduction of the `count` field, the field always remains at `0`. To update the field for such models, add the model's corpora again and include the `allow_overwrite` parameter with the `POST /v1/customizations/{customization_id}/corpora/{corpus_name}` method.
-   A `source` field that indicates how the word was added to the custom model's words resource. The field includes the name of each corpus from which the service extracted the word. If you modified or added the word directly, the field includes the string `user`.

If the service discovered one or more problems with a custom word's definition, the output includes an `error` field. The field provides an array that lists each problem element from the definition and a message that describes the problem. An error can occur, for example, if you add a custom word with an invalid `sounds_like` field, one that violates one of the rules for adding a pronunciation. You cannot train a model whose words resource includes a word with an error. You must correct or delete the word before you can train the model.

### Example requests and responses
{: #listExample}

The following example lists all of the words, regardless of type, from the custom model with the specified customization ID. The words are displayed in the default sort order, ascending alphabetical.

```bash
curl -X GET -u "apikey:{api_key}"
"https://{icp_cluster_host}{:port}/speech-to-text/api/v1/customizations/{customization_id}/words"
```
{: pre}

The words resource for the model contains four words. The first word was added directly by the user, but its `sounds_like` field contains an error: The field cannot contain numbers. The other words were added by the user or by both the user and from corpora.

```javascript
{
  "words": [
    {
      "word": "75.00",
      "sounds_like": ["75 dollars"],
      "display_as": "75.00",
      "count": 1,
      "source": ["user"],
      "error": [{"75 dollars": "Numbers are not allowed in sounds_like. You can try for example 'seventy five dollars'."}]
    },
    {
      "word": "HHonors",
      "sounds_like": [
        "hilton honors",
        "H. honors"
      ],
      "display_as": "HHonors",
      "count": 1,
      "source": [
        "corpus1",
        "user"
      ]
    },
    {
      "word": "IEEE",
      "sounds_like": ["I. triple E."],
      "display_as": "IEEE",
      "count": 3,
      "source": [
        "corpus1",
        "corpus2",
        "user"
      ]
    },
    {
      "word": "tomato",
      "sounds_like": [
        "tomatoh",
        "tomayto"
      ],
      "display_as": "tomato",
      "count": 1,
      "source": ["user"]
    }
  ]
}
```
{: codeblock}

The following example shows information about the word `NCAA` from the words resource of the specified model:

```bash
curl -X GET -u "apikey:{api_key}"
"https://{icp_cluster_host}{:port}/speech-to-text/api/v1/customizations/{customization_id}/words/NCAA"
```
{: pre}

The user added the word initially. The service then found the word twice in `corpus3`.

```javascript
{
  "word": "NCAA",
  "sounds_like": [
    "N. C. A. A.",
    "N. C. double A."
  ],
  "display_as": "NCAA",
  "count": 3,
  "source": [
    "corpus3",
    "user"
  ]
}
```
{: codeblock}

## Deleting a word from a custom language model
{: #deleteWord}

Use the `DELETE /v1/customizations/{customization_id}/words/{word_name}` method to delete a word from a custom language model. Use the method to remove words that were added in error, for example, from a corpus with faulty data.

You can remove any word that you added to the custom model's words resource via any means. However, you cannot delete a word from the service's base vocabulary. Deleting a word from a custom model deletes only the custom pronunciation for the word; the word remains in the base vocabulary.

Removing a word from a custom model does not affect the model until you retrain it by using the `POST /v1/customizations/{customization_id}/train` method. If the model was previously trained on the word, the model continues to apply the word to speech recognition even after you delete the word from its words resource. You must retrain the model to reflect the deletion.

### Example request
{: #deleteExample}

The following example deletes the word `IEEE` from the custom model with the specified customization ID:

```bash
curl -X DELETE -u "apikey:{api_key}"
"https://{icp_cluster_host}{:port}/speech-to-text/api/v1/customizations/{customization_id}/words/IEEE"
```
{: pre}