---
title: 'Avvio rapido: Libreria client di Analisi del testo v3 per Java | Microsoft Docs'
description: Introduzione alla libreria client di Analisi del testo v3 per Java.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 09/21/2020
ms.custom: devx-track-java
ms.author: aahi
ms.reviewer: tasharm, assafi, sumeh
ms.openlocfilehash: 2788b22d2b262cfef4b214d75813a203067462cb
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779506"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[Versione 3.1-preview](#tab/version-3-1)

[Documentazione di riferimento](https://docs.microsoft.com/java/api/overview/azure/ai-textanalytics-readme?view=azure-java-stable) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-java/blob/azure-ai-textanalytics_5.1.0-beta.1/sdk/textanalytics/azure-ai-textanalytics) | [Pacchetto](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.1.0-beta.1) | [Esempi](https://github.com/Azure/azure-sdk-for-java/tree/azure-ai-textanalytics_5.1.0-beta.1/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

# <a name="version-30"></a>[Versione 3.0](#tab/version-3)

[Documentazione di riferimento](https://docs.microsoft.com/java/api/overview/azure/ai-textanalytics-readme?view=azure-java-stable) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-java/blob/azure-ai-textanalytics_5.0.0/sdk/textanalytics/azure-ai-textanalytics) | [Pacchetto](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.0.0) | [Esempi](https://github.com/Azure/azure-sdk-for-java/tree/azure-ai-textanalytics_5.0.0/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

# <a name="version-21"></a>[Versione 2.1](#tab/version-2)

Questo articolo descrive solo la versione 3.x dell'API.

---

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services)
* [Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK) versione 8 o successiva
* Dopo aver creato la sottoscrizione di Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="creare una risorsa di Analisi del testo"  target="_blank">creare una risorsa di Analisi del testo <span class="docon docon-navigate-external x-hidden-focus"></span></a> nel portale di Azure per ottenere la chiave e l'endpoint.  Al termine della distribuzione, fare clic su **Vai alla risorsa**.
    * La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione all'API Analisi del testo. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
    * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.

## <a name="setting-up"></a>Configurazione

### <a name="add-the-client-library"></a>Aggiungere la libreria client

# <a name="version-31-preview"></a>[Versione 3.1-preview](#tab/version-3-1)

Creare un progetto Maven nell'ambiente IDE o di sviluppo preferito. Aggiungere quindi la dipendenza seguente al file *pom.xml* del progetto. È possibile trovare la sintassi di implementazione [per altri strumenti di compilazione](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.1.0-beta.1) online.

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>5.1.0-beta.1</version>
    </dependency>
</dependencies>
```

# <a name="version-30"></a>[Versione 3.0](#tab/version-3)

Creare un progetto Maven nell'ambiente IDE o di sviluppo preferito. Aggiungere quindi la dipendenza seguente al file *pom.xml* del progetto. È possibile trovare la sintassi di implementazione [per altri strumenti di compilazione](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.0.0) online.

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>5.0.0</version>
    </dependency>
</dependencies>
```

> [!TIP]
> Si vuole visualizzare l'intero file di codice dell'argomento di avvio rapido? È possibile trovarlo [in GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/TextAnalytics/TextAnalyticsSamples.java), che contiene gli esempi di codice di questo argomento. 

# <a name="version-21"></a>[Versione 2.1](#tab/version-2)

Questo articolo descrive solo la versione 3.x dell'API.

---

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

# <a name="version-31-preview"></a>[Versione 3.1 (anteprima)](#tab/version-3-1)

```java
public static void main(String[] args) {
    //You will create these methods later in the quickstart.
    TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);

    sentimentAnalysisWithOpinionMiningExample(client)
    detectLanguageExample(client);
    recognizeEntitiesExample(client);
    recognizeLinkedEntitiesExample(client);
    recognizePiiEntitiesExample(client);
    extractKeyPhrasesExample(client);
}
```

# <a name="version-30"></a>[Versione 3.0](#tab/version-3)

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

# <a name="version-21"></a>[Versione 2.1](#tab/version-2)

Questo articolo descrive solo la versione 3.x dell'API.

---


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

Creare un metodo per creare un'istanza dell'oggetto `TextAnalyticsClient` con la chiave e l'endpoint per la risorsa Analisi del testo. Questo esempio è identico per le versioni 3.0 e 3.1 dell'API.

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

# <a name="version-31-preview"></a>[Versione 3.1-preview](#tab/version-3-1)

> [!NOTE]
> Nella versione `3.1`:
> * L'analisi del sentiment include l'analisi di opinion mining, che è un flag facoltativo. 
> * La funzionalità di opinion mining contiene il sentiment a livello di aspetto e opinione. 

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
}
```

### <a name="output"></a>Output

```console
Recognized document sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: neutral, positive score: 0.21, neutral score: 0.77, negative score: 0.02.
```

### <a name="opinion-mining"></a>Opinion mining

Per eseguire l'analisi del sentiment con l'opinion mining, creare una nuova funzione denominata `sentimentAnalysisWithOpinionMiningExample()` che accetta il client creato in precedenza e chiamare la relativa funzione `analyzeSentiment()` con l'oggetto di opzioni di impostazione `AnalyzeSentimentOptions`. L'oggetto `AnalyzeSentimentResult` restituito conterrà `documentSentiment` e `sentenceSentiments`, se l'operazione riesce, oppure `errorMessage` in caso contrario. 


```java
static void sentimentAnalysisWithOpinionMiningExample(TextAnalyticsClient client)
{
    // The Document that needs be analyzed.
    String document = "Bad atmosphere. Not close to plenty of restaurants, hotels, and transit! Staff are not friendly and helpful.";

    System.out.printf("Document = %s%n", document);

    AnalyzeSentimentOptions options = new AnalyzeSentimentOptions().setIncludeOpinionMining(true);
    final DocumentSentiment documentSentiment = client.analyzeSentiment(document, "en", options);
    SentimentConfidenceScores scores = documentSentiment.getConfidenceScores();
    System.out.printf(
            "\tRecognized document sentiment: %s, positive score: %f, neutral score: %f, negative score: %f.%n",
            documentSentiment.getSentiment(), scores.getPositive(), scores.getNeutral(), scores.getNegative());

    documentSentiment.getSentences().forEach(sentenceSentiment -> {
        SentimentConfidenceScores sentenceScores = sentenceSentiment.getConfidenceScores();
        System.out.printf("\t\tSentence sentiment: %s, positive score: %f, neutral score: %f, negative score: %f.%n",
                sentenceSentiment.getSentiment(), sentenceScores.getPositive(), sentenceScores.getNeutral(), sentenceScores.getNegative());
        sentenceSentiment.getMinedOpinions().forEach(minedOpinions -> {
            AspectSentiment aspectSentiment = minedOpinions.getAspect();
            System.out.printf("\t\t\tAspect sentiment: %s, aspect text: %s%n", aspectSentiment.getSentiment(),
                    aspectSentiment.getText());
            for (OpinionSentiment opinionSentiment : minedOpinions.getOpinions()) {
                System.out.printf("\t\t\t\t'%s' opinion sentiment because of \"%s\". Is the opinion negated: %s.%n",
                        opinionSentiment.getSentiment(), opinionSentiment.getText(), opinionSentiment.isNegated());
            }
        });
    });
}
```

### <a name="output"></a>Output

```console
Text = Bad atmosphere. Not close to plenty of restaurants, hotels, and transit! Staff are not friendly and helpful.
    Recognized document sentiment: negative, positive score: 0.010000, neutral score: 0.140000, negative score: 0.850000.
        Sentence sentiment: negative, positive score: 0.000000, neutral score: 0.000000, negative score: 1.000000.
            Aspect sentiment: negative, aspect text: atmosphere
                'negative' opinion sentiment because of "bad". Is the opinion negated: false.
        Sentence sentiment: negative, positive score: 0.020000, neutral score: 0.440000, negative score: 0.540000.
        Sentence sentiment: negative, positive score: 0.000000, neutral score: 0.000000, negative score: 1.000000.
            Aspect sentiment: negative, aspect text: Staff
                'negative' opinion sentiment because of "friendly". Is the opinion negated: true.
                'negative' opinion sentiment because of "helpful". Is the opinion negated: true.

