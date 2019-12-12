---
title: "Guida introduttiva: Libreria client dell'API Viso per Python | Microsoft Docs"
description: Questo articolo consente di iniziare a usare la libreria client Face per Python per rilevare, trovare un tipo simile, identificare, verificare e altro ancora.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: cd797567b381fb89c568b06dc8b056648e5c734a
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977812"
---
# <a name="quickstart-face-client-library-for-python"></a>Guida introduttiva: Libreria client dell'API Viso per Python

Introduzione alla libreria client dell'API Viso per Python. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base. Il servizio API Viso fornisce l'accesso ad algoritmi avanzati per il rilevamento e il riconoscimento dei visi umani nelle immagini.

Usare la libreria client dell'API Viso per Python per:

* Rilevare i visi in un'immagine
* Individuazione di visi simili
* Creare ed eseguire il training di un gruppo di persone
* Identificare un viso
* Verificare i visi
* Creare uno snapshot per la migrazione dei dati

[Documentazione di riferimento](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-face) | [Pacchetto (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-face/) | [Esempi](https://docs.microsoft.com/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Configurazione

### <a name="create-a-face-azure-resource"></a>Creare una risorsa di Azure Viso

I Servizi cognitivi di Azure sono rappresentati dalle risorse di Azure a cui si effettua la sottoscrizione. Creare una risorsa Viso usando il [portale di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) o l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) nel computer locale. È anche possibile:

* Ottenere un [codice di valutazione gratuito](https://azure.microsoft.com/try/cognitive-services/#decision) valido per 7 giorni. Dopo aver eseguito l'iscrizione, sarà disponibile sul [sito Web di Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Visualizzare questa risorsa nel [portale di Azure](https://portal.azure.com/)

Dopo aver ottenuto una chiave dalla sottoscrizione di valutazione o dalla risorsa, [creare una variabile di ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) per la chiave, denominata `FACE_SUBSCRIPTION_KEY`.
 
### <a name="create-a-new-python-application"></a>Creare una nuova applicazione Python

Creare un nuovo script Python, ad esempio *quickstart-file.py*. Aprirlo quindi in un editor o un IDE a scelta e importare le librerie seguenti.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_imports)]

Quindi, creare le variabili per l'endpoint e la chiave di Azure della risorsa. Potrebbe essere necessario modificare la prima parte dell'endpoint (`westus`) in modo che corrisponda alla sottoscrizione in uso.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_subvars)]

> [!NOTE]
> Se la variabile di ambiente è stata creata dopo l'avvio dell'applicazione, per accedervi sarà necessario chiudere e riaprire l'editor, l'IDE o la shell in cui è in esecuzione.

### <a name="install-the-client-library"></a>Installare la libreria client

È possibile installare la libreria client con:

```console
pip install --upgrade azure-cognitiveservices-vision-face
```

## <a name="object-model"></a>Modello a oggetti

Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità di Face Python SDK.

|NOME|DESCRIZIONE|
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
* [Creare uno snapshot per la migrazione dei dati](#take-a-snapshot-for-data-migration)

## <a name="authenticate-the-client"></a>Autenticare il client

> [!NOTE]
> Questo argomento di avvio rapido presuppone che sia stata [creata una variabile di ambiente](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) per la chiave del servizio Viso, denominata `FACE_SUBSCRIPTION_KEY`.

Creare un'istanza di un client con l'endpoint e la chiave. Creare un oggetto [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) con la chiave e usarlo con l'endpoint per creare un oggetto [FaceClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python).

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_auth)]

## <a name="detect-faces-in-an-image"></a>Rilevare i visi in un'immagine

Il codice seguente rileva un viso in un'immagine remota. Visualizza l'ID del viso rilevato nella console e lo archivia anche nella memoria del programma. Rileva quindi i visi in un'immagine con più persone e visualizza anche gli ID di questi visi nella console. Cambiando i parametri nel metodo [detect_with_url](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#detect-with-url-url--return-face-id-true--return-face-landmarks-false--return-face-attributes-none--recognition-model--recognition-01---return-recognition-model-false--detection-model--detection-01---custom-headers-none--raw-false----operation-config-), è possibile restituire informazioni diverse con ogni oggetto [DetectedFace](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python).

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detect)]

