---
title: 'Guida introduttiva: Libreria client di Visione artificiale per Python | Microsoft Docs'
description: Introduzione alla libreria client di Visione artificiale per Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: pafarley
ms.openlocfilehash: ab6a0d5c2a4c4623506d90b76b77462abb8fe4af
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719500"
---
# <a name="quickstart-computer-vision-client-library-for-python"></a>Guida introduttiva: Libreria client di Visione artificiale per Python

Il servizio Visione artificiale consente agli sviluppatori di accedere ad algoritmi avanzati per l'elaborazione delle immagini e la restituzione delle informazioni. Gli algoritmi di Visione artificiale analizzano il contenuto di un'immagine in diversi modi, in base alle caratteristiche visive a cui si è interessati.

Usare la libreria client di Visione artificiale per Python per:

* Analizzare un'immagine per trovare tag, descrizioni di testo, visi, contenuto per adulti e altro ancora.
* Riconoscere il testo stampato e scritto a mano con l'API di lettura batch.

> [!NOTE]
> Negli scenari di questo argomento di avvio rapido si usano URL di immagini remote. Per un esempio di codice che esegue le stesse operazioni con le immagini locali, vedere [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py).

[Documentazione di riferimento](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-computervision) | [Pacchetto (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-computervision/) | [Esempi](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Configurazione

### <a name="create-a-computer-vision-azure-resource"></a>Creare una risorsa di Visione artificiale in Azure

I Servizi cognitivi di Azure sono rappresentati dalle risorse di Azure a cui si effettua la sottoscrizione. Creare una risorsa per Visione artificiale usando il [portale di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) o l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) nel computer locale. È anche possibile:

* Ottenere un [codice di valutazione gratuito](https://azure.microsoft.com/try/cognitive-services/#decision) valido per 7 giorni. Dopo aver eseguito l'iscrizione, sarà disponibile sul [sito Web di Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Visualizzare questa risorsa nel [portale di Azure](https://portal.azure.com/)

Dopo aver ottenuto una chiave dalla sottoscrizione di valutazione o dalla risorsa, [creare le variabili di ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) per la chiave e per l'URL dell'endpoint, denominate rispettivamente `COMPUTER_VISION_SUBSCRIPTION_KEY` e `COMPUTER_VISION_ENDPOINT`.
 
### <a name="create-a-new-python-application"></a>Creare una nuova applicazione Python

Creare un nuovo script Python, ad esempio *quickstart-file.py*. Aprirlo quindi in un editor o un IDE a scelta e importare le librerie seguenti.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_imports)]

Quindi, creare le variabili per l'endpoint e la chiave di Azure della risorsa.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_vars)]

> [!NOTE]
> Se la variabile di ambiente è stata creata dopo l'avvio dell'applicazione, per accedervi sarà necessario chiudere e riaprire l'editor, l'IDE o la shell in cui è in esecuzione.

### <a name="install-the-client-library"></a>Installare la libreria client

È possibile installare la libreria client con:

```console
pip install --upgrade azure-cognitiveservices-vision-computervision
```

## <a name="object-model"></a>Modello a oggetti

Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità dell'SDK Visione artificiale per Python.

|NOME|DESCRIZIONE|
|---|---|
|[ComputerVisionClientOperationsMixin](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin?view=azure-python)| Questa classe gestisce direttamente tutte le operazioni per le immagini, come l'analisi, il rilevamento del testo e la generazione di anteprime.|
| [ComputerVisionClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python) | Questa classe è necessaria per tutte le funzionalità di Visione artificiale. È possibile crearne un'istanza con le informazioni della sottoscrizione e usarla per produrre istanze di altre classi. Implementa **ComputerVisionClientOperationsMixin**.|
|[VisualFeatureTypes](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-python)| Questa enumerazione definisce i diversi tipi di analisi delle immagini che è possibile eseguire in un'operazione di analisi standard. Specificare un set di valori di **VisualFeatureTypes** a seconda delle esigenze. |

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le attività seguenti con la libreria client di Visione artificiale per Python:

* [Autenticare il client](#authenticate-the-client)
* [Analizzare un'immagine](#analyze-an-image)
* [Leggere il testo stampato e scritto a mano](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Autenticare il client

> [!NOTE]
> Questo argomento di avvio rapido presuppone che sia stata [creata una variabile di ambiente](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) per la chiave di Visione artificiale, denominata `COMPUTER_VISION_SUBSCRIPTION_KEY`.

Creare un'istanza di un client con l'endpoint e la chiave. Creare un oggetto [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) con la chiave e usarlo con l'endpoint per creare un oggetto [ComputerVisionClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python).

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_client)]

## <a name="analyze-an-image"></a>Analizzare un'immagine

Salvare un riferimento all'URL di un'immagine da analizzare.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_remoteimage)]

