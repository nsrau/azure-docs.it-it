---
title: 'Avvio rapido: Libreria client di Riconoscimento modulo per Python'
description: Usare la libreria client di Riconoscimento modulo per Python per creare un'app per l'elaborazione di moduli che estrae coppie chiave/valore e dati di tabelle dai documenti personalizzati.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 10/26/2020
ms.author: pafarley
ms.openlocfilehash: 5ff9c95e51f63de77ca20dee965718687daae5f4
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92897699"
---
> [!IMPORTANT]
> * Il codice di questo articolo usa metodi sincroni e archiviazione con credenziali non protette per motivi di semplicità. Vedere la documentazione di riferimento di seguito. 

[Documentazione di riferimento](https://docs.microsoft.com/python/api/azure-ai-formrecognizer) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/azure/ai/formrecognizer) | [Pacchetto (PyPi)](https://pypi.org/project/azure-ai-formrecognizer/) | [Esempi](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples)

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* Un BLOB di Archiviazione di Azure contenente un set di dati di training. Consultare [Compilare un training set per un modello personalizzato](../../build-training-data-set.md) per suggerimenti e opzioni per la creazione di un set di dati di training. Per questo argomento di avvio rapido, è possibile usare i file inclusi nella cartella **Train** del [set di dati di esempio](https://go.microsoft.com/fwlink/?linkid=2090451) (scaricare ed estrarre *sample_data.zip* ).
* Dopo aver creato la sottoscrizione di Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="creare una risorsa di Riconoscimento modulo"  target="_blank">creare una risorsa di Riconoscimento modulo <span class="docon docon-navigate-external x-hidden-focus"></span></a> nel portale di Azure per ottenere la chiave e l'endpoint. Al termine della distribuzione, fare clic su **Vai alla risorsa** .
    * La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione all'API Riconoscimento modulo. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
    * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.

## <a name="setting-up"></a>Configurazione

### <a name="install-the-client-library"></a>Installare la libreria client

Dopo l'installazione di Python, è possibile installare la versione più recente della libreria client di Riconoscimento modulo con:

```console
pip install azure-ai-formrecognizer
```

### <a name="create-a-new-python-application"></a>Creare una nuova applicazione Python

Creare una nuova applicazione Python nell'ambiente di sviluppo integrato o nell'editor preferito. Importare quindi le librerie seguenti.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_imports)]

> [!TIP]
> Si vuole visualizzare l'intero file di codice dell'argomento di avvio rapido? È possibile trovarlo [in GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/FormRecognizerQuickstart.py), che contiene gli esempi di codice di questo argomento.


Creare le variabili per l'endpoint e la chiave di Azure della risorsa. 

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_creds)]


## <a name="object-model"></a>Modello a oggetti 

Con Riconoscimento modulo è possibile creare due diversi tipi di client. Il primo, `form_recognizer_client`, viene usato per eseguire query sul servizio per riconoscere campi modulo e contenuti. Il secondo, `form_training_client`, viene usato per creare e gestire modelli personalizzati da usare per migliorare il riconoscimento. 

### <a name="formrecognizerclient"></a>FormRecognizerClient
`form_recognizer_client` fornisce le operazioni per:

 * Riconoscere campi modulo e contenuti usando modelli personalizzati sottoposti a training per riconoscere i moduli personalizzati. 
 * Riconoscere i contenuti dei moduli, incluse tabelle, righe e parole, senza la necessità di eseguire il training di un modello. 
 * Riconoscere i campi comuni delle ricevute, usando un modello di ricevuta con training preliminare nel servizio Riconoscimento modulo.

### <a name="formtrainingclient"></a>FormTrainingClient
`form_training_client` fornisce le operazioni per:

* Eseguire il training di modelli personalizzati per riconoscere tutti i campi e i valori trovati nei moduli personalizzati. Per una spiegazione più dettagliata della creazione di un training set, vedere la [documentazione del servizio sul training di modelli non etichettati](#train-a-model-without-labels).
* Eseguire il training di modelli personalizzati per riconoscere i campi e i valori specificati mediante l'assegnazione di etichette ai moduli personalizzati. Per una spiegazione più dettagliata dell'applicazione di etichette a un training set, vedere la [documentazione del servizio sul training di modelli etichettati](#train-a-model-with-labels).
* Gestire i modelli creati nell'account.
* Copiare un modello personalizzato da una risorsa Riconoscimento modulo a un'altra.

> [!NOTE]
> È possibile eseguire il training dei modelli anche con un'interfaccia utente grafica, ad esempio con lo [strumento di etichettatura di Riconoscimento modulo](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/quickstarts/label-tool).

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le attività seguenti con la libreria client di Riconoscimento modulo per Python:

* [Autenticare il client](#authenticate-the-client)
* [Riconoscere il contenuto di un modulo](#recognize-form-content)
* [Riconoscere le ricevute](#recognize-receipts)
* [Eseguire il training di un modello personalizzato](#train-a-custom-model)
* [Analizzare i moduli con un modello personalizzato](#analyze-forms-with-a-custom-model)
* [Gestire i modelli personalizzati](#manage-your-custom-models)


## <a name="authenticate-the-client"></a>Autenticare il client

In questo caso si eseguirà l'autenticazione dei due oggetti client con le variabili di sottoscrizione definite in precedenza. Si userà un oggetto **AzureKeyCredential** in modo che, se necessario, sia possibile aggiornare la chiave API senza creare nuovi oggetti client.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_auth)]


## <a name="get-assets-for-testing"></a>Ottenere le risorse per il test

Sarà necessario aggiungere riferimenti agli URL per i dati di training e di test.
* Per recuperare l'URL di firma di accesso condiviso per i dati di training del modello personalizzato, aprire Microsoft Azure Storage Explorer, fare clic con il pulsante destro del mouse sul contenitore e scegliere **Ottieni firma di accesso condiviso** . Assicurarsi che le autorizzazioni **Lettura** ed **Elenco** siano selezionate e fare clic su **Crea** . A questo punto, copiare il valore dalla sezione **URL** . Dovrebbe essere in questo formato: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
* Usare il modulo di esempio e le immagini di ricevute inclusi negli esempi seguenti (disponibili anche in [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms)) oppure usare la procedura precedente per ottenere l'URL di firma di accesso condiviso di un singolo documento nell'archivio BLOB. 

> [!NOTE]
> I frammenti di codice di questa guida usano i moduli remoti a cui si accede tramite URL. Se invece si desidera elaborare i documenti del modulo locale, vedere i metodi correlati nella [documentazione di riferimento](https://docs.microsoft.com/python/api/azure-ai-formrecognizer) e negli [esempi](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples).

## <a name="recognize-form-content"></a>Riconoscere il contenuto di un modulo

È possibile usare Riconoscimento modulo per riconoscere tabelle, righe e parole nei documenti, senza dover eseguire il training di un modello.

Per riconoscere il contenuto di un file all'interno di un URL specifico, usare il metodo `begin_recognize_content_from_url`. Il valore restituito è una raccolta di oggetti `FormPage`: uno per ogni pagina nel documento inviato. Il codice seguente esegue l'iterazione di questi oggetti e stampa le coppie chiave-valore estratte con i dati della tabella.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_getcontent)]

> [!TIP]
> È anche possibile ottenere contenuto dalle immagini locali. Vedere i metodi [FormRecognizerClient](https://docs.microsoft.com/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python), ad esempio `begin_recognize_content`. In alternativa, per gli scenari con immagini locali, vedere il codice di esempio in [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples).

### <a name="output"></a>Output

```console
Table found on page 1:
Cell text: Invoice Number
Location: [Point(x=0.5075, y=2.8088), Point(x=1.9061, y=2.8088), Point(x=1.9061, y=3.3219), Point(x=0.5075, y=3.3219)]
Confidence score: 1.0

Cell text: Invoice Date
Location: [Point(x=1.9061, y=2.8088), Point(x=3.3074, y=2.8088), Point(x=3.3074, y=3.3219), Point(x=1.9061, y=3.3219)]
Confidence score: 1.0

Cell text: Invoice Due Date
Location: [Point(x=3.3074, y=2.8088), Point(x=4.7074, y=2.8088), Point(x=4.7074, y=3.3219), Point(x=3.3074, y=3.3219)]
Confidence score: 1.0

Cell text: Charges
Location: [Point(x=4.7074, y=2.8088), Point(x=5.386, y=2.8088), Point(x=5.386, y=3.3219), Point(x=4.7074, y=3.3219)]
Confidence score: 1.0

...

```

## <a name="recognize-receipts"></a>Riconoscere le ricevute

Questa sezione mostra come riconoscere ed estrarre i campi comuni dalle ricevute degli Stati Uniti, usando un modello di ricevuta con training preliminare. Per riconoscere le ricevute a partire da un URL, usare il metodo `begin_recognize_receipts_from_url`. 

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_receipts)]

> [!TIP]
> È anche possibile riconoscere le immagini di ricevute locali. Vedere i metodi [FormRecognizerClient](https://docs.microsoft.com/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python), ad esempio `begin_recognize_receipts`. In alternativa, per gli scenari con immagini locali, vedere il codice di esempio in [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples).

### <a name="output"></a>Output

```console
ReceiptType: Itemized has confidence 0.659
MerchantName: Contoso Contoso has confidence 0.516
MerchantAddress: 123 Main Street Redmond, WA 98052 has confidence 0.986
MerchantPhoneNumber: None has confidence 0.99
TransactionDate: 2019-06-10 has confidence 0.985
TransactionTime: 13:59:00 has confidence 0.968
Receipt Items:
...Item #1
......Name: 8GB RAM (Black) has confidence 0.916
......TotalPrice: 999.0 has confidence 0.559
...Item #2
......Quantity: None has confidence 0.858
......Name: SurfacePen has confidence 0.858
......TotalPrice: 99.99 has confidence 0.386
Subtotal: 1098.99 has confidence 0.964
Tax: 104.4 has confidence 0.713
Total: 1203.39 has confidence 0.774
```

## <a name="train-a-custom-model"></a>Eseguire il training di un modello personalizzato

Questa sezione illustra come eseguire il training di un modello con i dati personali. Un modello sottoposto a training restituisce dati strutturati che includono le relazioni chiave-valore del file originale. Dopo il training del modello, è possibile testarlo, ripeterne il training e infine usarlo per estrarre dati in modo affidabile da altri moduli in base alle proprie esigenze.

> [!NOTE]
> È anche possibile eseguire il training dei modelli con un'interfaccia utente grafica, ad esempio con lo [Strumento di etichettatura campioni Riconoscimento modulo](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Eseguire il training di un modello senza etichette

Eseguire il training di modelli personalizzati per riconoscere tutti i campi e i valori trovati nei moduli personalizzati, senza etichettare manualmente i documenti di training.

Il codice seguente usa il client di training con la funzione `begin_training` per eseguire il training di un modello in un determinato set di documenti. L'oggetto `CustomFormModel` restituito contiene informazioni sui tipi di modulo che il modello è in grado di riconoscere e sui campi che può estrarre da ogni tipo di modulo. Il blocco di codice seguente stampa queste informazioni all'interno della console.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_train)]


### <a name="output"></a>Output

Questo è l'output di un modello sottoposto a training con i dati di training disponibili in [Python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/training).

```console
Model ID: 628739de-779c-473d-8214-d35c72d3d4f7
Status: ready
Training started on: 2020-08-20 23:16:51+00:00
Training completed on: 2020-08-20 23:16:59+00:00

Recognized fields:
The submodel with form type 'form-0' has recognized the following fields: Additional Notes:, Address:, Company Name:, Company Phone:, Dated As:, Details, Email:, Hero Limited, Name:, Phone:, Purchase Order, Purchase Order #:, Quantity, SUBTOTAL, Seattle, WA 93849 Phone:, Shipped From, Shipped To, TAX, TOTAL, Total, Unit Price, Vendor Name:, Website:
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_4.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_5.jpg
Document status: succeeded
Document page count: 1
Document errors: []
```

### <a name="train-a-model-with-labels"></a>Eseguire il training di un modello con etichette

È inoltre possibile eseguire il training di modelli personalizzati etichettando manualmente i documenti di training. Il training con etichette consente prestazioni migliori in alcuni scenari. L'oggetto `CustomFormModel` restituito indica i campi che il modello può estrarre, oltre all'accuratezza stimata in ogni campo. Il blocco di codice seguente stampa queste informazioni all'interno della console.

> [!IMPORTANT]
> Per eseguire il training con etichette, è necessario avere file speciali di informazioni sulle etichette (`\<filename\>.pdf.labels.json`) nel contenitore di archiviazione BLOB insieme ai documenti di training. Lo [Strumento di etichettatura campioni Riconoscimento modulo](../../quickstarts/label-tool.md) fornisce un'interfaccia utente che consente di creare questi file di etichette. Una volta creati, è possibile chiamare la funzione `begin_training` con il parametro *use_training_labels* impostato su `true`.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_trainlabels)]

### <a name="output"></a>Output

Questo è l'output di un modello sottoposto a training con i dati di training disponibili in [Python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/training).

```console
Model ID: ae636292-0b14-4e26-81a7-a0bfcbaf7c91

Status: ready
Training started on: 2020-08-20 23:20:56+00:00
Training completed on: 2020-08-20 23:20:57+00:00

Recognized fields:
The submodel with form type 'form-ae636292-0b14-4e26-81a7-a0bfcbaf7c91' has recognized the following fields: CompanyAddress, CompanyName, CompanyPhoneNumber, DatedAs, Email, Merchant, PhoneNumber, PurchaseOrderNumber, Quantity, Signature, Subtotal, Tax, Total, VendorName, Website
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_4.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_5.jpg
Document status: succeeded
Document page count: 1
Document errors: []
```

## <a name="analyze-forms-with-a-custom-model"></a>Analizzare i moduli con un modello personalizzato

Questa sezione illustra come estrarre informazioni chiave-valore e altro contenuto dai tipi di modulo personalizzati, usando i modelli a cui è stato eseguito il training con moduli personalizzati.

> [!IMPORTANT]
> Per implementare questo scenario è necessario avere già eseguito il training di un modello in modo da poter passare il relativo ID al metodo seguente. Vedere la sezione [Eseguire il training di un modello](#train-a-model-without-labels).

Verrà usato il metodo `begin_recognize_custom_forms_from_url`. Il valore restituito è una raccolta di oggetti `RecognizedForm`: uno per ogni pagina nel documento inviato. Il codice seguente stampa i risultati dell'analisi nella console. Stampa ogni campo riconosciuto e il valore corrispondente, insieme a un punteggio di attendibilità.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_analyze)]

> [!TIP]
> È anche possibile analizzare le immagini locali. Vedere i metodi [FormRecognizerClient](https://docs.microsoft.com/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python), ad esempio `begin_recognize_custom_forms`. In alternativa, per gli scenari con immagini locali, vedere il codice di esempio in [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples).


### <a name="output"></a>Output

Usando il modello dell'esempio precedente viene fornito l'output seguente.

```console
Form type: form-ae636292-0b14-4e26-81a7-a0bfcbaf7c91
Field 'Merchant' has label 'Merchant' with value 'Invoice For:' and a confidence score of 0.116
Field 'CompanyAddress' has label 'CompanyAddress' with value '1 Redmond way Suite 6000 Redmond, WA' and a confidence score of 0.258
Field 'Website' has label 'Website' with value '99243' and a confidence score of 0.114
Field 'VendorName' has label 'VendorName' with value 'Charges' and a confidence score of 0.145
Field 'CompanyPhoneNumber' has label 'CompanyPhoneNumber' with value '$56,651.49' and a confidence score of 0.249
Field 'CompanyName' has label 'CompanyName' with value 'PT' and a confidence score of 0.245
Field 'DatedAs' has label 'DatedAs' with value 'None' and a confidence score of None
Field 'Email' has label 'Email' with value 'None' and a confidence score of None
Field 'PhoneNumber' has label 'PhoneNumber' with value 'None' and a confidence score of None
Field 'PurchaseOrderNumber' has label 'PurchaseOrderNumber' with value 'None' and a confidence score of None
Field 'Quantity' has label 'Quantity' with value 'None' and a confidence score of None
Field 'Signature' has label 'Signature' with value 'None' and a confidence score of None
Field 'Subtotal' has label 'Subtotal' with value 'None' and a confidence score of None
Field 'Tax' has label 'Tax' with value 'None' and a confidence score of None
Field 'Total' has label 'Total' with value 'None' and a confidence score of None
```

## <a name="manage-your-custom-models"></a>Gestire i modelli personalizzati

Questa sezione illustra come gestire modelli personalizzati archiviati nell'account. 

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Verificare il numero dei modelli all'interno dell'account della risorsa FormRecognizer

Il blocco di codice seguente consente di controllare il numero di modelli salvati nell'account di Riconoscimento modulo e di confrontarli con il limite dell'account.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_count)]


### <a name="output"></a>Output

```console
Our account has 5 custom models, and we can have at most 5000 custom models
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Elencare i modelli archiviati attualmente nell'account della risorsa

Il blocco di codice seguente elenca i modelli attuali presenti nell'account e stampa i relativi dettagli nella console. Consente inoltre di salvare un riferimento relativo al primo modello.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_list)]


### <a name="output"></a>Output

Questo è un output di esempio per l'account di test.

```console
We have models with the following ids:
453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e
628739de-779c-473d-8214-d35c72d3d4f7
ae636292-0b14-4e26-81a7-a0bfcbaf7c91
b4b5df77-8538-4ffb-a996-f67158ecd305
c6309148-6b64-4fef-aea0-d39521452699
```

### <a name="get-a-specific-model-using-the-models-id"></a>Ottenere un modello specifico con l'ID del modello

Il blocco di codice seguente usa l'ID del modello salvato dalla sezione precedente e lo usa per recuperare i dettagli sul modello.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_getmodel)]


### <a name="output"></a>Output

Questo è l'output di esempio per il modello personalizzato creato nell'esempio precedente.

```console
Model ID: ae636292-0b14-4e26-81a7-a0bfcbaf7c91
Status: ready
Training started on: 2020-08-20 23:20:56+00:00
Training completed on: 2020-08-20 23:20:57+00:00
```

### <a name="delete-a-model-from-the-resource-account"></a>Eliminare un modello dall'account della risorsa

È inoltre possibile eliminare un modello dall'account facendo riferimento al relativo ID. Questo codice elimina il modello usato nella sezione precedente.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_delete)]


## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione con il comando `python` nel file quickstart.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una sottoscrizione a Servizi cognitivi, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate.

* [Portale](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfaccia della riga di comando di Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="general"></a>Generale

La libreria client di Riconoscimento modulo genererà le eccezioni definite in [Azure Core](https://aka.ms/azsdk-python-azure-core).

### <a name="logging"></a>Registrazione

Per la registrazione questa libreria usa la [libreria di registrazione standard](https://docs.python.org/3/library/logging.html). Le informazioni di base sulle sessioni HTTP (URL, intestazioni e così via), vengono registrate a livello di informazioni.

La registrazione dettagliata a livello di debug, inclusi i corpi di richiesta-risposta e le intestazioni non modificate, può essere abilitata su un client con l'argomento parola chiave `logging_enable`:

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerLogging.py?name=snippet_logging)]


Analogamente, `logging_enable` può abilitare la registrazione dettagliata per una singola operazione, anche quando non è abilitata per il client:

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerLogging.py?name=snippet_example)]

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stata usata la libreria client di Riconoscimento modulo per Python per eseguire il training di modelli e analizzare i moduli in modi diversi. In seguito, è possibile ottenere suggerimenti per creare un set di dati di training migliore e produrre modelli più accurati.

> [!div class="nextstepaction"]
> [Creare un set di dati di training](../../build-training-data-set.md)

* [Informazioni su Riconoscimento modulo](../../overview.md)
* Il codice di esempio di questa guida è reperibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/FormRecognizerQuickstart.py).
