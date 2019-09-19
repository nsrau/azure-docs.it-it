---
title: 'Esercitazione: Generare i metadati per le immagini di Azure'
titleSuffix: Azure Cognitive Services
description: In questa esercitazione si apprenderà come integrare il servizio Visione artificiale di Azure in un'app web per generare i metadati per le immagini.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.date: 09/04/2019
ms.author: pafarley
ms.openlocfilehash: 7caf4493db32201a8e83ffb3722c80c5e9b41a8f
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71057730"
---
# <a name="tutorial-use-computer-vision-to-generate-image-metadata-in-azure-storage"></a>Esercitazione: Usare Visione artificiale per generare i metadati delle immagini in Archiviazione di Azure

In questa esercitazione si apprenderà come integrare il servizio Visione artificiale di Azure in un'app web per generare i metadati per le immagini caricate. Una guida completa alle app è reperibile nel [lab Archiviazione di Azure e Servizi cognitivi](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md) su GitHub e questa esercitazione illustra essenzialmente esercizio l'5 del lab. Può essere utile creare l'applicazione end-to-end seguendo ogni passaggio, ma se si vuole solo scoprire se è possibile integrare Visione artificiale in un'app Web esistente, leggere qui di seguito.

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Creare una risorsa di Visione artificiale in Azure
> * Eseguire l'analisi delle immagini in Archiviazione di Azure
> * Allegare i metadati alle immagini di Archiviazione di Azure
> * Controllare i metadati delle immagini con Azure Storage Explorer

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare. 

## <a name="prerequisites"></a>Prerequisiti

- [Visual Studio 2017 Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) o versione successiva, con i carichi di lavoro "Sviluppo ASP.NET e Web" e "Sviluppo di Azure".
- Un account di archiviazione di Azure con un contenitore BLOB allocato per le immagini (seguire l'[esercizio 1 del lab di Archiviazione di Azure](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise1) se occorre assistenza per questo passaggio).
- Lo strumento Azure Storage Explorer (seguire l'[esercizio 2 del lab di Archiviazione di Azure](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise2) se occorre assistenza per questo passaggio).
- Un'applicazione Web ASP.NET con l'accesso ad Archiviazione di Azure (seguire [l'esercizio 3 del lab di Archiviazione di Azure](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise3) per creare rapidamente un'app di questo tipo).

## <a name="create-a-computer-vision-resource"></a>Creare una risorsa di Visione artificiale

È necessario creare una risorsa di Visione artificiale per l'account Azure. Questa risorsa gestisce l'accesso al servizio Visione artificiale di Azure. 

1. Seguire le istruzioni contenute in [Creare una risorsa di Servizi cognitivi di Azure](../../cognitive-services-apis-create-account.md) per creare una risorsa di Visione artificiale.

1. Passare quindi al menu del gruppo di risorse e fare clic sulla sottoscrizione API Visione artificiale appena creata. Copiare l'URL sotto **Endpoint** in qualsiasi punto sia possibile recuperarlo facilmente in seguito. Quindi fare clic su **Mostra chiavi di accesso**.

    ![Pagina del portale di Azure con l'URL endpoint e il collegamento delle chiavi di accesso evidenziato](../Images/copy-vision-endpoint.png)
    
    [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]


1. Nella finestra successiva, copiare il valore della **CHIAVE 1** negli Appunti.

    ![Finestra di dialogo Gestisci chiavi con il pulsante di copia evidenziato](../Images/copy-vision-key.png)

## <a name="add-computer-vision-credentials"></a>Aggiungere le credenziali dell'API Visione artificiale

Aggiungere quindi le credenziali necessarie all'app in modo che possa accedere alle risorse di Visione artificiale

Aprire l'applicazione Web ASP.NET in Visual Studio e passare al file **Web. config** nella radice del progetto. Aggiungere le istruzioni seguenti alla sezione `<appSettings>` del file, sostituendo `VISION_KEY` con la chiave copiata nel passaggio precedente e `VISION_ENDPOINT` con l'URL salvato nel passaggio precedente.

```xml
<add key="SubscriptionKey" value="VISION_KEY" />
<add key="VisionEndpoint" value="VISION_ENDPOINT" />
```

Quindi, in Esplora soluzioni, fare clic con il pulsante destro del mouse sul progetto e usare il comando **Manage NuGet Packages** per installare il pacchetto **Microsoft.Azure.CognitiveServices.Vision.ComputerVision**. Questo pacchetto contiene i tipi necessari a chiamare l'API Visione artificiale.

## <a name="add-metadata-generation-code"></a>Aggiungere il codice di generazione dei metadati

Successivamente, si aggiungerà il codice che sfrutta il servizio Visione artificiale per creare i metadati per le immagini. Questi passaggi si applicano all'app ASP.NET nel lab, ma possono essere adattati alla propria app. Ciò che è importante a questo punto è avere un'applicazione Web ASP.NET che possa caricare immagini in un contenitore di Archiviazione di Azure, leggere le immagini in esso contenute e visualizzarle nella vista. In caso di dubbi, è consigliabile seguire l'[esercizio 3 del lab di Archiviazione di Azure](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise3). 

1. Aprire il file *HomeController.cs* nella cartella **controller** del progetto e aggiungere le seguenti istruzioni `using` all'inizio del file:

    ```csharp
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
    ```

1. Passare quindi al metodo **Upload**, che converte e carica le immagini nell'archiviazione BLOB. Aggiungere il codice seguente immediatamente dopo il blocco che inizia con `// Generate a thumbnail` (o alla fine del processo di creazione di BLOB dell'immagine). Questo codice accetta il BLOB contenente l'immagine (`photo`) e usa Visione artificiale per generare una descrizione per quell'immagine. L'API Visione artificiale genera inoltre un elenco di parole chiave che si applicano all'immagine. Le parole chiave e la descrizione generate vengono archiviate nei metadati del BLOB per poterle recuperare in un secondo momento.

    ```csharp
    // Submit the image to Azure's Computer Vision API
    ComputerVisionClient vision = new ComputerVisionClient(
        new ApiKeyServiceClientCredentials(ConfigurationManager.AppSettings["SubscriptionKey"]),
        new System.Net.Http.DelegatingHandler[] { });
    vision.Endpoint = ConfigurationManager.AppSettings["VisionEndpoint"];

    VisualFeatureTypes[] features = new VisualFeatureTypes[] { VisualFeatureTypes.Description };
    var result = await vision.AnalyzeImageAsync(photo.Uri.ToString(), features);

    // Record the image description and tags in blob metadata
    photo.Metadata.Add("Caption", result.Description.Captions[0].Text);

    for (int i = 0; i < result.Description.Tags.Count; i++)
    {
        string key = String.Format("Tag{0}", i);
        photo.Metadata.Add(key, result.Description.Tags[i]);
    }

    await photo.SetMetadataAsync();
    ```

1. Passare quindi al metodo **Index** nello stesso file; questo metodo enumera i BLOB dell'immagine archiviati nel contenitore BLOB di destinazione (come le istanze **IListBlobItem**) e li passa alla vista dell'applicazione. Sostituire il blocco `foreach` in questo metodo con il codice seguente. Questo codice chiama **CloudBlockBlob.FetchAttributes** per ottenere i metadati allegati di ogni BLOB. Estrae la descrizione generata dal computer (`caption`) dai metadati e la aggiunge all'oggetto **BlobInfo**, che viene passato alla vista.
    
    ```csharp
    foreach (IListBlobItem item in container.ListBlobs())
    {
        var blob = item as CloudBlockBlob;
    
        if (blob != null)
        {
            blob.FetchAttributes(); // Get blob metadata
            var caption = blob.Metadata.ContainsKey("Caption") ? blob.Metadata["Caption"] : blob.Name;
    
            blobs.Add(new BlobInfo()
            {
                ImageUri = blob.Uri.ToString(),
                ThumbnailUri = blob.Uri.ToString().Replace("/photos/", "/thumbnails/"),
                Caption = caption
            });
        }
    }
    ```

## <a name="test-the-app"></a>Testare l'app

Salvare le modifiche in Visual Studio e premere **CTRL+F5** per avviare l'applicazione nel browser. Usare l'app per caricare alcune immagini, dalla cartella "photos" nelle risorse del lab o da una cartella personalizzata. Quando si passa il cursore su una delle immagini nella vista, verrà visualizzata una finestra descrizione comando contenente la didascalia per l'immagine generata dal computer.

![Didascalia generata dal computer](../Images/thumbnail-with-tooltip.png)

Per visualizzare tutti i metadati allegati, usare Azure Storage Explorer per visualizzare il contenitore di archiviazione in uso per le immagini. Fare clic con il pulsante destro del mouse su un BLOB qualsiasi nel contenitore e scegliere **Proprietà**. Nella finestra di dialogo, verrà visualizzato un elenco di coppie chiave-valore. La descrizione dell'immagine generata dal computer viene archiviata nell'elemento "Sottotitolo" e le parole chiave di ricerca vengono archiviate in "Tag0", "Tag1" e così via. Al termine, fare clic su **Annulla** per chiudere la finestra di dialogo.

![Finestra di dialogo Proprietà immagine, con i tag dei metadati elencati](../Images/blob-metadata.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Per continuare a lavorare sull'app Web, vedere la sezione [Passaggi successivi](#next-steps). Se non si intende continuare a usare questa applicazione, è necessario eliminare tutte le risorse specifiche delle app. A tale scopo, è sufficiente eliminare il gruppo di risorse che contiene la sottoscrizione di Archiviazione di Azure e la risorsa Visione artificiale. Verranno rimossi l'account di archiviazione, i BLOB in esso caricati e la risorsa del servizio app necessaria per connettersi con l'app Web ASP.NET. 

Per eliminare il gruppo di risorse, aprire il pannello **Gruppi di risorse** nel portale, passare al gruppo di risorse usato per questo progetto e fare clic su **Elimina gruppo di risorse** nella parte superiore della visualizzazione. Verrà richiesto di digitare il nome del gruppo di risorse per confermare l'eliminazione, perché non sarà possibile recuperarlo una volta eliminato.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione, il servizio Visione artificiale di Azure è stato integrato in un'app Web esistente per generare automaticamente sottotitoli e parole chiave per le immagini BLOB durante il caricamento. Successivamente, consultare l'esercizio 6 del lab di Archiviazione di Azure per informazioni su come aggiungere funzionalità di ricerca all'app Web. In tal modo si sfruttano le parole chiave di ricerca generate dal servizio Visione artificiale.

> [!div class="nextstepaction"]
> [Aggiungere ricerca all'app](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise6)
