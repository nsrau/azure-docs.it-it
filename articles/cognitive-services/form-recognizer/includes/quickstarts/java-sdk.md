---
title: 'Avvio rapido: Libreria client di Riconoscimento modulo per Java'
description: Usare la libreria client di Riconoscimento modulo per Java per creare un'app per l'elaborazione di moduli che estrae coppie chiave/valore e dati di tabelle dai documenti personalizzati.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 09/21/2020
ms.custom: devx-track-java
ms.author: pafarley
ms.openlocfilehash: fa15b48cff73b567187aa078bec02aa82e41e665
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92918729"
---
> [!IMPORTANT]
> Il codice di questo articolo usa metodi sincroni e archiviazione con credenziali non protette per motivi di semplicità.

[Documentazione di riferimento ](/java/api/overview/azure/ai-formrecognizer-readme?view=azure-java-preview) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src) | [Pacchetto (Maven)](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer) | [Esempi](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md)

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services)
* La versione più recente di [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Lo [strumento di compilazione Gradle](https://gradle.org/install/) o un'altra utilità di gestione dipendenze.
* Dopo aver creato la sottoscrizione di Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="creare una risorsa di Riconoscimento modulo"  target="_blank">creare una risorsa di Riconoscimento modulo <span class="docon docon-navigate-external x-hidden-focus"></span></a> nel portale di Azure per ottenere la chiave e l'endpoint. Al termine della distribuzione, fare clic su **Vai alla risorsa**.
    * La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione all'API Riconoscimento modulo. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
    * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.
* Un BLOB di Archiviazione di Azure contenente un set di dati di training. Consultare [Compilare un training set per un modello personalizzato](../../build-training-data-set.md) per suggerimenti e opzioni per la creazione di un set di dati di training. Per questo argomento di avvio rapido, è possibile usare i file inclusi nella cartella **Train** del [set di dati di esempio](https://go.microsoft.com/fwlink/?linkid=2090451) (scaricare ed estrarre *sample_data.zip* ).


## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-gradle-project"></a>Creare un nuovo progetto Gradle

In una finestra della console, ad esempio cmd, PowerShell o Bash, creare e passare a una nuova directory per l'app. 

```console
mkdir myapp && cd myapp
```

Eseguire il comando `gradle init` dalla directory di lavoro. Questo comando creerà i file di compilazione essenziali per Gradle, tra cui *build.gradle.kts* , che viene usato in fase di esecuzione per creare e configurare l'applicazione.

```console
gradle init --type basic
```

Quando viene chiesto di scegliere un linguaggio **DSL** , selezionare **Kotlin**.


### <a name="install-the-client-library"></a>Installare la libreria client

Questo argomento di avvio rapido usa l'utilità di gestione dipendenze Gradle. La libreria client e le informazioni per altre utilità di gestione dipendenze sono disponibili in [Maven Central Repository](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer).

Nel file *build.gradle.kts* del progetto includere la libreria client come istruzione `implementation`, unitamente ai plug-in e alle impostazioni necessari.

```kotlin
plugins {
    java
    application
}
application {
    mainClass.set("FormRecognizer")
}
repositories {
    mavenCentral()
}
dependencies {
    implementation(group = "com.azure", name = "azure-ai-formrecognizer", version = "3.0.0")
}
```

### <a name="create-a-java-file"></a>Creare un file Java


Dalla directory di lavoro eseguire il comando seguente:

```console
mkdir -p src/main/java
```

Passare alla nuova cartella e creare un file denominato *FormRecognizer.java*. Aprirlo in un editor o un IDE a scelta e importare le istruzioni `import` seguenti:

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_imports)]

> [!TIP]
> Si vuole visualizzare l'intero file di codice dell'argomento di avvio rapido? È possibile trovarlo [in GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/FormRecognizer/FormRecognizer.java), che contiene gli esempi di codice di questo argomento.


Nella classe **FormRecognizer** dell'applicazione creare le variabili per l'endpoint e la chiave della risorsa.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_creds)]

> [!IMPORTANT]
> Accedere al portale di Azure. Se la risorsa [Nome prodotto] creata nella sezione **Prerequisiti** è stata distribuita correttamente, fare clic sul pulsante **Vai alla risorsa** in **Passaggi successivi**. La chiave e l'endpoint saranno disponibili nella pagina **Chiavi ed endpoint** della risorsa in **Gestione risorse**. 
>
> Al termine, ricordarsi di rimuovere la chiave dal codice e non renderlo mai pubblico. Per la produzione, è consigliabile usare un modo sicuro per archiviare e accedere alle credenziali, Per altre informazioni, vedere l'articolo sulla [sicurezza](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security) di Servizi cognitivi.

