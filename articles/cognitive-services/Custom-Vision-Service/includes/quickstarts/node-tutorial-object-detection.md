---
author: areddish
ms.author: areddish
ms.service: cognitive-services
ms.date: 10/26/2020
ms.custom: devx-track-js
ms.openlocfilehash: 0c6c80849f48cb4f859bcbaaeb4f072eb575ba74
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021301"
---
Questa guida fornisce istruzioni e codice di esempio per iniziare a usare la libreria client di Visione personalizzata per Node.js per creare un modello di rilevamento oggetti. Si creerà un progetto, si aggiungeranno tag, si eseguirà il training del progetto e si userà l'URL dell'endpoint di stima del progetto per testarlo a livello di codice. Usare questo esempio come modello per la creazione di un'app di riconoscimento immagini personalizzata.

> [!NOTE]
> Se si intende creare un modello di rilevamento oggetti ed eseguirne il training _senza_ scrivere codice, vedere invece le [indicazioni basate sul browser](../../get-started-build-detector.md).

Usare la libreria client di Visione personalizzata per .NET per:

* Creare un nuovo progetto Visione personalizzata
* Aggiungere tag al progetto
* Caricare e contrassegnare le immagini
* Eseguire il training del progetto
* Pubblicare l'iterazione corrente
* Testare l'endpoint di stima

Documentazione di riferimento [(training)](/javascript/api/@azure/cognitiveservices-customvision-training/?view=azure-node-latest) [(previsione)](/javascript/api/@azure/cognitiveservices-customvision-prediction/?view=azure-node-latest) | Codice sorgente della libreria [(training)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-customvision-training) [(previsione)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-customvision-prediction) | Pacchetto (npm) [(training)](https://www.npmjs.com/package/@azure/cognitiveservices-customvision-training) [(previsione)](https://www.npmjs.com/package/@azure/cognitiveservices-customvision-prediction) | [Esempi](/samples/browse/?products=azure&terms=custom%20vision&languages=javascript)


## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services/)
* Versione corrente di [Node.js](https://nodejs.org/)
* Dopo aver creato la sottoscrizione di Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision"  title="creare una risorsa Visione personalizzata"  target="_blank">creare una risorsa Visione personalizzata <span class="docon docon-navigate-external x-hidden-focus"></span></a> nel portale di Azure per creare la risorsa di training e di previsione e ottenere le chiavi e l'endpoint. Attendere che venga distribuita e fare clic sul pulsante **Vai alla risorsa**.
    * La chiave e l'endpoint delle risorse create sono necessari per connettere l'applicazione a Visione personalizzata. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
    * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-nodejs-application"></a>Creare una nuova applicazione Node.js

In una finestra della console, ad esempio cmd, PowerShell o Bash, creare e passare a una nuova directory per l'app. 

```console
mkdir myapp && cd myapp
```

Eseguire il comando `npm init` per creare un'applicazione Node con un file `package.json`. 

```console
npm init
```

### <a name="install-the-client-library"></a>Installare la libreria client

Per scrivere un'app di analisi immagini con Visione personalizzata per Node.js, sono necessari i pacchetti npm di Visione personalizzata. Per installarli, eseguire questo comando in PowerShell:

```shell
npm install @azure/cognitiveservices-customvision-training
npm install @azure/cognitiveservices-customvision-prediction
```

Il file `package.json` dell'app viene aggiornato con le dipendenze.

Creare un file denominato `index.js` e importare le librerie seguenti:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_imports)]

> [!TIP]
> Si vuole visualizzare l'intero file di codice dell'argomento di avvio rapido? È possibile trovarlo [in GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js), che contiene gli esempi di codice di questo argomento.

Creare le variabili per l'endpoint e le chiavi di Azure della risorsa. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_creds)]

> [!IMPORTANT]
> Accedere al portale di Azure. Se la risorsa [Nome prodotto] creata nella sezione **Prerequisiti** è stata distribuita correttamente, fare clic sul pulsante **Vai alla risorsa** in **Passaggi successivi**. La chiave e l'endpoint saranno disponibili nella pagina **Chiavi ed endpoint** della risorsa in **Gestione risorse**. 
>
> Al termine, ricordarsi di rimuovere la chiave dal codice e non renderlo mai pubblico. Per la produzione, è consigliabile usare un modo sicuro per archiviare e accedere alle credenziali, Per altre informazioni, vedere l'articolo sulla [sicurezza](../../../../cognitive-services/cognitive-services-security.md) di Servizi cognitivi.

Aggiungere anche i campi per il nome del progetto e un parametro di timeout per le chiamate asincrone.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_vars)]

## <a name="object-model"></a>Modello a oggetti

|Nome|Descrizione|
|---|---|
|[TrainingAPIClient](/javascript/api/@azure/cognitiveservices-customvision-training/trainingapiclient?view=azure-node-latest) | Questa classe gestisce la creazione, il training e la pubblicazione dei modelli. |
|[PredictionAPIClient](/javascript/api/@azure/cognitiveservices-customvision-prediction/predictionapiclient?view=azure-node-latest)| Questa classe gestisce l'esecuzione di query sui modelli per le previsioni di rilevamento oggetti.|
|[Stima](/javascript/api/@azure/cognitiveservices-customvision-prediction/prediction?view=azure-node-latest)| Questa interfaccia definisce una singola previsione in una singola immagine. Include le proprietà per l'ID e il nome dell'oggetto e un punteggio di attendibilità.|

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le attività seguenti con la libreria client di Visione personalizzata per JavaScript:

