---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 10/25/2020
ms.openlocfilehash: b35b383eb93b0c451fcb98c66538a4335480a649
ms.sourcegitcommit: 9706bee6962f673f14c2dc9366fde59012549649
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94625451"
---
Introduzione alla libreria client di Visione personalizzata per Python. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per creare un modello di rilevamento oggetti. Si creerà un progetto, si aggiungeranno tag, si eseguirà il training del progetto e si userà l'URL dell'endpoint di stima del progetto per testarlo a livello di codice. Usare questo esempio come modello per la creazione di un'app di riconoscimento immagini personalizzata.

> [!NOTE]
> Se si intende creare un modello di rilevamento oggetti ed eseguirne il training _senza_ scrivere codice, vedere invece le [indicazioni basate sul browser](../../get-started-build-detector.md).

Usare la libreria client di Visione personalizzata per Python per:

* Creare un nuovo progetto Visione personalizzata
* Aggiungere tag al progetto
* Caricare e contrassegnare le immagini
* Eseguire il training del progetto
* Pubblicare l'iterazione corrente
* Testare l'endpoint di stima

[Documentazione di riferimento](/python/api/overview/azure/cognitiveservices/customvision?view=azure-python) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-customvision/azure/cognitiveservices/vision/customvision) | [Pacchetto (PyPI)](https://pypi.org/project/azure-cognitiveservices-vision-customvision/) | [Esempi](/samples/browse/?languages=python&products=azure&term=vision&terms=vision)

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
* Dopo aver creato la sottoscrizione di Azure, <a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title="creare una risorsa Visione personalizzata"  target="_blank">creare una risorsa Visione personalizzata <span class="docon docon-navigate-external x-hidden-focus"></span></a> nel portale di Azure per creare la risorsa di training e di previsione e ottenere le chiavi e l'endpoint. Attendere che venga distribuita e fare clic sul pulsante **Vai alla risorsa**.
    * La chiave e l'endpoint delle risorse create sono necessari per connettere l'applicazione a Visione personalizzata. Le chiavi e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
    * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.

## <a name="setting-up"></a>Configurazione

### <a name="install-the-client-library"></a>Installare la libreria client

Per scrivere un'app di analisi immagini con Visione personalizzata per Python, è necessaria la libreria client di Visione personalizzata. Dopo l'installazione di Python, eseguire il comando seguente in PowerShell o in una finestra della console:

```powershell
pip install azure-cognitiveservices-vision-customvision
```

### <a name="create-a-new-python-application"></a>Creare una nuova applicazione Python

Creare un nuovo file Python e importare le librerie seguenti.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_imports)]

> [!TIP]
> Si vuole visualizzare l'intero file di codice dell'argomento di avvio rapido? È possibile trovarlo [in GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py), che contiene gli esempi di codice di questo argomento.

Creare le variabili per l'endpoint e le chiavi della sottoscrizione di Azure della risorsa.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_creds)]


> [!IMPORTANT]
> Accedere al portale di Azure. Se le risorse Visione personalizzata create nella sezione **Prerequisiti** sono state distribuite correttamente, fare clic sul pulsante **Vai alla risorsa** in **Passaggi successivi**. Le chiavi e l'endpoint saranno disponibili nella pagina **Chiavi ed endpoint** della risorsa in **Gestione risorse**. Sarà necessario recuperare sia la chiave di training che la chiave di previsione.
>
> È possibile trovare il valore dell'ID della risorsa di previsione nella scheda **Panoramica** della risorsa, indicato come **ID sottoscrizione**.
>
> Al termine, ricordarsi di rimuovere le chiavi dal codice e di non renderle mai pubbliche. Per la produzione, è consigliabile usare un modo sicuro per archiviare e accedere alle credenziali, Per altre informazioni, vedere l'articolo sulla [sicurezza](../../../../cognitive-services/cognitive-services-security.md) di Servizi cognitivi.

## <a name="object-model"></a>Modello a oggetti

|Nome|Description|
|---|---|
|[CustomVisionTrainingClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient?view=azure-python) | Questa classe gestisce la creazione, il training e la pubblicazione dei modelli. |
|[CustomVisionPredictionClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient?view=azure-python)| Questa classe gestisce l'esecuzione di query sui modelli per le previsioni di rilevamento oggetti.|
|[ImagePrediction](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.models.imageprediction?view=azure-python)| Questa classe definisce una singola previsione di oggetto in una singola immagine. Include le proprietà per l'ID e il nome dell'oggetto, la posizione del rettangolo di delimitazione dell'oggetto e un punteggio di attendibilità.|

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le attività seguenti con la libreria client di Visione personalizzata per Python:

