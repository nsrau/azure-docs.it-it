---
title: 'Guida introduttiva: Libreria client di Content Moderator per Python | Microsoft Docs'
description: Introduzione alla libreria client di Content Moderator per Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 07/24/2019
ms.author: pafarley
ms.openlocfilehash: 62407467e3c63b1752ee6816325f097ad9a1f09e
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755260"
---
# <a name="quickstart-content-moderator-client-library-for-python"></a>Guida introduttiva: Libreria client di Content Moderator per Python

Introduzione alla libreria client di Content Moderator per Python. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base. Content Moderator è un servizio cognitivo che verifica la presenza di materiale potenzialmente offensivo, rischioso o altrimenti indesiderato in contenuti di testo, immagini e video. Quando tale materiale viene trovato, il servizio applica al contenuto apposite etichette (flag). L'app può quindi gestire il contenuto contrassegnato per garantire la conformità alle normative o gestire un ambiente con le caratteristiche previste per gli utenti.

Usare la libreria client di Content Moderator per Python per:

* [Moderare il testo](#moderate-text)
* [Usare un elenco di termini personalizzati](#use-a-custom-terms-list)
* [Moderare le immagini](#moderate-images)
* [Usare un elenco personalizzato di immagini](#use-a-custom-image-list)
* [Creare una revisione](#create-a-review)

[Documentazione di riferimento](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/contentmoderator?view=azure-python) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-contentmoderator) | [Pacchetto (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-contentmoderator/) | [Esempi](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Configurazione

### <a name="create-a-content-moderator-azure-resource"></a>Creare una risorsa di Azure per Content Moderator

I Servizi cognitivi di Azure sono rappresentati dalle risorse di Azure a cui si effettua la sottoscrizione. Creare una risorsa per Content Moderator usando il [portale di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) o l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) nel computer locale. È anche possibile:

* Ottenere un [codice di valutazione gratuito](https://azure.microsoft.com/try/cognitive-services/#decision) valido per 7 giorni. Dopo aver eseguito l'iscrizione, sarà disponibile sul [sito Web di Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Visualizzare questa risorsa nel [portale di Azure](https://portal.azure.com/)

Dopo aver ottenuto una chiave dalla sottoscrizione di valutazione o dalla risorsa, [creare le variabili di ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) per la chiave e per l'URL dell'endpoint, denominate rispettivamente `CONTENT_MODERATOR_SUBSCRIPTION_KEY` e `CONTENT_MODERATOR_ENDPOINT`.
 
### <a name="create-a-python-script"></a>Creare uno script Python

Creare un nuovo script Python in un editor o un IDE a scelta. Aggiungere quindi le istruzioni `import` seguenti all'inizio del file.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imports)]

Creare quindi le variabili per la località dell'endpoint della risorsa e la chiave come variabile di ambiente. 

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_vars)]

> [!NOTE]
> Se le variabili di ambiente sono state create dopo aver avviato l'applicazione, per accedervi sarà necessario chiudere e riaprire l'editor, l'IDE o la shell che la eseguono.

### <a name="install-the-client-library"></a>Installare la libreria client

È possibile installare la libreria client di Content Moderator con il comando seguente:

```console
pip install --upgrade azure-cognitiveservices-vision-contentmoderator
```

## <a name="object-model"></a>Modello a oggetti

Le classi seguenti gestiscono alcune delle principali funzionalità di Content Moderator Python SDK.

|NOME|DESCRIZIONE|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient?view=azure-python)|Questa classe è necessaria per tutte le funzionalità di Content Moderator. È possibile crearne un'istanza con le informazioni della sottoscrizione e usarla per produrre istanze di altre classi.|
|[ImageModerationOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations?view=azure-python)|Questa classe fornisce la funzionalità per analizzare le immagini e individuare contenuti per adulti, informazioni personali o visi umani.|
|[TextModerationOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.textmoderationoperations?view=azure-python)|Questa classe fornisce la funzionalità per analizzare il testo e individuare lingua, volgarità, errori e informazioni personali.|
[ReviewsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations?view=azure-python)|Questa classe fornisce la funzionalità delle API di revisione, inclusi i metodi per la creazione di processi, flussi di lavoro personalizzati e revisioni umane.|

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le attività seguenti con la libreria client di Content Moderator per Python:

* [Autenticare il client](#authenticate-the-client)
* [Moderare il testo](#moderate-text)
* [Usare un elenco di termini personalizzati](#use-a-custom-terms-list)
* [Moderare le immagini](#moderate-images)
* [Usare un elenco personalizzato di immagini](#use-a-custom-image-list)
* [Creare una revisione](#create-a-review)

## <a name="authenticate-the-client"></a>Autenticare il client

> [!NOTE]
> Questo argomento di avvio rapido presuppone che siano state [create variabili di ambiente](../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) per la chiave e l'endpoint di Content Moderator.

Creare un'istanza di un client con l'endpoint e la chiave. Creare un oggetto [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) con la chiave e usarlo con l'endpoint per creare un oggetto [ContentModeratorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient?view=azure-python).

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_client)]

## <a name="moderate-text"></a>Moderare il testo

Il codice seguente usa un client di Content Moderator per analizzare un corpo di testo e stampare i risultati nella console. Creare prima di tutto una cartella **text_files/** nella radice del progetto e aggiungere un file *content_moderator_text_moderation.txt*. Aggiungere un testo personalizzato in questo file oppure usare il testo di esempio seguente:

```
Is this a grabage email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
Crap is the profanity here. Is this information PII? phone 3144444444
```

Aggiungere un riferimento alla nuova cartella.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

Aggiungere quindi il codice seguente allo script Python.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textmod)]

## <a name="use-a-custom-terms-list"></a>Usare un elenco di termini personalizzati

Il codice seguente illustra come gestire un elenco di termini personalizzati per la moderazione del testo. È possibile usare la classe [ListManagementTermListsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementtermlistsoperations?view=azure-python) per creare un elenco di termini, gestirli singolarmente e confrontarli con altri corpi di testo.

### <a name="get-sample-text"></a>Ottenere il testo di esempio

Per usare questo esempio, è necessario creare una cartella **text_files/** nella radice del progetto e aggiungere un file *content_moderator_term_list.txt*. Questo file deve contenere testo organico che verrà verificato rispetto all'elenco di termini. È possibile usare il testo di esempio seguente:

```
This text contains the terms "term1" and "term2".
```

Aggiungere un riferimento alla cartella se non ne è già stato definito uno.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

### <a name="create-a-list"></a>Crea un elenco

Aggiungere il codice seguente allo script Python per creare un elenco di termini personalizzati e salvare il relativo valore ID.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_create)]

### <a name="define-list-details"></a>Definire i dettagli dell'elenco

È possibile usare l'ID di un elenco per modificarne il nome e la descrizione.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_details)]

### <a name="add-a-term-to-the-list"></a>Aggiungere un termine all'elenco

Il codice seguente aggiunge i termini `"term1"` e `"term2"` all'elenco.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_add)]

### <a name="get-all-terms-in-the-list"></a>Ottenere tutti i termini dell'elenco

È possibile usare l'ID elenco per restituire tutti i termini dell'elenco.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_getterms)]

### <a name="refresh-the-list-index"></a>Aggiornare l'indice dell'elenco

Ogni volta che si aggiungono o si rimuovono termini dall'elenco, è necessario aggiornare l'indice prima di poter usare l'elenco aggiornato.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_refresh)]

### <a name="screen-text-against-the-list"></a>Confrontare il testo con l'elenco

La principale funzionalità dell'elenco di termini personalizzati consiste nel confrontare un corpo di testo con l'elenco e verificare se sono presenti termini corrispondenti. 

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_screen)]

### <a name="remove-a-term-from-a-list"></a>Rimuovere un termine da un elenco

Il codice seguente rimuove il termine `"term1"` dall'elenco.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_remove)]

### <a name="remove-all-terms-from-a-list"></a>Rimuovere tutti i termini da un elenco

Usare il codice seguente per cancellare tutti i termini da un elenco.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_removeall)]

### <a name="delete-list"></a>Eliminare l'elenco

Usare il codice seguente per eliminare l'elenco di termini personalizzati.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_deletelist)]

## <a name="moderate-images"></a>Moderare le immagini

Il codice seguente usa un client di Content Moderator, insieme a un oggetto [ImageModerationOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations?view=azure-python), per analizzare le immagini e verificare la presenza di contenuti per adulti e spinti.

### <a name="get-images"></a>Ottenere le immagini

Definire un riferimento ad alcune immagini da analizzare.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemodvars)]

Aggiungere quindi il codice seguente per eseguire l'iterazione tra le immagini. Il resto del codice contenuto in questa sezione verrà inserito in questo ciclo.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_iterate)]

### <a name="check-for-adultracy-content"></a>Verificare la presenza di contenuti per adulti o spinti

Il codice seguente verifica la presenza di contenuti per adulti o spinti nell'immagine in corrispondenza dell'URL specificato e stampa i risultati nella console. Per informazioni sul significato di questi termini, vedere la [guida concettuale alla moderazione di immagini](./image-moderation-api.md).

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_ar)]

### <a name="check-for-visible-text"></a>Verificare la presenza di testo visibile

Il codice seguente verifica la presenza di contenuto di testo visibile nell'immagine e stampa i risultati nella console.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Verificare la presenza di visi

Il codice seguente verifica la presenza di visi umani nell'immagine e stampa i risultati nella console.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_face)]

## <a name="use-a-custom-image-list"></a>Usare un elenco personalizzato di immagini

Il codice seguente illustra come gestire un elenco personalizzato di immagini per la moderazione. Questa funzionalità è utile se la piattaforma riceve spesso istanze dello stesso set di immagini da escludere. Mantenendo un elenco di queste immagini specifiche, è possibile migliorare le prestazioni. La classe [ListManagementImageListsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementimagelistsoperations?view=azure-python) consente di creare un elenco di immagini, gestirle singolarmente e confrontarle con altre immagini.

Creare le variabili di testo seguenti per archiviare gli URL delle immagini che verranno usati in questo scenario.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelistvars)]

> [!NOTE]
> Questo non è l'elenco corretto, ma un elenco informale di immagini che verranno aggiunte nella sezione `add images` del codice.


### <a name="create-an-image-list"></a>Creare un elenco di immagini

Aggiungere il codice seguente per creare un elenco di immagini e salvare un riferimento al relativo ID.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_create)]

### <a name="add-images-to-a-list"></a>Aggiungere immagini a un elenco

Il codice seguente aggiunge tutte le immagini all'elenco.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_add)]

Definire la funzione helper **add_images** in un altro punto dello script.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_addhelper)]

### <a name="get-images-in-list"></a>Ottenere le immagini dell'elenco

Il codice seguente stampa i nomi di tutte le immagini dell'elenco.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getimages)]

### <a name="update-list-details"></a>Aggiornare i dettagli dell'elenco

È possibile usare l'ID elenco per aggiornare il nome e la descrizione dell'elenco.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_updatedetails)]

### <a name="get-list-details"></a>Ottenere i dettagli dell'elenco

Usare il codice seguente per stampare i dettagli correnti dell'elenco.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getdetails)]

### <a name="refresh-the-list-index"></a>Aggiornare l'indice dell'elenco

Dopo aver aggiunto o rimosso immagini, è necessario aggiornare l'indice dell'elenco prima di poterlo usare per esaminare altre immagini.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_refresh)]

### <a name="match-images-against-the-list"></a>Confrontare le immagini con l'elenco

La funzione principale degli elenchi di immagini consiste nel confrontare le nuove immagini e verificare se sono presenti corrispondenze.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_match)]

### <a name="remove-an-image-from-the-list"></a>Rimuovere un'immagine dall'elenco

Il codice seguente rimuove una voce dall'elenco. In questo caso, si tratta di un'immagine che non corrisponde alla categoria dell'elenco.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_remove)]

### <a name="remove-all-images-from-a-list"></a>Rimuovere tutte le immagini da un elenco

Usare il codice seguente per cancellare le immagini da un elenco.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_removeall)]

### <a name="delete-the-image-list"></a>Eliminare l'elenco di immagini

Usare il codice seguente per eliminare uno specifico elenco di immagini.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_delete)]

## <a name="create-a-review"></a>Creare una revisione

È possibile usare Content Moderator Python SDK per inserire contenuti nello [strumento di revisione](https://contentmoderator.cognitive.microsoft.com), in modo che i moderatori possano esaminarlo. Per altre informazioni su questo strumento, vedere la [guida concettuale](./review-tool-user-guide/human-in-the-loop.md).

Il codice seguente usa la classe [ReviewsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations?view=azure-python) per creare una revisione, recuperare il relativo ID e controllarne i dettagli dopo aver ricevuto input degli utenti tramite il portale Web dello strumento di revisione.

### <a name="get-review-credentials"></a>Ottenere le credenziali per la revisione

Accedere prima di tutto allo strumento di revisione e recuperare il nome del proprio team. Quindi assegnarlo alla variabile appropriata nel codice. Facoltativamente, è possibile configurare un endpoint di callback per ricevere aggiornamenti sull'attività della revisione.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_vars)]

### <a name="create-an-image-review"></a>Creare una revisione di immagini

Aggiungere il codice seguente per creare e pubblicare una revisione per l'URL di immagini specificato. Il codice salva un riferimento all'ID revisione. 
[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_create)]

### <a name="get-review-details"></a>Ottenere i dettagli della revisione

Usare il codice seguente per controllare i dettagli di una specifica revisione. Dopo aver creato la revisione, è possibile passare allo strumento di revisione e interagire con il contenuto. Al termine, è possibile eseguire di nuovo questo codice per recuperare i risultati del processo di revisione.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_getdetails)]

Se in questo scenario è stato usato un endpoint di callback, si dovrebbe ricevere un evento nel formato seguente:

```console
{'callback_endpoint': 'https://requestb.in/qmsakwqm',
 'content': '',
 'content_id': '3ebe16cb-31ed-4292-8b71-1dfe9b0e821f',
 'created_by': 'cspythonsdk',
 'metadata': [{'key': 'sc', 'value': 'True'}],
 'review_id': '201901i14682e2afe624fee95ebb248643139e7',
 'reviewer_result_tags': [{'key': 'a', 'value': 'True'},
                          {'key': 'r', 'value': 'True'}],
 'status': 'Complete',
 'sub_team': 'public',
 'type': 'Image'}
```

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione con il comando `python` nel file quickstart.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una sottoscrizione a Servizi cognitivi, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate.

* [Portale](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfaccia della riga di comando di Azure](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è appreso come usare la libreria Python di Content Moderator per eseguire attività di moderazione. Per altre informazioni sulla moderazione di immagini o di altri contenuti multimediali, è possibile leggere una guida concettuale.

> [!div class="nextstepaction"]
>[Concetti di moderazione delle immagini](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Che cos'è Azure Content Moderator?](./overview.md)
* Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ContentModerator/ContentModeratorQuickstart.py).