Process finished with exit code 0
```

# <a name="version-30"></a>[Versione 3.0](#tab/version-3)

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
}
```

### <a name="output"></a>Output

```console
Recognized document sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: neutral, positive score: 0.21, neutral score: 0.77, negative score: 0.02.
```

# <a name="version-21"></a>[Versione 2.1](#tab/version-2)

Questo articolo descrive solo la versione 3.x dell'API.

---

## <a name="language-detection"></a>Rilevamento della lingua

Creare una nuova funzione denominata `detectLanguageExample()`, che accetta il client creato in precedenza e chiama la relativa funzione `detectLanguage()`. L'oggetto `DetectLanguageResult` restituito conterrà la lingua principale rilevata e un elenco delle altre lingue rilevate, se l'operazione riesce, oppure `errorMessage` in caso contrario. Questo esempio è identico per le versioni 3.0 e 3.1 dell'API.

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

# <a name="version-31-preview"></a>[Versione 3.1-preview](#tab/version-3-1)

> [!NOTE]
> Nella versione `3.1`:
> * Il riconoscimento di entità denominate include metodi distinti per il rilevamento di informazioni personali. 
> * Il collegamento di entità è una richiesta distinta rispetto al riconoscimento di entità denominate.

Creare una nuova funzione denominata `recognizeEntitiesExample()`, che accetta il client creato in precedenza e chiama la relativa funzione `recognizeEntities()`. L'oggetto `CategorizedEntityCollection` restituito conterrà un elenco di `CategorizedEntity`, se l'operazione riesce, oppure `errorMessage` in caso contrario.