* [Autenticare il client](#authenticate-the-client)
* [Creare un nuovo progetto Visione personalizzata](#create-a-new-custom-vision-project)
* [Aggiungere tag al progetto](#add-tags-to-the-project)
* [Caricare e contrassegnare le immagini](#upload-and-tag-images)
* [Eseguire il training del progetto](#train-the-project)
* [Pubblicare l'iterazione corrente](#publish-the-current-iteration)
* [Testare l'endpoint di stima](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>Autenticare il client

Creare un'istanza dei client di training e di previsione con l'endpoint e le chiavi. Creare oggetti **ApiKeyServiceClientCredentials** con le chiavi e usarli con l'endpoint per creare un oggetto [CustomVisionTrainingClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient?view=azure-python) e [CustomVisionPredictionClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient?view=azure-python).

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_auth)]


## <a name="create-a-new-custom-vision-project"></a>Creare un nuovo progetto Visione personalizzata

Per creare un nuovo progetto di Servizio visione artificiale personalizzato, aggiungere il codice seguente allo script. 

Per specificare altre opzioni quando si crea il progetto, come illustrato in [Creare un rilevatore](../../get-started-build-detector.md) nella guida al portale Web, vedere il metodo [create_project](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin?view=azure-python#create-project-name--description-none--domain-id-none--classification-type-none--target-export-platforms-none--custom-headers-none--raw-false----operation-config-&preserve-view=true).  

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_create)]


## <a name="add-tags-to-the-project"></a>Aggiungere tag al progetto

Per creare i tag degli oggetti nel progetto, aggiungere il codice seguente:

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_tags)]


## <a name="upload-and-tag-images"></a>Caricare e contrassegnare le immagini

Scaricare prima di tutto le immagini di esempio per questo progetto. Salvare il contenuto della [cartella immagini di esempio](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ObjectDetection/Images) nel dispositivo locale.

Quando si aggiungono tag alle immagini nei progetti di rilevamento degli oggetti, è necessario specificare l'area di ogni oggetto contrassegnato usando coordinate normalizzate. Il codice seguente associa ognuna delle immagini di esempio alla relativa area contrassegnata. Le aree specificano il rettangolo delimitatore in coordinate normalizzate e le coordinate sono specificate in questo ordine: sinistra, alto, larghezza, altezza.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_tagging)]

> [!NOTE]
> Se non si ha un'utilità di clic e trascinamento per contrassegnare le coordinate delle aree, è possibile usare l'interfaccia utente Web in [Customvision.ai](https://www.customvision.ai/). In questo esempio le coordinate sono già disponibili.

Usare quindi questa mappa di associazioni per caricare ogni immagine di esempio con le relative coordinate di area. È possibile caricare fino a 64 immagini in un singolo batch. Aggiungere il codice seguente.


[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_upload)]

> [!NOTE]
> Occorrerà anche modificare il percorso delle immagini in base a dove è stato scaricato il repository di esempi di Python SDK per Servizi cognitivi.

## <a name="train-the-project"></a>Eseguire il training del progetto

Questo codice crea la prima iterazione del modello di previsione. 

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_train)]

> [!TIP]
> Eseguire il training con i tag selezionati
>
> Facoltativamente, è possibile eseguire il training solo su un subset dei tag applicati. Questa operazione può essere eseguita se non è ancora stato applicato un numero sufficiente di determinati tag, ma si dispone di un numero sufficiente di altri. Nella chiamata **[train_project](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin?view=azure-python#train-project-project-id--training-type-none--reserved-budget-in-hours-0--force-train-false--notification-email-address-none--selected-tags-none--custom-headers-none--raw-false----operation-config-&preserve-view=true)** impostare il parametro opzionale *selected_tags* su un elenco di stringhe di ID dei tag da usare. Il modello eseguirà il training in modo da riconoscere solo i tag nell'elenco.

## <a name="publish-the-current-iteration"></a>Pubblicare l'iterazione corrente

L'iterazione è disponibile nell'endpoint di stima solo dopo che è stata pubblicata. Il codice seguente rende disponibile l'iterazione corrente del modello per l'esecuzione di query. 

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_publish)]

## <a name="test-the-prediction-endpoint"></a>Testare l'endpoint di stima

Per inviare un'immagine all'endpoint di stima e recuperare la stima, aggiungere il codice seguente alla fine del file:

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_test)]


## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire *CustomVisionQuickstart.py*.

```powershell
python CustomVisionQuickstart.py
```

L'output dell'applicazione dovrebbe essere visualizzato nella console. È quindi possibile verificare che l'immagine di test (disponibile in **<percorso_immagine_base>/images/Test**) sia contrassegnata in modo appropriato e che l'area di rilevamento sia corretta. È anche possibile tornare al [sito Web di Visione personalizzata](https://customvision.ai) e vedere lo stato corrente del progetto appena creato.

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>Passaggi successivi

A questo punto è stato eseguito ogni passaggio del processo di rilevamento degli oggetti nel codice. Questo esempio esegue una sola iterazione del training, ma spesso è necessario eseguire il training e il test del modello più volte per ottenere una maggiore precisione. La guida seguente è incentrata sulla classificazione delle immagini, ma i principi sono simili a quelli del rilevamento di oggetti.

> [!div class="nextstepaction"]
> [Testare un modello e ripeterne il training](../../test-your-model.md)

* Informazioni su Visione personalizzata
* Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py)
* [Documentazione di riferimento sull'SDK](/python/api/overview/azure/cognitiveservices/customvision?view=azure-python)
