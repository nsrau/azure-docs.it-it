---
title: "Avvio rapido: Libreria client dell'API Viso per Python"
description: Usare la libreria client di Viso per Python per rilevare visi, trovare volti simili (ricerca di volti per immagine) e identificare i visi (ricerca basata su riconoscimento facciale).
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 10/26/2020
ms.author: pafarley
ms.openlocfilehash: 86d2935cfac7ca095c918826c47dbf3f1dd2d8d3
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92886618"
---
Introduzione al riconoscimento facciale con la libreria client di Viso per Python. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base. Il servizio Viso fornisce l'accesso ad algoritmi avanzati per il rilevamento e il riconoscimento dei visi umani nelle immagini.

Usare la libreria client dell'API Viso per Python per:

* Rilevare i visi in un'immagine
* Individuazione di visi simili
* Creare ed eseguire il training di un gruppo di persone
* Identificare un viso
* Verificare i visi

[Documentazione di riferimento](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-face) | [Pacchetto (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-face/) | [Esempi](https://docs.microsoft.com/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
* Dopo aver creato la sottoscrizione di Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="creare una risorsa Viso"  target="_blank">creare una risorsa Viso <span class="docon docon-navigate-external x-hidden-focus"></span></a> nel portale di Azure per ottenere la chiave e l'endpoint. Al termine della distribuzione, fare clic su **Vai alla risorsa** .
    * La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione all'API Viso. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
    * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.


## <a name="setting-up"></a>Configurazione
 
### <a name="install-the-client-library"></a>Installare la libreria client

Dopo l'installazione di Python, è possibile installare la libreria client con:

```console
pip install --upgrade azure-cognitiveservices-vision-face
```

### <a name="create-a-new-python-application"></a>Creare una nuova applicazione Python

Creare un nuovo script Python, ad esempio *quickstart-file.py* . Aprirlo quindi in un editor o un IDE a scelta e importare le librerie seguenti.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_imports)]

> [!TIP]
> Si vuole visualizzare l'intero file di codice dell'argomento di avvio rapido? È possibile trovarlo [in GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py), che contiene gli esempi di codice di questo argomento.

Quindi, creare le variabili per l'endpoint e la chiave di Azure della risorsa.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_subvars)]