### <a name="get-image-description"></a>Ottenere la descrizione dell'immagine

Il codice seguente ottiene l'elenco di didascalie generate per l'immagine. Per altre informazioni, vedere [Descrivere le immagini](../concept-describing-images.md).

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_describe)]

### <a name="get-image-category"></a>Ottenere la categoria dell'immagine

Il codice seguente ottiene la categoria rilevata dell'immagine. Per altre informazioni, vedere [Categorizzare le immagini](../concept-categorizing-images.md).

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_categorize)]

### <a name="get-image-tags"></a>Ottenere i tag delle immagini

Il codice seguente ottiene il set di tag rilevati nell'immagine. Per altre informazioni, vedere [Tag di contenuto](../concept-tagging-images.md).

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_tags)]

### <a name="detect-objects"></a>Rilevare oggetti

Il codice seguente rileva gli oggetti comuni nell'immagine e li stampa nella console. Per altre informazioni, vedere [Rilevamento di oggetti](../concept-object-detection.md).

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_objects)]

### <a name="detect-brands"></a>Rilevare marchi

Il codice seguente rileva i marchi e i logo aziendali nell'immagine e li stampa nella console. Per altre informazioni, vedere [Rilevamento di marchi](../concept-brand-detection.md).

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_brands)]

### <a name="detect-faces"></a>Rilevare visi

Il codice seguente restituisce i visi rilevati nell'immagine con le rispettive coordinate del rettangolo e determinati attributi del viso. Per altre informazioni, vedere [Rilevamento viso](../concept-detecting-faces.md).

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Rilevare contenuto per adulti, spinto o cruento

Il codice seguente stampa la presenza rilevata di contenuto per adulti nell'immagine. Per altre informazioni, vedere [Contenuto per adulti, spinto o cruento](../concept-detecting-adult-content.md).

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Ottenere la combinazione di colori dell'immagine

Il codice seguente stampa gli attributi di colore rilevati nell'immagine, ad esempio i colori dominanti e il colore principale. Per altre informazioni, vedere [Combinazioni di colori](../concept-detecting-color-schemes.md).

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Ottenere contenuto specifico del dominio

Visione artificiale può usare un modello speciale per eseguire un'ulteriore analisi delle immagini. Per altre informazioni, vedere [Contenuto specifico del dominio](../concept-detecting-domain-content.md). 

Il codice seguente analizza i dati sulle celebrità rilevate nell'immagine.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_celebs)]

Il codice seguente analizza i dati sui luoghi di interesse rilevati nell'immagine.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Ottenere il tipo di immagine

Il codice seguente stampa informazioni sul tipo di immagine, sia che si tratti di ClipArt o di un disegno lineare.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_type)]

## <a name="read-printed-and-handwritten-text"></a>Leggere il testo stampato e scritto a mano

Visione artificiale può leggere il testo visibile di un'immagine e convertirlo in un flusso di caratteri. Questa operazione viene eseguita in due parti.

### <a name="call-the-read-api"></a>Chiamare l'API di lettura

Prima di tutto, usare il codice seguente per chiamare il metodo **batch_read_file** per l'immagine specificata. Viene restituito un ID operazione e viene avviato un processo asincrono per leggere il contenuto dell'immagine.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_read_call)]

### <a name="get-read-results"></a>Ottenere risultati della lettura

Ottenere quindi l'ID operazione restituito dalla chiamata a **batch_read_file** e usarlo per eseguire una query sul servizio per ottenere i risultati dell'operazione. Il codice seguente controlla l'operazione a intervalli di un secondo fino a quando non vengono restituiti i risultati. Stampa quindi i dati di testo estratti nella console.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_read_response)]

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione con il comando `python` nel file quickstart.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una sottoscrizione a Servizi cognitivi, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate.

* [Portale](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#clean-up-resources)
* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli#clean-up-resources)


## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è appreso come usare la libreria di Visione artificiale per Python per eseguire attività di base. Successivamente, esplorare la documentazione di riferimento per altre informazioni sulla libreria.


> [!div class="nextstepaction"]
>[Informazioni di riferimento per l'API Visione artificiale (Python)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision)

* [Che cos'è l'API Visione artificiale?](../Home.md)
* Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py).
