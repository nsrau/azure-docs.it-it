---
title: 'Avvio rapido: Libreria client di Content Moderator per Python'
titleSuffix: Azure Cognitive Services
description: Questo argomento di avvio rapido illustra come iniziare a usare la libreria client di Azure Content Moderator per Python. È possibile integrare un software di filtro dei contenuti nell'app per garantire la conformità con le normative o mantenere l'ambiente previsto per gli utenti.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 09/15/2020
ms.custom: cog-serv-seo-aug-2020
ms.author: pafarley
ms.openlocfilehash: 64a9143e7a425b35e37f23b233c91b8e7bb70169
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92755747"
---
Introduzione alla libreria client di Azure Content Moderator per Python. Seguire questi passaggi per installare il pacchetto PiPy e provare il codice di esempio per le attività di base. 

Content Moderator è un servizio di intelligenza artificiale che consente di gestire i contenuti potenzialmente offensivi, rischiosi o in altro modo indesiderati. Usare il servizio di moderazione del contenuto basato sull'intelligenza artificiale per analizzare testo, immagini e video e applicare automaticamente i flag di contenuto. Integrare quindi l'app con lo strumento di revisione, un ambiente di moderazione online per un team di revisori umani. È possibile integrare un software di filtro dei contenuti nell'app per garantire la conformità con le normative o mantenere l'ambiente previsto per gli utenti.

Usare la libreria client di Content Moderator per Python per:

* Moderare il testo
* Usare un elenco di termini personalizzati
* Moderare le immagini
* Usare un elenco personalizzato di immagini
* Creare una revisione

[Documentazione di riferimento](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/contentmoderator?view=azure-python) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-contentmoderator) | [Pacchetto (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-contentmoderator/) | [Esempi](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
* Dopo aver creato la sottoscrizione di Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title="creare una risorsa Content Moderator"  target="_blank">creare una risorsa Content Moderator <span class="docon docon-navigate-external x-hidden-focus"></span></a> nel portale di Azure per ottenere la chiave e l'endpoint. Attendere che venga distribuita e fare clic sul pulsante **Vai alla risorsa** .
    * La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione a Content Moderator. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
    * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.


## <a name="setting-up"></a>Configurazione

### <a name="install-the-client-library"></a>Installare la libreria client

Dopo aver installato Python, è possibile installare la libreria client di Content Moderator con il comando seguente:

```console
pip install --upgrade azure-cognitiveservices-vision-contentmoderator
```

### <a name="create-a-new-python-application"></a>Creare una nuova applicazione Python

Creare un nuovo script Python in un editor o un IDE a scelta. Aggiungere quindi le istruzioni `import` seguenti all'inizio del file.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imports)]

> [!TIP]
> Si vuole visualizzare l'intero file di codice dell'argomento di avvio rapido? È possibile trovarlo [in GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ContentModerator/ContentModeratorQuickstart.py), che contiene gli esempi di codice di questo argomento.

Creare quindi le variabili per la località dell'endpoint della risorsa e la chiave.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_vars)]

> [!IMPORTANT]
> Accedere al portale di Azure. Se la risorsa Content Moderator creata nella sezione **Prerequisiti** è stata distribuita correttamente, fare clic sul pulsante **Vai alla risorsa** in **Passaggi successivi** . La chiave e l'endpoint saranno disponibili nella pagina **Chiavi ed endpoint** della risorsa in **Gestione risorse** . 
>
> Al termine, ricordarsi di rimuovere la chiave dal codice e non renderlo mai pubblico. Per la produzione, è consigliabile usare un modo sicuro per archiviare e accedere alle credenziali, ad esempio [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

## <a name="object-model"></a>Modello a oggetti

Le classi seguenti gestiscono alcune delle principali funzionalità della libreria client di Content Moderator per Python.

|Nome|Descrizione|
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

Creare un'istanza di un client con l'endpoint e la chiave. Creare un oggetto [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) con la chiave e usarlo con l'endpoint per creare un oggetto [ContentModeratorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient?view=azure-python).

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_client)]

## <a name="moderate-text"></a>Moderare il testo

Il codice seguente usa un client di Content Moderator per analizzare un corpo di testo e stampare i risultati nella console. Creare prima di tutto una cartella **text_files/** nella radice del progetto e aggiungere un file *content_moderator_text_moderation.txt* . Aggiungere un testo personalizzato in questo file oppure usare il testo di esempio seguente:

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 2065550111
```

Aggiungere un riferimento alla nuova cartella.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

Aggiungere quindi il codice seguente allo script Python.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textmod)]

## <a name="use-a-custom-terms-list"></a>Usare un elenco di termini personalizzati

Il codice seguente illustra come gestire un elenco di termini personalizzati per la moderazione del testo. È possibile usare la classe [ListManagementTermListsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementtermlistsoperations?view=azure-python) per creare un elenco di termini, gestirli singolarmente e confrontarli con altri corpi di testo.

### <a name="get-sample-text"></a>Ottenere il testo di esempio

Per usare questo esempio, è necessario creare una cartella **text_files/** nella radice del progetto e aggiungere un file *content_moderator_term_list.txt* . Questo file deve contenere testo organico che verrà verificato rispetto all'elenco di termini. È possibile usare il testo di esempio seguente:

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

### <a name="delete-a-list"></a>Eliminare un elenco

Usare il codice seguente per eliminare l'elenco di termini personalizzati.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_deletelist)]

## <a name="moderate-images"></a>Moderare le immagini

Il codice seguente usa un client di Content Moderator, insieme a un oggetto [ImageModerationOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations?view=azure-python), per analizzare le immagini e verificare la presenza di contenuti per adulti e spinti.

### <a name="get-sample-images"></a>Recupera immagini di esempio

Definire un riferimento ad alcune immagini da analizzare.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemodvars)]

Aggiungere quindi il codice seguente per eseguire l'iterazione tra le immagini. Il resto del codice contenuto in questa sezione verrà inserito in questo ciclo.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_iterate)]

### <a name="check-for-adultracy-content"></a>Verificare la presenza di contenuti per adulti o spinti

Il codice seguente verifica la presenza di contenuti per adulti o spinti nell'immagine in corrispondenza dell'URL specificato e stampa i risultati nella console. Per informazioni sul significato di questi termini, vedere la [guida concettuale alla moderazione di immagini](../../image-moderation-api.md).

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

È possibile usare la libreria client di Content Moderator per Python per inserire contenuti nello [strumento di revisione](https://contentmoderator.cognitive.microsoft.com), in modo che i moderatori possano esaminarlo. Per altre informazioni sullo strumento di revisione, vedere la [Guida concettuale dello strumento di revisione](../../review-tool-user-guide/human-in-the-loop.md).

Il codice seguente usa la classe [ReviewsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations?view=azure-python) per creare una revisione, recuperare il relativo ID e controllarne i dettagli dopo aver ricevuto input degli utenti tramite il portale Web dello strumento di revisione.

### <a name="get-review-credentials"></a>Ottenere le credenziali per la revisione

Accedere prima di tutto allo strumento di revisione e recuperare il nome del proprio team. Quindi assegnarlo alla variabile appropriata nel codice. Facoltativamente, è possibile configurare un endpoint di callback per ricevere aggiornamenti sull'attività della revisione.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_vars)]

### <a name="create-an-image-review"></a>Creare una revisione di immagini

Aggiungere il codice seguente per creare e pubblicare una revisione per l'URL di immagini specificato. Il codice salva un riferimento all'ID revisione. 
[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_create)]

### <a name="get-review-details"></a>Ottenere i dettagli della revisione

Usare il codice seguente per controllare i dettagli di una specifica revisione. Dopo aver creato la revisione, è possibile passare allo strumento di revisione e interagire con il contenuto. Per informazioni su come eseguire questa operazione, vedere la [Guida pratica per le revisioni](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/review-moderated-images). Al termine, è possibile eseguire di nuovo questo codice per recuperare i risultati del processo di revisione.

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

* [Portale](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfaccia della riga di comando di Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è appreso come usare la libreria Python di Content Moderator per eseguire attività di moderazione. Per altre informazioni sulla moderazione di immagini o di altri contenuti multimediali, è possibile leggere una guida concettuale.

> [!div class="nextstepaction"]
>[Concetti di moderazione delle immagini](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Che cos'è Azure Content Moderator?](../../overview.md)
* Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ContentModerator/ContentModeratorQuickstart.py).