> [!IMPORTANT]
> Accedere al portale di Azure. Se la risorsa [Nome prodotto] creata nella sezione **Prerequisiti** è stata distribuita correttamente, fare clic sul pulsante **Vai alla risorsa** in **Passaggi successivi** . La chiave e l'endpoint saranno disponibili nella pagina **Chiavi ed endpoint** della risorsa in **Gestione risorse** . 
>
> Al termine, ricordarsi di rimuovere la chiave dal codice e non renderlo mai pubblico. Per la produzione, è consigliabile usare un modo sicuro per archiviare e accedere alle credenziali, ad esempio [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

## <a name="object-model"></a>Modello a oggetti

Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità della libreria client dell'API Viso per Python.

|Nome|Descrizione|
|---|---|
|[FaceClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python) | Questa classe rappresenta l'autorizzazione per l'utilizzo del servizio Viso ed è necessaria per tutte le funzionalità del servizio. È possibile crearne un'istanza con le informazioni della sottoscrizione e usarla per produrre istanze di altre classi. |
|[FaceOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python)|Questa classe gestisce le attività di rilevamento e riconoscimento di base che è possibile eseguire con i visi umani. |
|[DetectedFace](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python)|Questa classe rappresenta tutti i dati rilevati da un singolo viso in un'immagine. È possibile usarla per recuperare informazioni dettagliate sul viso.|
|[FaceListOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.facelistoperations?view=azure-python)|Questa classe gestisce i costrutti **FaceList** archiviati nel cloud, che archiviano un set assortito di visi. |
|[PersonGroupPersonOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongrouppersonoperations?view=azure-python)| Questa classe gestisce i costrutti **Person** archiviati nel cloud, che archiviano un set di visi che appartengono a una singola persona.|
|[PersonGroupOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongroupoperations?view=azure-python)| Questa classe gestisce i costrutti **PersonGroup** archiviati nel cloud, che archiviano un set di oggetti **Person** assortiti. |
|[ShapshotOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.snapshotoperations?view=azure-python)|Questa classe gestisce la funzionalità Snapshot, che può essere usata per salvare temporaneamente tutti i dati sui visi basati sul cloud ed eseguirne la migrazione in una nuova sottoscrizione di Azure. |

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le attività seguenti con la libreria client del servizio Viso per Python:

* [Autenticare il client](#authenticate-the-client)
* [Rilevare i visi in un'immagine](#detect-faces-in-an-image)
* [Individuare visi simili](#find-similar-faces)
* [Creare ed eseguire il training di un gruppo di persone](#create-and-train-a-person-group)
* [Identificare un viso](#identify-a-face)
* [Verificare i visi](#verify-faces)

## <a name="authenticate-the-client"></a>Autenticare il client

Creare un'istanza di un client con l'endpoint e la chiave. Creare un oggetto [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) con la chiave e usarlo con l'endpoint per creare un oggetto [FaceClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python).

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_auth)]

## <a name="detect-faces-in-an-image"></a>Rilevare i visi in un'immagine

Il codice seguente rileva un viso in un'immagine remota. Visualizza l'ID del viso rilevato nella console e lo archivia anche nella memoria del programma. Rileva quindi i visi in un'immagine con più persone e visualizza anche gli ID di questi visi nella console. Cambiando i parametri nel metodo [detect_with_url](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#detect-with-url-url--return-face-id-true--return-face-landmarks-false--return-face-attributes-none--recognition-model--recognition-01---return-recognition-model-false--detection-model--detection-01---custom-headers-none--raw-false----operation-config-), è possibile restituire informazioni diverse con ogni oggetto [DetectedFace](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python).

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detect)]

> [!TIP]
> È anche possibile rilevare i visi in un'immagine locale. Vedere i metodi [FaceOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python) come **detect_with_stream** .

### <a name="display-and-frame-faces"></a>Visualizzare e incorniciare i visi

Il codice seguente visualizza l'immagine specificata sullo schermo e disegna rettangoli attorno ai visi usando la proprietà DetectedFace.faceRectangle.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_frame)]

![Una giovane donna con un rettangolo rosso tracciato attorno al viso](../../images/face-rectangle-result.png)

## <a name="find-similar-faces"></a>Individuazione di visi simili

Il codice seguente confronta un singolo viso rilevato (origine) con un set di altri visi (destinazione) per trovare corrispondenze (ricerca di volti per immagine). Quando trova una corrispondenza, visualizza l'ID del viso corrispondente nella console.

### <a name="find-matches"></a>Trovare le corrispondenze

Prima di tutto eseguire il codice riportato nella sezione precedente ([Rilevare i visi in un'immagine](#detect-faces-in-an-image)) per salvare un riferimento a un singolo viso. Eseguire quindi il codice seguente per ottenere riferimenti a più visi in un'immagine di gruppo.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detectgroup)]

Aggiungere ora il blocco di codice seguente per trovare le istanze del primo viso nel gruppo. Vedere il metodo [find_similar](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#find-similar-face-id--face-list-id-none--large-face-list-id-none--face-ids-none--max-num-of-candidates-returned-20--mode--matchperson---custom-headers-none--raw-false----operation-config-) per informazioni su come modificare questo comportamento.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar)]

### <a name="print-matches"></a>Visualizzare le corrispondenze

Usare il codice seguente per visualizzare i dettagli delle corrispondenze nella console.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar_print)]

## <a name="create-and-train-a-person-group"></a>Creare ed eseguire il training di un gruppo di persone

Il codice seguente crea un **PersonGroup** con tre diversi oggetti **Person** . Associa ogni oggetto **Person** a un set di immagini di esempio e quindi esegue il training per essere in grado di riconoscere ogni persona. 

### <a name="create-persongroup"></a>Creare l'oggetto PersonGroup

Per eseguire i passaggi di questo scenario, è necessario salvare le immagini seguenti nella directory radice del progetto: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images.

Questo gruppo di immagini contiene tre set di immagini di visi corrispondenti a tre persone diverse. Il codice definirà tre oggetti **Person** e li assocerà a file di immagine che iniziano con `woman`, `man` e `child`.

Dopo aver configurato le immagini, definire un'etichetta all'inizio dello script per l' oggetto **PersonGroup** che verrà creato.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroupvars)]

Aggiungere quindi il codice seguente in fondo allo script. Questo codice crea un oggetto **PersonGroup** e tre oggetti **Person** .

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_create)]