Per altri scenari di rilevamento, vedere il codice di esempio su [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py).

### <a name="display-and-frame-faces"></a>Visualizzare e incorniciare i visi

Il codice seguente visualizza l'immagine specificata sullo schermo e disegna rettangoli attorno ai visi usando la proprietà DetectedFace.faceRectangle.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_frame)]

![Una giovane donna con un rettangolo rosso tracciato attorno al viso](../images/face-rectangle-result.png)

## <a name="find-similar-faces"></a>Individuazione di visi simili

Il codice seguente cerca le corrispondenze di un singolo viso rilevato in un set di altri visi. Quando trova una corrispondenza, visualizza le coordinate del rettangolo del viso corrispondente nella console. 

### <a name="find-matches"></a>Trovare le corrispondenze

Prima di tutto eseguire il codice riportato nella sezione precedente ([Rilevare i visi in un'immagine](#detect-faces-in-an-image)) per salvare un riferimento a un singolo viso. Eseguire quindi il codice seguente per ottenere riferimenti a più visi in un'immagine di gruppo.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detectgroup)]

Aggiungere ora il blocco di codice seguente per trovare le istanze del primo viso nel gruppo. Vedere il metodo [find_similar](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#find-similar-face-id--face-list-id-none--large-face-list-id-none--face-ids-none--max-num-of-candidates-returned-20--mode--matchperson---custom-headers-none--raw-false----operation-config-) per informazioni su come modificare questo comportamento.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar)]

### <a name="print-matches"></a>Visualizzare le corrispondenze

Usare il codice seguente per visualizzare i dettagli delle corrispondenze nella console.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar_print)]

## <a name="create-and-train-a-person-group"></a>Creare ed eseguire il training di un gruppo di persone

Il codice seguente crea un **PersonGroup** con tre diversi oggetti **Person**. Associa ogni oggetto **Person** a un set di immagini di esempio e quindi esegue il training per essere in grado di riconoscere ogni persona. 

### <a name="create-persongroup"></a>Creare l'oggetto PersonGroup

Per eseguire i passaggi di questo scenario, è necessario salvare le immagini seguenti nella directory radice del progetto: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images.

Questo gruppo di immagini contiene tre set di immagini di visi corrispondenti a tre persone diverse. Il codice definirà tre oggetti **Person** e li assocerà a file di immagine che iniziano con `woman`, `man` e `child`.

Dopo aver configurato le immagini, definire un'etichetta all'inizio dello script per l' oggetto **PersonGroup** che verrà creato.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroupvars)]

Aggiungere quindi il codice seguente in fondo allo script. Questo codice crea un oggetto **PersongGroup** e tre oggetti **Person**.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_create)]

### <a name="assign-faces-to-persons"></a>Assegnare i visi alle persone

Il codice seguente ordina le immagini in base al prefisso, rileva i visi e li assegna a ogni oggetto **Person**.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_assign)]

### <a name="train-persongroup"></a>Eseguire il training di PersonGroup

Una volta assegnati i visi, è necessario eseguire il training di **PersonGroup** in modo che possa identificare le funzionalità visive associate a ogni oggetto **Person**. Il codice seguente chiama il metodo **train** asincrono ed esegue il polling del risultato, visualizzando lo stato nella console.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_train)]

## <a name="identify-a-face"></a>Identificare un viso

Il codice seguente cerca di identificare l'identità di ogni persona in un'immagine con più visi. Confronta ogni viso rilevato con un **PersonGroup**, un database di oggetti **Person** diversi le cui caratteristiche del viso sono note.

> [!IMPORTANT]
> Prima di eseguire questo esempio occorre eseguire il codice in [Creare ed eseguire il training di un gruppo di persone](#create-and-train-a-person-group).

### <a name="get-a-test-image"></a>Ottenere un'immagine di test

Il codice seguente cerca nella radice del progetto l'immagine _test-image-person-group.jpg_ e rileva i visi nell'immagine. Questa immagine è reperibile con le immagini usate per la gestione di **PersonGroup**: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify_testimage)]

### <a name="identify-faces"></a>Identificare visi

