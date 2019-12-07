---
title: Gestire un account di Video Indexer
titleSuffix: Azure Media Services
description: Questo articolo illustra come gestire un account Video Indexer connesso ad Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 4f8491e31747eda9cbe8689ba7db3026df0ff3ad
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892771"
---
# <a name="manage-a-video-indexer-account-connected-to-azure"></a>Gestire un account di Video Indexer connesso ad Azure

Questo articolo illustra come gestire un account di Video Indexer connesso alla sottoscrizione di Azure e a un account di Servizi multimediali di Azure.

> [!NOTE]
> Per fare rettifiche alla configurazione dell'account come illustrato in questo argomento, è necessario essere proprietari dell'account di Video Indexer.

## <a name="prerequisites"></a>Prerequisiti

Collegare l'account di Video Indexer ad Azure come descritto in [Creare un account di Video Indexer connesso ad Azure](connect-to-azure.md). 

Assicurarsi di seguire i [Prerequisiti](connect-to-azure.md#prerequisites) ed esaminare le [Considerazioni](connect-to-azure.md#considerations) riportate nell'articolo.

## <a name="examine-account-settings"></a>Esaminare le impostazioni dell'account

Questa sezione esamina le impostazioni dell'account di Video Indexer.

Per visualizzare le impostazioni:

1. Fare clic sull'icona utente nell'angolo superiore destro e selezionare **Impostazioni**.

    ![Impostazioni](./media/manage-account-connected-to-azure/select-settings.png)

2. Nella pagina **Impostazioni** selezionare la scheda **Account**.

Se l'account di Video Indexer è connesso ad Azure, viene visualizzato quanto segue:

* Immettere il nome dell'account Servizi multimediali sottostante.
* Il numero di processi di indicizzazione in esecuzione e in coda.
* Il numero e il tipo di unità riservare allocate.

Se l'account richiede delle rettifiche, verranno visualizzati gli errori e gli avvisi relativi alla configurazione dell'account nella pagina **Impostazioni**. I messaggi contengono collegamenti a posizioni esatte nel portale di Azure in cui è necessario apportare modifiche. Per altre informazioni, vedere la sezione relativa agli [errori e avvisi](#errors-and-warnings) qui di seguito.

## <a name="auto-scale-reserved-units"></a>Scalabilità automatica delle unità riservate

La pagina **Impostazioni** consente di impostare la scalabilità automatica delle Unità riservate multimediali (UR). Se l'opzione è **Attiva**, è possibile allocare il numero massimo di unità riservate (UR) e assicurarsi che Video Indexer arresti/avvii le UR automaticamente. Con questa opzione, non vengono addebitati costi per il tempo di inattività, ma non si attende nemmeno a lungo il completamento dei processi di indicizzazione quando il carico di indicizzazione è elevato.

La scalabilità automatica non effettua ridimensionamenti al di sotto di una UR o al di sopra del limite predefinito dell'account dei Servizi multimediali. Per aumentare il limite creare una richiesta di servizio. Per informazioni su quote e limitazioni e su come aprire un ticket di supporto, vedere [Quote e limitazioni](../../media-services/previous/media-services-quotas-and-limitations.md).

![Iscriviti](./media/manage-account-connected-to-azure/autoscale-reserved-units.png)

## <a name="errors-and-warnings"></a>Errori e avvisi

Se l'account richiede delle rettifiche, vengono visualizzati gli errori e gli avvisi relativi alla configurazione dell'account nella pagina **Impostazioni**. I messaggi contengono collegamenti a posizioni esatte nel portale di Azure in cui è necessario apportare modifiche. Questa sezione fornisce altri dettagli relativi ai messaggi di errore e di avviso.

* Griglia di eventi

    È necessario registrare il provider di risorse EventGrid usando il portale di Azure. Nel [portale di Azure](https://portal.azure.com/) passare a **Sottoscrizioni** > [sottoscrizione] > **ResourceProviders** > **Microsoft.EventGrid**. Se lo stato non è **Registrato**, fare clic su **Registra**. La registrazione richiede alcuni minuti. 

* Endpoint di streaming

    Assicurarsi che l'account dei Servizi multimediali sottostante abbia l'**Endpoint di streaming** predefinito in stato avviato. Altrimenti, non sarà possibile guardare i video di questo account dei Servizi multimediali o in Video Indexer.

* Media Reserved Unit 

    È necessario allocare le Unità riservate multimediali nella risorsa dei Servizi multimediali per poter indicizzare i video. Per ottimizzare le prestazioni di indicizzazione, si consiglia di allocare almeno 10 unità riservate S3. Per le informazioni sui prezzi, vedere la sezione delle domande frequenti nella pagina dei [prezzi dei Servizi multimediali](https://azure.microsoft.com/pricing/details/media-services/).   

## <a name="next-steps"></a>Passaggi successivi

È possibile interagire a livello di codice con l'account di valutazione e/o con gli account Video Indexer connessi ad Azure seguendo le istruzioni riportate in: [Usare le API](video-indexer-use-apis.md).

È consigliabile usare lo stesso utente di Azure AD usato durante la connessione ad Azure.