```java
static void recognizeEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had a wonderful trip to Seattle last week.";

    for (CategorizedEntity entity : client.recognizeEntities(text)) {
        System.out.printf(
            "Recognized entity: %s, entity category: %s, entity sub-category: %s, score: %s, offset: %s, length: %s.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubcategory(),
            entity.getConfidenceScore(),
            entity.getOffset(),
            entity.getLength());
    }
}
```

### <a name="output"></a>Output

```console
Recognized entity: trip, entity category: Event, entity sub-category: null, score: 0.61, offset: 8, length: 4.
Recognized entity: Seattle, entity category: Location, entity sub-category: GPE, score: 0.82, offset: 16, length: 7.
Recognized entity: last week, entity category: DateTime, entity sub-category: DateRange, score: 0.8, offset: 24, length: 9.
```

### <a name="entity-linking"></a>Collegamento di entità

Creare una nuova funzione denominata `recognizeLinkedEntitiesExample()`, che accetta il client creato in precedenza e chiama la relativa funzione `recognizeLinkedEntities()`. L'oggetto `LinkedEntityCollection` restituito conterrà un elenco di `LinkedEntity`, se l'operazione riesce, oppure `errorMessage` in caso contrario. Poiché le entità collegate vengono identificate in modo univoco, le occorrenze della stessa entità vengono raggruppate in un oggetto `LinkedEntity` come elenco di oggetti `LinkedEntityMatch`.


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
            System.out.printf("Text: %s, Score: %.2f, Offset: %s, Length: %s%n",
            linkedEntityMatch.getText(),
            linkedEntityMatch.getConfidenceScore(),
            linkedEntityMatch.getOffset(),
            linkedEntityMatch.getLength());
        }
    }
}
```

### <a name="output"></a>Output

```console
Linked Entities:
Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
Matches:
Text: Microsoft, Score: 0.55, Offset: 9, Length: 0
Text: Microsoft, Score: 0.55, Offset: 9, Length: 150
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
Matches:
Text: Bill Gates, Score: 0.63, Offset: 10, Length: 25
Text: Gates, Score: 0.63, Offset: 5, Length: 161
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
Matches:
Text: Paul Allen, Score: 0.60, Offset: 10, Length: 40
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
Matches:
Text: April 4, Score: 0.32, Offset: 7, Length: 54
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
Matches:
Text: BASIC, Score: 0.33, Offset: 5, Length: 89
Name: Altair 8800, ID: Altair 8800, URL: https://en.wikipedia.org/wiki/Altair_8800, Data Source: Wikipedia.
Matches:
Text: Altair 8800, Score: 0.88, Offset: 11, Length: 116
```


### <a name="personally-identifiable-information-recognition"></a>Riconoscimento di informazioni personali

Creare una nuova funzione denominata `recognizePiiEntitiesExample()`, che accetta il client creato in precedenza e chiama la relativa funzione `recognizePiiEntities()`. L'oggetto `PiiEntityCollection` restituito conterrà un elenco di `PiiEntity`, se l'operazione riesce, oppure `errorMessage` in caso contrario. Conterrà anche il testo redatto, costituito dal testo di input con tutte le entità identificabili sostituite con `*****`.

```java
static void recognizePiiEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String document = "My SSN is 859-98-0987";
    PiiEntityCollection piiEntityCollection = client.recognizePiiEntities(document);
    System.out.printf("Redacted Text: %s%n", piiEntityCollection.getRedactedText());
    piiEntityCollection.forEach(entity -> System.out.printf(
        "Recognized Personally Identifiable Information entity: %s, entity category: %s, entity subcategory: %s,"
            + " confidence score: %f.%n",
        entity.getText(), entity.getCategory(), entity.getSubcategory(), entity.getConfidenceScore()));
}
```

### <a name="output"></a>Output

```console
Redacted Text: My SSN is ***********
Recognized Personally Identifiable Information entity: 859-98-0987, entity category: U.S. Social Security Number (SSN), entity subcategory: null, confidence score: 0.650000.
```

# <a name="version-30"></a>[Versione 3.0](#tab/version-3)

> [!NOTE]
> Nella versione `3.0`:
> * Il riconoscimento di entità denominate include metodi distinti per il rilevamento di informazioni personali. 
> * Il collegamento di entità è una richiesta distinta rispetto al riconoscimento di entità denominate.

Creare una nuova funzione denominata `recognizeEntitiesExample()`, che accetta il client creato in precedenza e chiama la relativa funzione `recognizeEntities()`. L'oggetto `CategorizedEntityCollection` restituito conterrà un elenco di `CategorizedEntity`, se l'operazione riesce, oppure `errorMessage` in caso contrario.

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

### <a name="entity-linking"></a>Collegamento di entità

Creare una nuova funzione denominata `recognizeLinkedEntitiesExample()`, che accetta il client creato in precedenza e chiama la relativa funzione `recognizeLinkedEntities()`. L'oggetto `LinkedEntityCollection` restituito conterrà un elenco di `LinkedEntity`, se l'operazione riesce, oppure `errorMessage` in caso contrario. Poiché le entità collegate vengono identificate in modo univoco, le occorrenze della stessa entità vengono raggruppate in un oggetto `LinkedEntity` come elenco di oggetti `LinkedEntityMatch`.

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

# <a name="version-21"></a>[Versione 2.1](#tab/version-2)

Questo articolo descrive solo la versione 3.x dell'API.

---

## <a name="key-phrase-extraction"></a>Estrazione di frasi chiave

Creare una nuova funzione denominata `extractKeyPhrasesExample()`, che accetta il client creato in precedenza e chiama la relativa funzione `extractKeyPhrases()`. L'oggetto `ExtractKeyPhraseResult` restituito conterrà un elenco di frasi chiave, se l'operazione riesce, oppure `errorMessage` in caso contrario. Questo esempio è identico per le versioni 3.0 e 3.1 dell'API.

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