* [Autenticare il client](#authenticate-the-client)
* [Creare un nuovo progetto Visione personalizzata](#create-a-new-custom-vision-project)
* [Aggiungere tag al progetto](#add-tags-to-the-project)
* [Caricare e contrassegnare le immagini](#upload-and-tag-images)
* [Eseguire il training del progetto](#train-the-project)
* [Pubblicare l'iterazione corrente](#publish-the-current-iteration)
* [Testare l'endpoint di stima](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>Autenticare il client

Creare istanze degli oggetti client con l'endpoint e la chiave. Creare un oggetto **ApiKeyCredentials** con la chiave e usarlo con l'endpoint per creare gli oggetti [TrainingAPIClient](/javascript/api/@azure/cognitiveservices-customvision-training/trainingapiclient?view=azure-node-latest) e [PredictionAPIClient](/javascript/api/@azure/cognitiveservices-customvision-prediction/predictionapiclient?view=azure-node-latest).

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_auth)]

## <a name="add-helper-function"></a>Aggiungere una funzione helper

Aggiungere la funzione seguente per eseguire più chiamate asincrone. Verrà usata in seguito.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>Creare un nuovo progetto Visione personalizzata

Avviare una nuova funzione in cui contenere tutte le chiamate di funzione di Visione personalizzata. Aggiungere il codice seguente per creare un nuovo progetto del servizio Visione personalizzata.


[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_create)]

## <a name="create-a-new-custom-vision-project"></a>Creare un nuovo progetto Visione personalizzata

Avviare una nuova funzione in cui contenere tutte le chiamate di funzione di Visione personalizzata. Aggiungere il codice seguente per creare un nuovo progetto del servizio Visione personalizzata.


[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_create)]

## <a name="upload-and-tag-images"></a>Caricare e contrassegnare le immagini

Scaricare prima di tutto le immagini di esempio per questo progetto. Salvare il contenuto della [cartella immagini di esempio](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ObjectDetection/Images) nel dispositivo locale.

Per aggiungere le immagini di esempio al progetto, inserire il codice seguente dopo la creazione dei tag. Questo codice carica ogni immagine con il tag corrispondente. Quando si aggiungono tag alle immagini nei progetti di rilevamento degli oggetti, è necessario specificare l'area di ogni oggetto contrassegnato usando coordinate normalizzate. Per questa esercitazione le aree sono hardcoded in linea con il codice. Le aree specificano il rettangolo delimitatore in coordinate normalizzate e le coordinate sono specificate in questo ordine: sinistra, alto, larghezza, altezza. È possibile caricare fino a 64 immagini in un singolo batch.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_upload)]


> [!IMPORTANT]
> Sarà necessario cambiare il percorso delle immagini (`sampleDataRoot`) in base a dove è stato scaricato il repository di esempi di Python SDK per Servizi cognitivi.

> [!NOTE]
> Se non si ha un'utilità di clic e trascinamento per contrassegnare le coordinate delle aree, è possibile usare l'interfaccia utente Web in [Customvision.ai](https://www.customvision.ai/). In questo esempio le coordinate sono già disponibili.


## <a name="train-the-project"></a>Eseguire il training del progetto

Questo codice crea la prima iterazione del modello di previsione. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_train)]


## <a name="publish-the-current-iteration"></a>Pubblicare l'iterazione corrente

Questo codice pubblica l'iterazione sottoposta a training nell'endpoint di previsione. Il nome assegnato all'iterazione pubblicata può essere usato per inviare le richieste di stima. L'iterazione è disponibile nell'endpoint di stima solo dopo che è stata pubblicata.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_publish)]


## <a name="test-the-prediction-endpoint"></a>Testare l'endpoint di stima

Per inviare un'immagine all'endpoint di previsione e recuperare la previsione, aggiungere il codice seguente alla funzione. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_test)]

Chiudere quindi la funzione di Visione personalizzata e chiamarla.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_function_close)]

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione con il comando `node` nel file quickstart.

```shell
node index.js
```

L'output dell'applicazione dovrebbe essere visualizzato nella console. È quindi possibile verificare che l'immagine di test (disponibile in **<sampleDataRoot>/Test/** ) sia contrassegnata in modo appropriato e che l'area di rilevamento sia corretta. È anche possibile tornare al [sito Web di Visione personalizzata](https://customvision.ai) e vedere lo stato corrente del progetto appena creato.


[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>Passaggi successivi

A questo punto è stato eseguito ogni passaggio del processo di rilevamento degli oggetti nel codice. Questo esempio esegue una sola iterazione del training, ma spesso è necessario eseguire il training e il test del modello più volte per ottenere una maggiore precisione. La guida seguente è incentrata sulla classificazione delle immagini, ma i principi sono simili a quelli del rilevamento di oggetti.

> [!div class="nextstepaction"]
> [Testare un modello e ripeterne il training](../../test-your-model.md)

* Informazioni su Visione personalizzata
* Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js)
* [Documentazione di riferimento sull'SDK (esercitazione)](/javascript/api/@azure/cognitiveservices-customvision-training/?view=azure-node-latest)
* [Documentazione di riferimento sull'SDK (previsione)](/javascript/api/@azure/cognitiveservices-customvision-prediction/?view=azure-node-latest)