Nel metodo **main** dell'applicazione aggiungere le chiamate per i metodi usati in questa guida di avvio rapido. Verranno definiti in un secondo momento. Sarà inoltre necessario aggiungere riferimenti agli URL per i dati di training e di test.

* Per recuperare l'URL di firma di accesso condiviso per i dati di training del modello personalizzato, aprire Microsoft Azure Storage Explorer, fare clic con il pulsante destro del mouse sul contenitore e scegliere **Ottieni firma di accesso condiviso**. Assicurarsi che le autorizzazioni **Lettura** ed **Elenco** siano selezionate e fare clic su **Crea**. A questo punto, copiare il valore dalla sezione **URL**. Dovrebbe essere in questo formato: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
* Per ottenere un URL di un modulo da testare è possibile usare i passaggi precedenti per ottenere l'URL di firma di accesso condiviso di un singolo documento nell'archivio BLOB. In alternativa, prendere l'URL di un documento situato altrove.
* Usare il metodo precedente per ottenere l'URL anche di un'immagine di una ricevuta.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_mainvars)]

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_maincalls)]



## <a name="object-model"></a>Modello a oggetti 

Con Riconoscimento modulo è possibile creare due diversi tipi di client. Il primo, `FormRecognizerClient`, viene usato per eseguire query sul servizio per riconoscere campi modulo e contenuti. Il secondo, `FormTrainingClient`, viene usato per creare e gestire modelli personalizzati da usare per migliorare il riconoscimento. 

### <a name="formrecognizerclient"></a>FormRecognizerClient

`FormRecognizerClient` fornisce le operazioni per:

- Riconoscere campi modulo e contenuti, usando modelli personalizzati sottoposti a training per riconoscere i moduli personalizzati.  Questi valori vengono restituiti in una raccolta di oggetti `RecognizedForm`. Vedere l'esempio [Analizzare moduli personalizzati](#analyze-forms-with-a-custom-model).
- Riconoscere i contenuti dei moduli, incluse tabelle, righe e parole, senza la necessità di eseguire il training di un modello.  I contenuti dei moduli vengono restituiti in una raccolta di oggetti `FormPage`. Vedere l'esempio [Riconoscere il contenuto di un modulo](#recognize-form-content).
- Riconoscere i campi comuni delle ricevute degli Stati Uniti, usando un modello di ricevuta con training preliminare nel servizio Riconoscimento modulo.  Questi campi e i metadati vengono restituiti in una raccolta di oggetti `RecognizedForm`. Vedere l'esempio [Riconoscere le ricevute](#recognize-receipts).

### <a name="formtrainingclient"></a>FormTrainingClient

`FormTrainingClient` fornisce le operazioni per:

- Eseguire il training di modelli personalizzati per riconoscere tutti i campi e i valori trovati nei moduli personalizzati.  Viene restituito un `CustomFormModel` che indica i tipi di modulo che il modello riconoscerà e i campi che estrarrà per ogni tipo di modulo.
- Eseguire il training di modelli personalizzati per riconoscere i campi e i valori specificati mediante l'assegnazione di etichette ai moduli personalizzati.  Viene restituito un `CustomFormModel` che indica i campi che verranno estratti dal modello e l'accuratezza stimata per ogni campo.
- Gestire i modelli creati nell'account.
- Copiare un modello personalizzato da una risorsa Riconoscimento modulo a un'altra.

> [!NOTE]
> È possibile eseguire il training dei modelli anche con un'interfaccia utente grafica, ad esempio con lo [strumento di etichettatura di Riconoscimento modulo](../../quickstarts/label-tool.md).

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le attività seguenti con la libreria client di Riconoscimento modulo per Java:

* [Autenticare il client](#authenticate-the-client)
* [Riconoscere il contenuto di un modulo](#recognize-form-content)
* [Riconoscere le ricevute](#recognize-receipts)
* [Eseguire il training di un modello personalizzato](#train-a-custom-model)
* [Analizzare i moduli con un modello personalizzato](#analyze-forms-with-a-custom-model)
* [Gestire i modelli personalizzati](#manage-your-custom-models)

## <a name="authenticate-the-client"></a>Autenticare il client

All'inizio del metodo **principale** , aggiungere il codice seguente. In questo caso si eseguirà l'autenticazione dei due oggetti client con le variabili di sottoscrizione definite in precedenza. Si userà un oggetto **AzureKeyCredential** in modo che, se necessario, sia possibile aggiornare la chiave API senza creare nuovi oggetti client.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_auth)]

## <a name="recognize-form-content"></a>Riconoscere il contenuto di un modulo

È possibile usare Riconoscimento modulo per riconoscere tabelle, righe e parole nei documenti, senza dover eseguire il training di un modello.

Per riconoscere il contenuto di un file all'interno di un URL specifico, usare il metodo **beginRecognizeContentFromUrl**.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_getcontent_call)]

> [!TIP]
> È anche possibile ottenere contenuto da un file locale. Vedere i metodi [FormRecognizerClient](https://docs.microsoft.com/java/api/com.azure.ai.formrecognizer.formrecognizerclient?view=azure-java-stable), ad esempio **beginRecognizeContent**. In alternativa, per gli scenari con immagini locali, vedere il codice di esempio in [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md).

Il valore restituito è una raccolta di oggetti **FormPage** : uno per ogni pagina nel documento inviato. Il codice seguente esegue l'iterazione di questi oggetti e stampa le coppie chiave-valore estratte con i dati della tabella.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_getcontent_print)]
### <a name="output"></a>Output

```console
Get form content...
----Recognizing content ----
Has width: 8.500000 and height: 11.000000, measured with unit: inch.
Table has 2 rows and 6 columns.
Cell has text Invoice Number.
Cell has text Invoice Date.
Cell has text Invoice Due Date.
Cell has text Charges.
Cell has text VAT ID.
Cell has text 458176.
Cell has text 3/28/2018.
Cell has text 4/16/2018.
Cell has text $89,024.34.
Cell has text ET.
```

## <a name="recognize-receipts"></a>Riconoscere le ricevute

Questa sezione mostra come riconoscere ed estrarre i campi comuni dalle ricevute degli Stati Uniti, usando un modello di ricevuta con training preliminare.

Per riconoscere le ricevute a partire da un URL, usare il metodo **beginRecognizeReceiptsFromUrl**. 

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_receipts_call)]

> [!TIP]
> È anche possibile ottenere le immagini di ricevute locali. Vedere i metodi [FormRecognizerClient](https://docs.microsoft.com/java/api/com.azure.ai.formrecognizer.formrecognizerclient?view=azure-java-stable), ad esempio **beginRecognizeReceipts**. In alternativa, per gli scenari con immagini locali, vedere il codice di esempio in [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md).

Il valore restituito è una raccolta di oggetti **RecognizedReceipt** , uno per ogni pagina del documento inviato. Il blocco di codice successivo esegue l'iterazione delle ricevute e nel stampa i dettagli nella console.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_receipts_print)]

Il blocco di codice successivo esegue l'iterazione dei singoli elementi rilevati nella ricevuta e stampa i relativi dettagli nella console.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_receipts_print_items)]

### <a name="output"></a>Output 

```console
Analyze receipt...
----------- Recognized Receipt page 0 -----------
Merchant Name: Contoso Contoso, confidence: 0.62
Merchant Address: 123 Main Street Redmond, WA 98052, confidence: 0.99
Transaction Date: 2020-06-10, confidence: 0.90
Receipt Items:
Name: Cappuccino, confidence: 0.96s
Quantity: null, confidence: 0.957s]
Total Price: 2.200000, confidence: 0.95
Name: BACON & EGGS, confidence: 0.94s
Quantity: null, confidence: 0.927s]
Total Price: null, confidence: 0.93
```
## <a name="train-a-custom-model"></a>Eseguire il training di un modello personalizzato

Questa sezione illustra come eseguire il training di un modello con i dati personali. Un modello sottoposto a training restituisce dati strutturati che includono le relazioni chiave-valore del file originale. Dopo il training del modello, è possibile testarlo, ripeterne il training e infine usarlo per estrarre dati in modo affidabile da altri moduli in base alle proprie esigenze.

> [!NOTE]
> È anche possibile eseguire il training dei modelli con un'interfaccia utente grafica, ad esempio con lo [Strumento di etichettatura campioni Riconoscimento modulo](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Eseguire il training di un modello senza etichette

Eseguire il training di modelli personalizzati per riconoscere tutti i campi e i valori trovati nei moduli personalizzati, senza etichettare manualmente i documenti di training.

Il metodo seguente esegue il training di un modello su un set di documenti specificato e ne stampa lo stato nella console. 


[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_train_call)]

L'oggetto **CustomFormModel** restituito, contiene informazioni sui tipi di modulo che il modello è in grado di riconoscere e i campi che può estrarre da ogni tipo di modulo. Il blocco di codice seguente stampa queste informazioni all'interno della console.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_train_print)]

Infine, questo metodo restituisce l'ID univoco del modello.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_train_return)]


### <a name="output"></a>Output

```console
Train Model with training data...
Model Id: 20c3544d-97b4-49d9-b39b-dc32d85f1358
Model Status: ready
Training started on: 2020-08-31T16:52:09Z
Training completed on: 2020-08-31T16:52:23Z

Recognized Fields:
The subModel has form type form-0
The model found field 'field-0' with label: Address:
The model found field 'field-1' with label: Charges
The model found field 'field-2' with label: Invoice Date
The model found field 'field-3' with label: Invoice Due Date
The model found field 'field-4' with label: Invoice For:
The model found field 'field-5' with label: Invoice Number
The model found field 'field-6' with label: VAT ID
```

### <a name="train-a-model-with-labels"></a>Eseguire il training di un modello con etichette

È inoltre possibile eseguire il training di modelli personalizzati etichettando manualmente i documenti di training. Il training con etichette consente prestazioni migliori in alcuni scenari. Per eseguire il training con etichette, è necessario avere file speciali di informazioni sulle etichette ( *\<filename\>.pdf. labels.json* ) nel contenitore di archiviazione BLOB insieme ai documenti di training. Lo [Strumento di etichettatura campioni Riconoscimento modulo](../../quickstarts/label-tool.md) fornisce un'interfaccia utente che consente di creare questi file di etichette. Una volta creati, è possibile chiamare il metodo **beginTraining** con il parametro *useTrainingLabels* impostato su `true`.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_trainlabels_call)]


L'oggetto **CustomFormModel** restituito indica i campi che il modello può estrarre, insieme alla relativa precisione stimata in ogni campo. Il blocco di codice seguente stampa queste informazioni all'interno della console.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_trainlabels_print)]


