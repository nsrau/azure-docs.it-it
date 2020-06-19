---
title: 'Avvio rapido: Libreria client di Analisi del testo v3 per Java | Microsoft Docs'
description: Introduzione alla libreria client di Analisi del testo v3 per Java.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/17/2020
ms.author: aahi
ms.reviewer: tasharm, assafi, sumeh
ms.openlocfilehash: a4db7c59bd43a541a350d78511c6749a914bec79
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/12/2020
ms.locfileid: "84735556"
---
<a name="HOLTop"></a>

[Documentazione di riferimento](https://aka.ms/azsdk-java-textanalytics-ref-docs) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/textanalytics/azure-ai-textanalytics) | [Pacchetto](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/1.0.0) | [Esempi](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
* [Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK) versione 8 o successiva
* Dopo aver creato la sottoscrizione di Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="creare una risorsa di Analisi del testo"  target="_blank">creare una risorsa di Analisi del testo <span class="docon docon-navigate-external x-hidden-focus"></span></a> nel portale di Azure per ottenere la chiave e l'endpoint.  Al termine della distribuzione, fare clic su **Vai alla risorsa**.
    * La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione all'API Analisi del testo. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
    * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.

## <a name="setting-up"></a>Configurazione

### <a name="add-the-client-library"></a>Aggiungere la libreria client

Creare un progetto Maven nell'ambiente IDE o di sviluppo preferito. Aggiungere quindi la dipendenza seguente al file *pom.xml* del progetto. È possibile trovare la sintassi di implementazione [per altri strumenti di compilazione](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/1.0.0) online.

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>1.0.0</version>
    </dependency>
</dependencies>
```

> [!TIP]
> Si vuole visualizzare l'intero file di codice dell'argomento di avvio rapido? È possibile trovarlo [in GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/TextAnalytics/TextAnalyticsSamples.java), che contiene gli esempi di codice di questo argomento. 

Creare un file Java denominato `TextAnalyticsSamples.java`. Aprire il file e aggiungere le istruzioni `import` seguenti:

```java
import com.azure.core.credential.AzureKeyCredential;
import com.azure.ai.textanalytics.models.*;
import com.azure.ai.textanalytics.TextAnalyticsClientBuilder;
import com.azure.ai.textanalytics.TextAnalyticsClient;
```

Nel file Java aggiungere una nuova classe e quindi la chiave e l'endpoint della risorsa di Azure, come illustrato di seguito.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```java
public class TextAnalyticsSamples {
    private static String KEY = "<replace-with-your-text-analytics-key-here>";
    private static String ENDPOINT = "<replace-with-your-text-analytics-endpoint-here>";
}
```

Aggiungere il metodo main seguente alla classe. I metodi chiamati qui verranno definiti in un secondo momento.

```java
public static void main(String[] args) {
    //You will create these methods later in the quickstart.
    TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);

    sentimentAnalysisExample(client);
    detectLanguageExample(client);
    recognizeEntitiesExample(client);
    recognizeLinkedEntitiesExample(client);
    extractKeyPhrasesExample(client);
}
```

## <a name="object-model"></a>Modello a oggetti

Il client di Analisi del testo è un oggetto `TextAnalyticsClient` che esegue l'autenticazione in Azure tramite la chiave e fornisce funzioni per accettare il testo come singole stringhe o batch. È possibile inviare testo all'API in modo sincrono o asincrono. L'oggetto risposta conterrà le informazioni di analisi per ogni documento inviato. 

## <a name="code-examples"></a>Esempi di codice

* [Autenticare il client](#authenticate-the-client)
* [Analisi del sentiment](#sentiment-analysis) 
* [Rilevamento della lingua](#language-detection)
* [Riconoscimento di entità denominate](#named-entity-recognition-ner) 
* [Collegamento di entità](#entity-linking)
* [Estrazione delle frasi chiave](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autenticare il client

Creare un metodo per creare un'istanza dell'oggetto `TextAnalyticsClient` con la chiave e l'endpoint per la risorsa Analisi del testo.

```java
static TextAnalyticsClient authenticateClient(String key, String endpoint) {
    return new TextAnalyticsClientBuilder()
        .credential(new AzureKeyCredential(key))
        .endpoint(endpoint)
        .buildClient();
}
```

Nel metodo `main()` del programma chiamare il metodo di autenticazione per creare un'istanza del client.

## <a name="sentiment-analysis"></a>Analisi del sentiment

Creare una nuova funzione denominata `sentimentAnalysisExample()`, che accetta il client creato in precedenza e chiama la relativa funzione `analyzeSentiment()`. L'oggetto `AnalyzeSentimentResult` restituito conterrà `documentSentiment` e `sentenceSentiments`, se l'operazione riesce, oppure `errorMessage` in caso contrario. 

```java
static void sentimentAnalysisExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had the best day of my life. I wish you were there with me.";

    DocumentSentiment documentSentiment = client.analyzeSentiment(text);
    System.out.printf(
        "Recognized document sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
        documentSentiment.getSentiment(),
        documentSentiment.getConfidenceScores().getPositive(),
        documentSentiment.getConfidenceScores().getNeutral(),
        documentSentiment.getConfidenceScores().getNegative());

    for (SentenceSentiment sentenceSentiment : documentSentiment.getSentences()) {
        System.out.printf(
            "Recognized sentence sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
            sentenceSentiment.getSentiment(),
            sentenceSentiment.getConfidenceScores().getPositive(),
            sentenceSentiment.getConfidenceScores().getNeutral(),
            sentenceSentiment.getConfidenceScores().getNegative());
        }
    }
```

### <a name="output"></a>Output

```console
Recognized document sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: neutral, positive score: 0.21, neutral score: 0.77, negative score: 0.02.
```

## <a name="language-detection"></a>Rilevamento della lingua

Creare una nuova funzione denominata `detectLanguageExample()`, che accetta il client creato in precedenza e chiama la relativa funzione `detectLanguage()`. L'oggetto `DetectLanguageResult` restituito conterrà la lingua principale rilevata e un elenco delle altre lingue rilevate, se l'operazione riesce, oppure `errorMessage` in caso contrario.

> [!Tip]
> In alcuni casi potrebbe essere difficile distinguere le lingue in base all'input. È possibile usare il parametro `countryHint` per specificare un codice paese di 2 lettere. Per impostazione predefinita, l'API usa "US" come valore predefinito di countryHint. Per rimuovere questo comportamento, è possibile reimpostare questo parametro usando come valore una stringa vuota, `countryHint = ""`. Per impostare un valore predefinito diverso, impostare la proprietà `TextAnalyticsClientOptions.DefaultCountryHint` e passarla durante l'inizializzazione del client.

```java
static void detectLanguageExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Ce document est rédigé en Français.";

    DetectedLanguage detectedLanguage = client.detectLanguage(text);
    System.out.printf("Detected primary language: %s, ISO 6391 name: %s, score: %.2f.%n",
        detectedLanguage.getName(),
        detectedLanguage.getIso6391Name(),
        detectedLanguage.getConfidenceScore());
}
```

### <a name="output"></a>Output

```console
Detected primary language: French, ISO 6391 name: fr, score: 1.00.
```
## <a name="named-entity-recognition-ner"></a>Riconoscimento di entità denominate

> [!NOTE]
> Nella versione `3.0`:
> * Il riconoscimento di entità denominate include metodi distinti per il rilevamento di informazioni personali. 
> * Il collegamento di entità è una richiesta distinta rispetto al riconoscimento di entità denominate.

Creare una nuova funzione denominata `recognizeEntitiesExample()`, che accetta il client creato in precedenza e chiama la relativa funzione `recognizeEntities()`. L'oggetto `RecognizeEntitiesResult` restituito conterrà un elenco di `NamedEntity`, se l'operazione riesce, oppure `errorMessage` in caso contrario.

```java
static void recognizeEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had a wonderful trip to Seattle last week.";

    for (CategorizedEntity entity : client.recognizeEntities(text)) {
        System.out.printf(
            "Recognized entity: %s, entity category: %s, entity sub-category: %s, score: %s.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubcategory(),
            entity.getConfidenceScore());
    }
}
```

### <a name="output"></a>Output

```console
Recognized entity: trip, entity category: Event, entity sub-category: null, score: 0.61.
Recognized entity: Seattle, entity category: Location, entity sub-category: GPE, score: 0.82.
Recognized entity: last week, entity category: DateTime, entity sub-category: DateRange, score: 0.8.
```

## <a name="entity-linking"></a>Collegamento di entità

Creare una nuova funzione denominata `recognizeLinkedEntitiesExample()`, che accetta il client creato in precedenza e chiama la relativa funzione `recognizeLinkedEntities()`. L'oggetto `RecognizeLinkedEntitiesResult` restituito conterrà un elenco di `LinkedEntity`, se l'operazione riesce, oppure `errorMessage` in caso contrario. Poiché le entità collegate vengono identificate in modo univoco, le occorrenze della stessa entità vengono raggruppate in un oggetto `LinkedEntity` come elenco di oggetti `LinkedEntityMatch`.

```java
static void recognizeLinkedEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.";

    System.out.printf("Linked Entities:%n");
    for (LinkedEntity linkedEntity : client.recognizeLinkedEntities(text)) {
        System.out.printf("Name: %s, ID: %s, URL: %s, Data Source: %s.%n",
            linkedEntity.getName(),
            linkedEntity.getDataSourceEntityId(),
            linkedEntity.getUrl(),
            linkedEntity.getDataSource());
        System.out.printf("Matches:%n");
        for (LinkedEntityMatch linkedEntityMatch : linkedEntity.getMatches()) {
            System.out.printf("Text: %s, Score: %.2f%n",
            linkedEntityMatch.getText(),
            linkedEntityMatch.getConfidenceScore());
        }
    }
}
```

### <a name="output"></a>Output

```console
Linked Entities:
Name: Altair 8800, ID: Altair 8800, URL: https://en.wikipedia.org/wiki/Altair_8800, Data Source: Wikipedia.
Matches:
Text: Altair 8800, Score: 0.88
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
Matches:
Text: Bill Gates, Score: 0.63
Text: Gates, Score: 0.63
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
Matches:
Text: Paul Allen, Score: 0.60
Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
Matches:
Text: Microsoft, Score: 0.55
Text: Microsoft, Score: 0.55
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
Matches:
Text: April 4, Score: 0.32
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
Matches:
Text: BASIC, Score: 0.33
```
## <a name="key-phrase-extraction"></a>Estrazione di frasi chiave

Creare una nuova funzione denominata `extractKeyPhrasesExample()`, che accetta il client creato in precedenza e chiama la relativa funzione `extractKeyPhrases()`. L'oggetto `ExtractKeyPhraseResult` restituito conterrà un elenco di frasi chiave, se l'operazione riesce, oppure `errorMessage` in caso contrario.

```java
static void extractKeyPhrasesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "My cat might need to see a veterinarian.";

    System.out.printf("Recognized phrases: %n");
    for (String keyPhrase : client.extractKeyPhrases(text)) {
        System.out.printf("%s%n", keyPhrase);
    }
}
```

### <a name="output"></a>Output

```console
Recognized phrases: 
cat
veterinarian
```