### <a name="assign-faces-to-persons"></a>Assegnare i visi alle persone

Il codice seguente ordina le immagini in base al prefisso, rileva i visi e li assegna a ogni oggetto **Person** .

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_assign)]

> [!TIP]
> È anche possibile creare un **PersonGroup** dalle immagini remote a cui fa riferimento l'URL. Vedere i metodi [PersonGroupPersonOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongrouppersonoperations?view=azure-python), ad esempio **add_face_from_url** .

### <a name="train-persongroup"></a>Eseguire il training di PersonGroup

Una volta assegnati i visi, è necessario eseguire il training di **PersonGroup** in modo che possa identificare le funzionalità visive associate a ogni oggetto **Person** . Il codice seguente chiama il metodo **train** asincrono ed esegue il polling del risultato, visualizzando lo stato nella console.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_train)]

## <a name="identify-a-face"></a>Identificare un viso

L'operazione di identificazione acquisisce un'immagine di una persona (o di più persone) e cerca di individuare l'identità di ogni viso nell'immagine (ricerca basata su riconoscimento facciale). Confronta ogni viso rilevato con un **PersonGroup** , un database di oggetti **Person** diversi le cui caratteristiche del viso sono note.

> [!IMPORTANT]
> Prima di eseguire questo esempio occorre eseguire il codice in [Creare ed eseguire il training di un gruppo di persone](#create-and-train-a-person-group).

### <a name="get-a-test-image"></a>Ottenere un'immagine di test

Il codice seguente cerca nella radice del progetto l'immagine _test-image-person-group.jpg_ e rileva i visi nell'immagine. Questa immagine è reperibile con le immagini usate per la gestione di **PersonGroup** : https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify_testimage)]

### <a name="identify-faces"></a>Identificare visi

Il metodo **identify** confronta una matrice di visi rilevati con un **PersonGroup** . Se riesce a stabilire una corrispondenza tra un viso rilevato e un oggetto **Person** , salva il risultato. Questo codice visualizza i risultati dettagliati delle corrispondenze nella console.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify)]

## <a name="verify-faces"></a>Verificare i visi

L'operazione di verifica si basa su un Face ID e su un altro Face ID o un oggetto **Person** per determinare se appartengono alla stessa persona.

Il codice seguente rileva i visi in due immagini di origine e quindi li verifica a fronte di un viso rilevato da un'immagine di destinazione.

### <a name="get-test-images"></a>Ottenere immagini di test

I blocchi di codice seguenti dichiarano variabili che punteranno alle immagini di origine e di destinazione per l'operazione di verifica.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_baseurl)]

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_photos)]

### <a name="detect-faces-for-verification"></a>Rilevare i visi per la verifica

Il codice seguente rileva i visi nelle immagini di origine e di destinazione e li salva nelle variabili.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_detect)]

### <a name="get-verification-results"></a>Ottenere i risultati della verifica

Il codice seguente confronta ognuna delle immagini di origine con l'immagine di destinazione e stampa un messaggio che indica se appartengono alla stessa persona.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify)]

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'app di riconoscimento volto dalla directory dell'applicazione con il comando `python`.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una sottoscrizione a Servizi cognitivi, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate.

* [Portale](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#clean-up-resources)
* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli#clean-up-resources)

Se è stato creato un oggetto **PersonGroup** in questa guida di avvio rapido e si vuole eliminarlo, eseguire il codice seguente nello script:

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletegroup)]

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è appreso come usare la libreria client di Viso per Python per eseguire attività di riconoscimento facciale di base. Successivamente, esplorare la documentazione di riferimento per altre informazioni sulla libreria.

> [!div class="nextstepaction"]
> [Informazioni di riferimento per l'API Viso (Python)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python)

* [Che cos'è il servizio Viso?](../../overview.md)
* Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py).