### <a name="output"></a>Output

```console
Train Model with training data...
Model Id: 20c3544d-97b4-49d9-b39b-dc32d85f1358
Model Status: ready
Training started on: 2020-08-31T16:52:09Z
Training completed on: 2020-08-31T16:52:23Z

Recognized Fields:
The subModel has form type form-0
The model found field 'field-0' with label: Address:
The model found field 'field-1' with label: Charges
The model found field 'field-2' with label: Invoice Date
The model found field 'field-3' with label: Invoice Due Date
The model found field 'field-4' with label: Invoice For:
The model found field 'field-5' with label: Invoice Number
The model found field 'field-6' with label: VAT ID
```

## <a name="analyze-forms-with-a-custom-model"></a>Analizzare i moduli con un modello personalizzato

Questa sezione illustra come estrarre informazioni chiave-valore e altro contenuto dai tipi di modulo personalizzati, usando i modelli a cui è stato eseguito il training con moduli personalizzati.

> [!IMPORTANT]
> Per implementare questo scenario è necessario avere già eseguito il training di un modello in modo da poter passare il relativo ID al metodo seguente. Vedere la sezione [Eseguire il training di un modello](#train-a-model-without-labels).

Si userà il metodo **beginRecognizeCustomFormsFromUrl**. 

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_analyze_call)]

