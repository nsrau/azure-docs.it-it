---
title: 'Guida introduttiva: Libreria client di Analisi del testo per Go | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Informazioni ed esempi di codice per iniziare a usare l'API Analisi del testo in Servizi cognitivi di Azure.
services: cognitive-services
author: laramume
manager: assafi
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: 25d8052cda422c185d49c36c5daff9ac4582e66c
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881017"
---
# <a name="quickstart-text-analytics-client-library-for-go"></a>Guida introduttiva: Libreria client di Analisi del testo per Go

Introduzione alla libreria client di Analisi del testo per Go. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base. 

Usare la libreria client di Analisi del testo per Go per eseguire queste operazioni:

* Analisi del sentiment
* Rilevamento della lingua
* Riconoscimento delle entità
* Estrazione di frasi chiave

[Documentazione di riferimento](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | [Pacchetto (GitHub)](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics) | [Esempi](https://github.com/Azure-Samples/cognitive-services-quickstart-code)

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuitamente](https://azure.microsoft.com/free/)
* Versione più recente di [Go](https://golang.org/dl/)

## <a name="setting-up"></a>Configurazione

### <a name="create-a-text-analytics-azure-resource"></a>Creare una risorsa di Azure per Analisi del testo 

I Servizi cognitivi di Azure sono rappresentati dalle risorse di Azure a cui si effettua la sottoscrizione. Creare una risorsa per Analisi del testo usando il [portale di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) o l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) nel computer locale. È anche possibile:

* Ottenere un [codice della versione di valutazione gratuita](https://azure.microsoft.com/try/cognitive-services/#decision), valido per 7 giorni. Dopo aver eseguito l'iscrizione, sarà disponibile sul [sito Web di Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).
* Visualizzare questa risorsa nel [portale di Azure](https://portal.azure.com).

Dopo aver ottenuto una chiave dalla sottoscrizione di valutazione o dalla risorsa, [creare una variabile di ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) per la chiave, denominata `TEXT_ANALYTICS_SUBSCRIPTION_KEY`.

### <a name="create-a-new-go-project"></a>Creare un nuovo progetto Go

In una finestra della console, ad esempio cmd, PowerShell o Bash, creare e passare a un'area di lavoro per il progetto Go. L'area di lavoro conterrà tre cartelle: 

* **src**: questa directory contiene il codice sorgente e i pacchetti. Tutti i pacchetti installati con il comando `go get` saranno inclusi qui.
* **pkg**: questa directory contiene gli oggetti pacchetto Go compilati. Tutti i file hanno estensione `.a`.
* **bin**: questa directory contiene i file eseguibili binari creati durante l'esecuzione di `go install`.

> [!TIP]
> Acquisire familiarità con la struttura di un'[area di lavoro Go](https://golang.org/doc/code.html#Workspaces). Questa guida include informazioni per l'impostazione di `$GOPATH` e `$GOROOT`.

Creare un'area di lavoro denominata `my-app` e le sottodirectory necessarie per `src`, `pkg` e `bin`:

```console
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

### <a name="install-the-text-analytics-client-library-for-go"></a>Installare la libreria client di Analisi del testo per Go

Installare la libreria client per Go: 

```console
$ go get -u <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

oppure, se si usa DEP, all'interno del repository eseguire:

```console
$ dep ensure -add <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

### <a name="create-your-go-application"></a>Creare l'applicazione Go

Creare quindi un file denominato `src/quickstart.go`:

```bash
$ cd src
$ touch quickstart.go
```

Aprire `quickstart.go` nell'IDE o nell'editor di testo preferito. Aggiungere quindi il nome del pacchetto e importare le librerie seguenti:

```golang
import (
    "context"
    "encoding/json"
    "fmt"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics"
    "github.com/Azure/go-autorest/autorest"
)
```

Aggiungere le funzioni seguenti al progetto, perché la maggior parte dei parametri e delle proprietà per questo argomento di avvio rapido richiede puntatori di stringa e booleani.

```golang
// returns a pointer to the string value passed in.
func StringPointer(v string) *string {
    return &v
}

// returns a pointer to the bool value passed in.
func BoolPointer(v bool) *bool {
    return &v
}
```

Nella funzione principale del progetto creare le variabili per l'endpoint e la chiave di Azure della risorsa. Se la variabile di ambiente è stata creata dopo l'avvio dell'applicazione, per accedervi sarà necessario chiudere e riaprire l'editor, l'IDE o la shell in cui è in esecuzione.

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

```golang
// This sample assumes you have created an environment variable for your key
subscriptionKey := os.Getenv("TEXT_ANALYTICS_SUBSCRIPTION_KEY")
// replace this endpoint with the correct one for your Azure resource. 
endpoint := "https://eastus.api.cognitive.microsoft.com"
```

## <a name="object-model"></a>Modello a oggetti 

Il client di Analisi del testo è un oggetto [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) che esegue l'autenticazione in Azure tramite la chiave. Il client fornisce diversi metodi per l'analisi del testo, come singola stringa o batch. 

Il testo viene inviato all'API come elenco di `documents`, che sono oggetti `dictionary` contenenti una combinazione di attributi `id`, `text` e `language`, a seconda del metodo usato. L'attributo `text` archivia il testo da analizzare in base all'attributo `language` di origine, mentre `id` può essere un valore qualsiasi. 

L'oggetto risposta è un elenco contenente le informazioni di analisi per ogni documento. 

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le attività seguenti con la libreria client di Analisi del testo per Python:

* [Autenticare il client](#authenticate-the-client)
* [Analisi del sentiment](#sentiment-analysis)
* [Rilevamento della lingua](#language-detection)
* [Riconoscimento delle entità](#entity-recognition)
* [Estrazione delle frasi chiave](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autenticare il client

Nella funzione principale del progetto creare un nuovo oggetto [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New). Passare la chiave alla funzione [autorest.NewCognitiveServicesAuthorizer()](https://godoc.org/github.com/Azure/go-autorest/autorest#NewCognitiveServicesAuthorizer), che verrà quindi passata alla proprietà `authorizer` del client.

```golang
textAnalyticsClient := textanalytics.New(endpoint)
textAnalyticsClient.Authorizer = autorest.NewCognitiveServicesAuthorizer(subscriptionKey)
```

## <a name="sentiment-analysis"></a>Analisi del sentiment

Creare una nuova funzione denominata `SentimentAnalysis()` che accetta il client creato in precedenza. Creare un elenco di oggetti [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) contenente i documenti da analizzare. Ogni oggetto conterrà un `id`, `Language` e un attributo `text`. Nell'attributo `text` viene memorizzato il testo da analizzare, mentre `language` è la lingua del documento e `id` può essere un valore qualsiasi. 

```golang
func SentimentAnalysis(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.MultiLanguageInput {
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("0"),
            Text:StringPointer("I had the best day of my life."),
        },
    }

    batchInput := textanalytics.MultiLanguageBatchInput{Documents:&inputDocuments}
}
```

Nella stessa funzione chiamare la funzione [Sentiment()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Sentiment) del client e ottenere il risultato. Quindi eseguire l'iterazione attraverso i risultati e stampare l'ID di ogni documento e il punteggio del sentiment. I punteggi più vicini a 0 indicano un sentiment negativo, mentre quelli più vicini a 1 indicano un sentiment positivo.

```golang
result, _ := textAnalyticsclient.Sentiment(ctx, BoolPointer(false), &batchInput)
batchResult := textanalytics.SentimentBatchResult{}
jsonString, _ := json.Marshal(result.Value)
json.Unmarshal(jsonString, &batchResult)

// Printing sentiment results
for _,document := range *batchResult.Documents {
    fmt.Printf("Document ID: %s " , *document.ID)
    fmt.Printf("Sentiment Score: %f\n",*document.Score)
}

// Printing document errors
fmt.Println("Document Errors")
for _,error := range *batchResult.Errors {
    fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
}
```

Nella funzione principale del progetto chiamare `SentimentAnalysis()`.

### <a name="output"></a>Output

```console
Document ID: 1 , Sentiment Score: 0.87
```

## <a name="language-detection"></a>Rilevamento della lingua

Creare una nuova funzione denominata `LanguageDetection()` che accetta il client creato in precedenza. Creare un elenco di oggetti [LanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#LanguageInput) contenente i documenti da analizzare. Ogni oggetto conterrà un `id` e un attributo `text`. Nell'attributo `text` viene memorizzato il testo da analizzare, mentre l'`id` può essere un valore qualsiasi. 

```golang
func LanguageDetection(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.LanguageInput {
        textanalytics.LanguageInput {
            ID:StringPointer("0"),
            Text:StringPointer("This is a document written in English."),
        },
    }

    batchInput := textanalytics.LanguageBatchInput{Documents:&inputDocuments}
}
```

Nella stessa funzione chiamare la funzione [DetectLanguage()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.DetectLanguage) del client e ottenere il risultato. Quindi eseguire l'iterazione attraverso i risultati e stampare l'ID di ogni documento e la lingua rilevata.

```golang
result, _ := textAnalyticsclient.DetectLanguage(ctx, BoolPointer(false), &batchInput)

// Printing language detection results
for _,document := range *result.Documents {
    fmt.Printf("Document ID: %s " , *document.ID)
    fmt.Printf("Detected Languages with Score: ")
    for _,language := range *document.DetectedLanguages{
        fmt.Printf("%s %f,",*language.Name, *language.Score)
    }
    fmt.Println()
}

// Printing document errors
fmt.Println("Document Errors")
for _,error := range *result.Errors {
    fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
}
```

Nella funzione principale del progetto chiamare `LanguageDetection()`.

### <a name="output"></a>Output

```console
Document ID: 0 Detected Languages with Score: English 1.000000
```

## <a name="entity-recognition"></a>Riconoscimento delle entità

Creare una nuova funzione denominata `ExtractEntities()` che accetta il client creato in precedenza. Creare un elenco di oggetti [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) contenente i documenti da analizzare. Ogni oggetto conterrà un `id`, `language` e un attributo `text`. Nell'attributo `text` viene memorizzato il testo da analizzare, mentre `language` è la lingua del documento e `id` può essere un valore qualsiasi. 

```golang
func ExtractKeyPhrases(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.MultiLanguageInput {
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("0"),
            Text:StringPointer("Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."),
        }
    }

    batchInput := textanalytics.MultiLanguageBatchInput{Documents:&inputDocuments}
}
```

Nella stessa funzione chiamare la funzione [Entities()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Entities) del client e ottenere il risultato. Eseguire quindi l'iterazione attraverso i risultati e stampare l'ID di ogni documento e il punteggio delle entità estratte.

```golang
    result, _ := textAnalyticsclient.Entities(ctx, BoolPointer(false), &batchInput)

    // Printing extracted entities results
    for _,document := range *result.Documents {
        fmt.Printf("Document ID: %s\n" , *document.ID)
        fmt.Printf("\tExtracted Entities:\n")
        for _,entity := range *document.Entities{
            fmt.Printf("\t\tName: %s\tType: %s",*entity.Name, *entity.Type)
            if entity.SubType != nil{
                fmt.Printf("\tSub-Type: %s\n", *entity.SubType)
            }
            fmt.Println()
            for _,match := range *entity.Matches{
                fmt.Printf("\t\t\tOffset: %v\tLength: %v\tScore: %f\n", *match.Offset, *match.Length, *match.EntityTypeScore)
            }
        }
        fmt.Println()
    }

    // Printing document errors
    fmt.Println("Document Errors")
    for _,error := range *result.Errors {
        fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
    }
```

Nella funzione principale del progetto chiamare `ExtractEntities()`.

### <a name="output"></a>Output

```console
Document ID: 0
    Extracted Entities:
        Name: Microsoft Type: Organization
            Offset: 0   Length: 9   Score: 1.000000
        Name: Bill Gates    Type: Person
            Offset: 25  Length: 10  Score: 0.999847
        Name: Paul Allen    Type: Person
            Offset: 40  Length: 10  Score: 0.998841
        Name: April 4   Type: Other
            Offset: 54  Length: 7   Score: 0.800000
        Name: April 4, 1975 Type: DateTime  Sub-Type: Date

            Offset: 54  Length: 13  Score: 0.800000
        Name: BASIC Type: Other
            Offset: 89  Length: 5   Score: 0.800000
        Name: Altair 8800   Type: Other
            Offset: 116 Length: 11  Score: 0.800000
```

## <a name="key-phrase-extraction"></a>Estrazione di frasi chiave

Creare una nuova funzione denominata `ExtractKeyPhrases()` che accetta il client creato in precedenza. Creare un elenco di oggetti [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) contenente i documenti da analizzare. Ogni oggetto conterrà un `id`, `language` e un attributo `text`. Nell'attributo `text` viene memorizzato il testo da analizzare, mentre `language` è la lingua del documento e `id` può essere un valore qualsiasi.

```golang
func ExtractKeyPhrases(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.MultiLanguageInput {
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("0"),
            Text:StringPointer("My cat might need to see a veterinarian."),
        },
    }

    batchInput := textanalytics.MultiLanguageBatchInput{Documents:&inputDocuments}
}
```

Nella stessa funzione chiamare la funzione [KeyPhrases()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.KeyPhrases) del client e ottenere il risultato. Quindi eseguire l'iterazione attraverso i risultati e stampare l'ID di ogni documento e le frasi chiave estratte.

```golang
    result, _ := textAnalyticsclient.KeyPhrases(ctx, BoolPointer(false), &batchInput)

    // Printing extracted key phrases results
    for _,document := range *result.Documents {
        fmt.Printf("Document ID: %s\n" , *document.ID)
        fmt.Printf("\tExtracted Key Phrases:\n")
        for _,keyPhrase := range *document.KeyPhrases{
            fmt.Printf("\t\t%s\n",keyPhrase)
        }
        fmt.Println()
    }

    // Printing document errors
    fmt.Println("Document Errors")
    for _,error := range *result.Errors {
        fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
    }
```

Nella funzione principale del progetto chiamare `ExtractKeyPhrases()`.

### <a name="output"></a>Output

```console
Document ID: 0
    Extracted Key Phrases:
        cat
        veterinarian
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una sottoscrizione a Servizi cognitivi, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche le altre risorse associate al gruppo.

* [Portale](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfaccia della riga di comando di Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passaggi successivi


> [!div class="nextstepaction"]
> [Analisi del testo con Power BI](../tutorials/tutorial-power-bi-key-phrases.md)


* [Panoramica di Analisi del testo](../overview.md)
* [Analisi del sentiment](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Riconoscimento delle entità](../how-tos/text-analytics-how-to-entity-linking.md)
* [Rilevare la lingua](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Riconoscimento della lingua](../how-tos/text-analytics-how-to-language-detection.md)
* Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/cognitiveservices).