Il metodo **identify** confronta una matrice di visi rilevati con un **PersonGroup**. Se riesce a stabilire una corrispondenza tra un viso rilevato e un oggetto **Person**, salva il risultato. Questo codice visualizza i risultati dettagliati delle corrispondenze nella console.

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

## <a name="take-a-snapshot-for-data-migration"></a>Creare uno snapshot per la migrazione dei dati

La funzionalità Snapshot consente di spostare i dati sui visi salvati, ad esempio un **PersonGroup** sottoposto a training, in una sottoscrizione diversa dell'API Viso di Servizi cognitivi di Azure. È consigliabile usare questa funzionalità se, ad esempio, è stato creato un oggetto **PersonGroup** usando una sottoscrizione di valutazione gratuita e ora si vuole eseguirne la migrazione a una sottoscrizione a pagamento. Per un'ampia panoramica della funzionalità Snapshot, vedere [Eseguire la migrazione dei dati sui visi in una sottoscrizione dell'API Viso diversa](../Face-API-How-to-Topics/how-to-migrate-face-data.md).

In questo esempio si eseguirà la migrazione del **PersonGroup** creato in [Creare ed eseguire il training di un gruppo di persone](#create-and-train-a-person-group). È possibile completare prima quella sezione o usare i propri costrutti di dati sui visi.

### <a name="set-up-target-subscription"></a>Configurare la sottoscrizione di destinazione

Prima di tutto è necessario avere una seconda sottoscrizione di Azure con una risorsa Viso. A tale scopo, seguire la procedura descritta nella sezione [Configurazione](#setting-up). 

Quindi, creare le variabili seguenti nella parte iniziale dello script. Sarà anche necessario creare nuove variabili di ambiente per l'ID sottoscrizione dell'account Azure, oltre alla chiave, all'endpoint e all'ID sottoscrizione del nuovo account (di destinazione). 

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshotvars)]

### <a name="authenticate-target-client"></a>Autenticare il client di destinazione

Più avanti nello script salvare l'oggetto client corrente come client di origine, quindi autenticare un nuovo oggetto client per la sottoscrizione di destinazione. 

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_auth)]

### <a name="use-a-snapshot"></a>Usare uno snapshot

Il resto delle operazioni snapshot viene eseguita all'interno di una funzione asincrona. 

1. Il primo passaggio consiste nel **creare** lo snapshot, che salva i dati relativi ai visi della sottoscrizione originale in una posizione cloud temporanea. Questo metodo restituisce un ID da usare per eseguire query sullo stato dell'operazione.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_take)]

1. Successivamente, eseguire query sull'ID fino al completamento dell'operazione.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_wait)]

    Questo codice usa la funzione `wait_for_operation`, che deve essere definita separatamente:

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_waitforop)]

1. Tornare alla funzione asincrona. Usare l'operazione **apply** per scrivere i dati relativi ai visi nella sottoscrizione di destinazione. Questo metodo restituisce anche un ID.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_apply)]

1. Usare la funzione `wait_for_operation` per eseguire di nuovo query sull'ID fino al completamento dell'operazione.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_wait2)]

Una volta completati questi passaggi, sarà possibile accedere ai costrutti di dati sui visi dalla nuova sottoscrizione (di destinazione).

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione con il comando `python` nel file quickstart.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una sottoscrizione a Servizi cognitivi, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate.

* [Portale](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#clean-up-resources)
* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli#clean-up-resources)

Se è stato creato un oggetto **PersonGroup** in questa guida di avvio rapido e si vuole eliminarlo, eseguire il codice seguente nello script:

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletegroup)]

Se è stata eseguita la migrazione dei dati usando la funzionalità Snapshot in questa guida di avvio rapido, sarà anche necessario eliminare il **PersonGroup** salvato nella sottoscrizione di destinazione.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletetargetgroup)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come usare la libreria dell'API Viso per Python per eseguire attività di base. Successivamente, esplorare la documentazione di riferimento per altre informazioni sulla libreria.

> [!div class="nextstepaction"]
> [Informazioni di riferimento per l'API Viso (Python)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python)

* [Informazioni sull'API Viso](../overview.md)
* Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py).