> [!TIP]
> È anche possibile analizzare un file locale. Vedere i metodi [FormRecognizerClient](https://docs.microsoft.com/java/api/com.azure.ai.formrecognizer.formrecognizerclient?view=azure-java-stable), ad esempio **beginRecognizeCustomForms**. In alternativa, per gli scenari con immagini locali, vedere il codice di esempio in [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md).

Il valore restituito è una raccolta di oggetti **RecognizedForm** , uno per ogni pagina del documento inviato. Il codice seguente stampa i risultati dell'analisi nella console. Stampa ogni campo riconosciuto e il valore corrispondente, insieme a un punteggio di attendibilità.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_analyze_print)]


### <a name="output"></a>Output

```console
Analyze PDF form...
----------- Recognized custom form info for page 0 -----------
Form type: form-0
Field 'field-0' has label 'Address:' with a confidence score of 0.91.
Field 'field-1' has label 'Invoice For:' with a confidence score of 1.00.
Field 'field-2' has label 'Invoice Number' with a confidence score of 1.00.
Field 'field-3' has label 'Invoice Date' with a confidence score of 1.00.
Field 'field-4' has label 'Invoice Due Date' with a confidence score of 1.00.
Field 'field-5' has label 'Charges' with a confidence score of 1.00.
Field 'field-6' has label 'VAT ID' with a confidence score of 1.00.
```


## <a name="manage-custom-models"></a>Gestire i modelli personalizzati

Questa sezione illustra come gestire modelli personalizzati archiviati nell'account. Il codice seguente esegue tutte le attività di gestione dei modelli in un singolo metodo, come esempio. Per iniziare, copiare la firma del metodo seguente:

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage)]


### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Verificare il numero dei modelli all'interno dell'account della risorsa FormRecognizer

Il blocco di codice seguente consente di controllare il numero di modelli salvati nell'account di Riconoscimento modulo e di confrontarli con il limite dell'account.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage_count)]


#### <a name="output"></a>Output 

```console
The account has 12 custom models, and we can have at most 250 custom models
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Elencare i modelli archiviati attualmente nell'account della risorsa

Il blocco di codice seguente elenca i modelli attuali presenti nell'account e stampa i relativi dettagli nella console.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage_list)]


#### <a name="output"></a>Output 

Questa risposta è stata troncata per migliorare la leggibilità.

```console
We have following models in the account:
Model Id: 0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Model Id: 0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Model Status: ready
Training started on: 2020-06-04T18:33:08Z
Training completed on: 2020-06-04T18:33:10Z
Custom Model Form type: form-0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Custom Model Accuracy: 1.00
Field Text: invoice date
Field Accuracy: 1.00
Field Text: invoice number
Field Accuracy: 1.00
...
```

### <a name="delete-a-model-from-the-resource-account"></a>Eliminare un modello dall'account della risorsa

È inoltre possibile eliminare un modello dall'account facendo riferimento al relativo ID.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage_delete)]



## <a name="run-the-application"></a>Eseguire l'applicazione

Tornare alla directory principale del progetto. Compilare quindi l'app con il comando seguente:

```console
gradle build
```

Eseguire l'applicazione con il comando `run`:

```console
gradle run
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una sottoscrizione a Servizi cognitivi, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate.

* [Portale](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfaccia della riga di comando di Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Risoluzione dei problemi

I client di Riconoscimento modulo generano le eccezioni `ErrorResponseException`. Se ad esempio si prova a specificare un URL di origine file non valido, verrà generata un'eccezione `ErrorResponseException` con un messaggio che indica la causa del problema. Nel frammento di codice seguente l'errore viene gestito normalmente rilevando l'eccezione e visualizzando informazioni aggiuntive sull'errore.

```java Snippet:FormRecognizerBadRequest
try {
    formRecognizerClient.beginRecognizeContentFromUrl("invalidSourceUrl");
} catch (ErrorResponseException e) {
    System.out.println(e.getMessage());
}
```

### <a name="enable-client-logging"></a>Abilitare la registrazione client

Azure SDK per Java offre un'esperienza di registrazione coerente per facilitare la l'individuazione degli errori dell'applicazione e velocizzarne la risoluzione. I log prodotti acquisiranno il flusso di un'applicazione prima di raggiungere lo stato terminale per facilitare l'individuazione del problema radice. Per informazioni sull'abilitazione della registrazione, vedere la pagina [wiki](https://github.com/Azure/azure-sdk-for-java/wiki/Logging-with-Azure-SDK) corrispondente.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stata usata la libreria client di Riconoscimento modulo per JavaP per eseguire il training di modelli e analizzare i moduli in modi diversi. In seguito, è possibile ottenere suggerimenti per creare un set di dati di training migliore e produrre modelli più accurati.

> [!div class="nextstepaction"]
> [Creare un set di dati di training](../../build-training-data-set.md)

* [Informazioni su Riconoscimento modulo](../../overview.md)
* Il codice di esempio di questa guida (e altro ancora) è reperibile su [GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples).