---
title: Connettere un archivio conoscenze con Power BI - Ricerca di Azure
description: Connettere un archivio conoscenze di Ricerca di Azure con Power BI per l'analisi e l'esplorazione.
author: lisaleib
services: search
ms.service: search
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: v-lilei
ms.openlocfilehash: de282213535a2e49f73bc30e476bae02d470fdb2
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265657"
---
# <a name="connect-a-knowledge-store-with-power-bi"></a>Connettere un archivio conoscenze con Power BI

> [!Note]
> La funzionalità dell'archivio conoscenze è in anteprima e non deve essere usata in ambienti di produzione. È inclusa nell'[API REST di Ricerca di Azure versione 2019-05-06-Preview](search-api-preview.md). Non è attualmente disponibile alcun supporto di .NET SDK.
>
Questo articolo illustra come connettere ed esplorare un archivio conoscenze usando Power Query nell'app Power BI Desktop. Per creare l'esempio di archivio conoscenze usato in questa procedura dettagliata, vedere [Creare un archivio conoscenze nel portale di Azure](knowledge-store-create-portal.md).

## <a name="prerequisites"></a>Prerequisiti

+ Per creare l'esempio di archivio conoscenze usato in questa procedura dettagliata, seguire le procedure descritte in [Creare un archivio conoscenze nel portale di Azure](knowledge-store-create-portal.md). Servirà anche il nome dell'account di archiviazione di Azure usato per creare l'archivio conoscenze, insieme alla relativa chiave di accesso dal portale di Azure.

+ [Installare Power BI Desktop](https://powerbi.microsoft.com/downloads/)

## <a name="connect-with-power-bi"></a>Connettersi con Power BI

1. Avviare Power BI Desktop e fare clic su **Recupera dati**.

1. Nella finestra **Recupera dati** selezionare **Azure** e quindi **Archiviazione tabelle di Azure**.

1. Fare clic su **Connetti**.

1. In **Nome dell'account o URL** immettere il nome dell'account di archiviazione di Azure. L'URL completo verrà creato automaticamente.

1. Se richiesto, immettere la chiave dell'account di archiviazione.

1. Selezionare le tabelle *hotelReviewsSsDocument*, *hotelReviewsSsKeyPhrases* e *hotelReviewsSsPages*. Queste tabelle sono le proiezioni di tabelle di Azure dei dati di esempio delle recensioni di hotel e includono gli arricchimenti dei servizi cognitivi selezionati al momento della creazione dell'archivio conoscenze.

1. Fare clic su **Carica**.

1. Sulla barra multifunzione superiore fare clic su **Modifica query** per aprire **Editor di Power Query**.

   ![Aprire Power Query](media/knowledge-store-connect-power-bi/powerbi-edit-queries.png "Aprire Power Query")

1. Selezionare *hotelReviewsSsDocument* e quindi rimuovere le colonne *PartitionKey*, *RowKey* e *Timestamp*. 

   ![Modificare le tabelle](media/knowledge-store-connect-power-bi/powerbi-edit-table.png "Modificare le tabelle")

1. Fare clic sull'icona con le frecce opposte sul lato superiore destro della tabella per espandere *Contenuto*. Quando viene visualizzato l'elenco delle colonne, selezionare tutte le colonne e quindi deselezionare le colonne che iniziano con "metadata". Fare clic su **OK** per visualizzare le colonne selezionate.

   ![Modificare le tabelle](media/knowledge-store-connect-power-bi/powerbi-expand-content-table.png "Espandere il contenuto")

1. Cambiare il tipo di dati delle colonne seguenti facendo clic sull'icona ABC-123 nell'angolo superiore sinistro della colonna.

   + Per *Content.latitude* e *Content.longitude* selezionare **Numero decimale**.
   + Per *Content.reviews_date* e *Content.reviews_dateAdded* selezionare **Data/ora**.

   ![Cambiare i tipi di dati](media/knowledge-store-connect-power-bi/powerbi-change-type.png "Cambiare i tipi di dati")

1. Selezionare *hotelReviewsSsPages*, quindi ripetere i passaggi 9 e 10 per eliminare le colonne ed espandere *Contenuto*.
1. Cambiare il tipo di dati di *Content.SentimentScore* in **Numero decimale**.
1. Selezionare *hotelReviewsSsKeyPhrases*, quindi ripetere i passaggi 9 e 10 per eliminare le colonne ed espandere *Contenuto*. Per questa tabella non sono necessarie modifiche del tipo di dati.

1. Sulla barra dei comandi fare clic su **Chiudi e applica**.

1. Fare clic sul riquadro Modello nel riquadro di spostamento a sinistra e verificare che Power BI mostri le relazioni fra le tre tabelle.

   ![Verificare le relazioni](media/knowledge-store-connect-power-bi/powerbi-relationships.png "Verificare le relazioni")

1. Fare doppio clic su ogni relazione e verificare che **Direzione filtro incrociato** sia impostato su **Entrambe**.  Questa impostazione consente l'aggiornamento degli oggetti visivi quando viene applicato un filtro.

<!-- ## Try with larger data sets

We purposely kept the data set small to avoid charges for a demo walkthrough. For a more realistic experience, you can create and then attach a billable Cognitive Services resource to enable a larger number of transactions against the sentiment analyzer, keyphrase extraction, and language detector skills.

Create new containers in Azure Blob storage and upload each CSV file to its own container. Specify one of these containers in the data source creation step in Import data wizard.

| Description | Link |
|-------------|------|
| Free tier   | [HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Small (500 Records) | [HotelReviews_Small.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Small.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Medium (6000 Records)| [HotelReviews_Medium.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Medium.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)
| Large (Full dataset 35000 Records) | [HotelReviews_Large.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Large.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Be aware that very large data sets are expensive to process. This one costs roughly $1000 U.S dollars.|

In the enrichment step of the wizard, attach a billable [Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) resource, created at the *S0* tier, in the same region as Azure Search to use larger data sets. 

  ![Create a Cognitive Services resource](media/knowledge-store-connect-power-bi/create-cognitive-service.png "Create a Cognitive Services resource") -->

## <a name="clean-up"></a>Eseguire la pulizia

Quando si lavora nella propria sottoscrizione, alla fine di un progetto è opportuno verificare se le risorse create sono ancora necessarie. L'esecuzione continua delle risorse può avere un costo. È possibile eliminare le singole risorse oppure il gruppo di risorse per eliminare l'intero set di risorse.

Per trovare e gestire le risorse nel portale, usare il collegamento **Tutte le risorse** o **Gruppi di risorse** nel riquadro di spostamento a sinistra.

Se si usa un servizio gratuito, tenere presente che il numero di indicizzatori e origini dati è limitato a tre. Per non superare il limite, è possibile eliminare i singoli elementi nel portale.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come esplorare questo archivio conoscenze usando Storage Explorer, vedere l'articolo seguente.

> [!div class="nextstepaction"]
> [Visualizzare con Storage Explorer](knowledge-store-view-storage-explorer.md)

Per informazioni su come creare un archivio conoscenze usando le API REST e Postman, vedere l'articolo seguente.  

> [!div class="nextstepaction"]
> [Creare un archivio conoscenze in REST](knowledge-store-howto.md)
