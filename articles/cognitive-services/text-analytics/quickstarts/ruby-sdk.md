---
title: "Guida introduttiva: Chiamare il servizio cognitivo Analisi del testo usando l'SDK per Ruby"
titleSuffix: Azure Cognitive Services
description: Informazioni ed esempi di codice per iniziare a usare l'API Analisi del testo in Servizi cognitivi di Azure.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 05/08/2019
ms.author: tasharm
ms.openlocfilehash: 3f18b77fe436328e79df351b9c5edcf6dc289ad7
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697261"
---
# <a name="quickstart-call-the-text-analytics-service-using-the-ruby-sdk"></a>Guida introduttiva: Chiamare il servizio Analisi del testo usando l'SDK per Ruby

<a name="HOLTop"></a>


Usare questo argomento di avvio rapido per iniziare ad analizzare il linguaggio con l'SDK di Analisi del testo per Ruby. Sebbene l'API REST [Analisi del testo](//go.microsoft.com/fwlink/?LinkID=759711) sia compatibile con la maggior parte dei linguaggi di programmazione, l'SDK rappresenta un modo semplice per integrare il servizio nelle applicazioni. Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-ruby-sdk-samples/blob/master/samples/text_analytics.rb).

Per la documentazione tecnica delle API, vedere le [definizioni delle API](//go.microsoft.com/fwlink/?LinkID=759346).

## <a name="prerequisites"></a>Prerequisiti

* [Ruby 2.5.5 o versione successiva](https://www.ruby-lang.org/)
* Text Analytics [SDK per Ruby](https://rubygems.org/gems/azure_cognitiveservices_textanalytics)
 
[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

<a name="RubyProject"></a>

## <a name="create-a-ruby-project-and-install-the-sdk"></a>Creare un progetto Ruby e installare l'SDK

1. Creare un nuovo progetto Ruby e aggiungere un nuovo file denominato `Gemfile`.
2. Aggiungere l'SDK di Analisi del testo al progetto includendo il codice seguente in `Gemfile`.

    ```ruby
    source 'https://rubygems.org'
    gem 'azure_cognitiveservices_textanalytics', '~>0.17.3'
    ```

## <a name="create-a-text-analytics-client"></a>Creare un client di Analisi del testo

1. Creare un file denominato `TextAnalyticsExamples.rb` nell'ambiente di sviluppo integrato o nell'editor preferito. Importare l'SDK di Analisi del testo.

2. Il client di Analisi del testo userà un oggetto credenziali. Crearlo con `CognitiveServicesCredentials.new()` e passare la propria chiave di sottoscrizione.

3. Creare il client con l'endpoint di Analisi del testo corretto.

    ```ruby
    require 'azure_cognitiveservices_textanalytics'
    
    include Azure::CognitiveServices::TextAnalytics::V2_1::Models
    
    credentials =
        MsRestAzure::CognitiveServicesCredentials.new("enter key here")
    # Replace 'westus' with the correct region for your Text Analytics subscription
    endpoint = String.new("https://westus.api.cognitive.microsoft.com/")
    
    textAnalyticsClient =
        Azure::TextAnalytics::Profiles::Latest::Client.new({
            credentials: credentials
        })
    textAnalyticsClient.endpoint = endpoint
    ```

<a name="SentimentAnalysis"></a>

## <a name="sentiment-analysis"></a>Analisi del sentiment

Usando l'API o l'SDK di Analisi del testo è possibile eseguire l'analisi del sentiment su un set di record di testo. L'esempio seguente visualizza i punteggi del sentiment per diversi documenti.

1. Creare una nuova funzione denominata `SentimentAnalysisExample()` che accetta come parametro il client di Analisi del testo creato in precedenza.

2. Definire un set di oggetti `MultiLanguageInput` da analizzare. Aggiungere una lingua e del testo per ogni oggetto. Come ID è possibile definire qualsiasi valore.

    ```ruby
    def SentimentAnalysisExample(client)
      # The documents to be analyzed. Add the language of the document. The ID can be any value.
      input_1 = MultiLanguageInput.new
      input_1.id = '1'
      input_1.language = 'en'
      input_1.text = 'I had the best day of my life.'
    
      input_2 = MultiLanguageInput.new
      input_2.id = '2'
      input_2.language = 'en'
      input_2.text = 'This was a waste of my time. The speaker put me to sleep.'
    
      input_3 = MultiLanguageInput.new
      input_3.id = '3'
      input_3.language = 'es'
      input_3.text = 'No tengo dinero ni nada que dar...'
    
      input_4 = MultiLanguageInput.new
      input_4.id = '4'
      input_4.language = 'it'
      input_4.text = "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."
    ```

3. All'interno della stessa funzione, combinare i documenti in un elenco. Aggiungerlo al campo `documents` di un oggetto `MultiLanguageBatchInput`. 

4. Chiamare la funzione `sentiment()` del client con l'oggetto `MultiLanguageBatchInput` come parametro per inviare i documenti. Se vengono restituiti risultati, stamparli.
    ```ruby
      input_documents =  MultiLanguageBatchInput.new
      input_documents.documents = [input_1, input_2, input_3, input_4]
    
      result = client.sentiment(
          multi_language_batch_input: input_documents
      )
      
      if (!result.nil? && !result.documents.nil? && result.documents.length > 0)
        puts '===== SENTIMENT ANALYSIS ====='
        result.documents.each do |document|
          puts "Document Id: #{document.id}: Sentiment Score: #{document.score}"
        end
      end
    end
    ```

5. Chiamare la funzione `SentimentAnalysisExample()`.

    ```ruby
    SentimentAnalysisExample(textAnalyticsClient)
    ```

### <a name="output"></a>Output

```console
===== SENTIMENT ANALYSIS =====
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

<a name="LanguageDetection"></a>

## <a name="language-detection"></a>Rilevamento della lingua

Il servizio Analisi del testo è in grado di rilevare la lingua di un documento di testo tra un numero elevato di lingue e impostazioni locali. L'esempio seguente mostra la lingua in cui sono stati scritti diversi documenti.

1. Creare una nuova funzione denominata `DetectLanguageExample()` che accetta come parametro il client di Analisi del testo creato in precedenza. 

2. Definire un set di oggetti `LanguageInput` da analizzare. Aggiungere una lingua e del testo per ogni oggetto. Come ID è possibile definire qualsiasi valore.

    ```ruby
    def DetectLanguageExample(client)
       # The documents to be analyzed.
       language_input_1 = LanguageInput.new
       language_input_1.id = '1'
       language_input_1.text = 'This is a document written in English.'
    
       language_input_2 = LanguageInput.new
       language_input_2.id = '2'
       language_input_2.text = 'Este es un document escrito en Español..'
    
       language_input_3 = LanguageInput.new
       language_input_3.id = '3'
       language_input_3.text = '这是一个用中文写的文件'
    ```

3. All'interno della stessa funzione, combinare i documenti in un elenco. Aggiungerlo al campo `documents` di un oggetto `LanguageBatchInput`. 

4. Chiamare la funzione `detect_language()` del client con l'oggetto `LanguageBatchInput` come parametro per inviare i documenti. Se vengono restituiti risultati, stamparli.
    ```ruby
       input_documents = LanguageBatchInput.new
       input_documents.documents = [language_input_1, language_input_2, language_input_3]
    
    
       result = client.detect_language(
           language_batch_input: input_documents
       )
    
       if (!result.nil? && !result.documents.nil? && result.documents.length > 0)
         puts '===== LANGUAGE DETECTION ====='
         result.documents.each do |document|
           puts "Document ID: #{document.id} , Language: #{document.detected_languages[0].name}"
         end
       else
         puts 'No results data..'
       end
     end
    ```

5. Chiamare la funzione `DetectLanguageExample`

    ```ruby
    DetectLanguageExample(textAnalyticsClient)
    ```

### <a name="output"></a>Output

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

<a name="EntityRecognition"></a>

## <a name="entity-recognition"></a>Riconoscimento delle entità

Il servizio Analisi del testo è in grado di distinguere ed estrarre diverse entità (persone, luoghi e cose) nei documenti di testo. L'esempio seguente mostra le entità rilevate in diversi documenti di esempio.

1. Creare una nuova funzione denominata `Recognize_Entities()` che accetta come parametro il client di Analisi del testo creato in precedenza.

2. Definire un set di oggetti `MultiLanguageInput` da analizzare. Aggiungere una lingua e del testo per ogni oggetto. Come ID è possibile definire qualsiasi valore.

    ```ruby
      def RecognizeEntitiesExample(client)
        # The documents to be analyzed.
        input_1 = MultiLanguageInput.new
        input_1.id = '1'
        input_1.language = 'en'
        input_1.text = 'Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800.'
    
        input_2 = MultiLanguageInput.new
        input_2.id = '2'
        input_2.language = 'es'
        input_2.text = 'La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle.'
    ```

3. All'interno della stessa funzione, combinare i documenti in un elenco. Aggiungerlo al campo `documents` di un oggetto `MultiLanguageBatchInput`. 

4. Chiamare la funzione `entities()` del client con l'oggetto `MultiLanguageBatchInput` come parametro per inviare i documenti. Se vengono restituiti risultati, stamparli.

    ```ruby
        input_documents =  MultiLanguageBatchInput.new
        input_documents.documents = [input_1, input_2]
    
        result = client.entities(
            multi_language_batch_input: input_documents
        )
    
        if (!result.nil? && !result.documents.nil? && result.documents.length > 0)
          puts '===== ENTITY RECOGNITION ====='
          result.documents.each do |document|
            puts "Document ID: #{document.id}"
              document.entities.each do |entity|
                puts "\tName: #{entity.name}, \tType: #{entity.type == nil ? "N/A": entity.type},\tSub-Type: #{entity.sub_type == nil ? "N/A": entity.sub_type}"
                entity.matches.each do |match|
                  puts "\tOffset: #{match.offset}, \Length: #{match.length},\tScore: #{match.entity_type_score}"
                end
                puts
              end
          end
        else
          puts 'No results data..'
        end
      end
    ```

5. Chiamare la funzione `RecognizeEntitiesExample`
    ```ruby
    RecognizeEntitiesExample(textAnalyticsClient)
    ```

### <a name="output"></a>Output

```console
===== ENTITY RECOGNITION =====
Document ID: 1
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 0, Length: 9,   Score: 1.0

        Name: Bill Gates,       Type: Person,   Sub-Type: N/A
        Offset: 25, Length: 10, Score: 0.999847412109375

        Name: Paul Allen,       Type: Person,   Sub-Type: N/A
        Offset: 40, Length: 10, Score: 0.9988409876823425

        Name: April 4,  Type: Other,    Sub-Type: N/A
        Offset: 54, Length: 7,  Score: 0.8

        Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
        Offset: 54, Length: 13, Score: 0.8

        Name: BASIC,    Type: Other,    Sub-Type: N/A
        Offset: 89, Length: 5,  Score: 0.8

        Name: Altair 8800,      Type: Other,    Sub-Type: N/A
        Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 21, Length: 9,  Score: 0.999755859375

        Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
        Offset: 60, Length: 7,  Score: 0.9911284446716309

        Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
        Offset: 71, Length: 13, Score: 0.8

        Name: Seattle,  Type: Location, Sub-Type: N/A
        Offset: 88, Length: 7,  Score: 0.9998779296875
```

<a name="KeyPhraseExtraction"></a>

## <a name="key-phrase-extraction"></a>Estrazione di frasi chiave

Il servizio Analisi del testo è in grado di estrarre frasi chiave all'interno di un testo. L'esempio seguente mostra le entità rilevate in diversi documenti di esempio in più lingue.

1. Creare una nuova funzione denominata `KeyPhraseExtractionExample()` che accetta come parametro il client di Analisi del testo creato in precedenza.

2. Definire un set di oggetti `MultiLanguageInput` da analizzare. Aggiungere una lingua e del testo per ogni oggetto. Come ID è possibile definire qualsiasi valore.

    ```ruby
    def KeyPhraseExtractionExample(client)
      # The documents to be analyzed.
      input_1 = MultiLanguageInput.new
      input_1.id = '1'
      input_1.language = 'ja'
      input_1.text = '猫は幸せ'
  
      input_2 = MultiLanguageInput.new
      input_2.id = '2'
      input_2.language = 'de'
      input_2.text = 'Fahrt nach Stuttgart und dann zum Hotel zu Fu.'
  
      input_3 = MultiLanguageInput.new
      input_3.id = '3'
      input_3.language = 'en'
      input_3.text = 'My cat is stiff as a rock.'
  
      input_4 = MultiLanguageInput.new
      input_4.id = '4'
      input_4.language = 'es'
      input_4.text = 'A mi me encanta el fútbol!'
      ```

3. All'interno della stessa funzione, combinare i documenti in un elenco. Aggiungerlo al campo `documents` di un oggetto `MultiLanguageBatchInput`. 

4. Chiamare la funzione `key_phrases()` del client con l'oggetto `MultiLanguageBatchInput` come parametro per inviare i documenti. Se vengono restituiti risultati, stamparli.

    ```ruby
      input_documents =  MultiLanguageBatchInput.new
      input_documents.documents = [input_1, input_2, input_3, input_4]
    
      result = client.key_phrases(
          multi_language_batch_input: input_documents
      )
    
      if (!result.nil? && !result.documents.nil? && result.documents.length > 0)
        result.documents.each do |document|
          puts "Document Id: #{document.id}"
          puts '  Key Phrases'
          document.key_phrases.each do |key_phrase|
            puts "    #{key_phrase}"
          end
        end
      else
        puts 'No results data..'
      end
    end
    ```

5. Chiamare la funzione `KeyPhraseExtractionExample`

    ```ruby
    KeyPhraseExtractionExample(textAnalyticsClient)
    ```

### <a name="output"></a>Output

```console
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                rock
Document ID: 4
         Key phrases:
                fútbol
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Analisi del testo con Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Vedere anche

 [Panoramica di Analisi del testo](../overview.md)  
 [Domande frequenti](../text-analytics-resource-faq.md)
