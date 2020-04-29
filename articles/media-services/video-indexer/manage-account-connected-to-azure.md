---
title: Gestire un account di Video Indexer
titleSuffix: Azure Media Services
description: Informazioni su come gestire un account Video Indexer connesso ad Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 12/16/2019
ms.author: juliako
ms.openlocfilehash: 5b9ec1c04dd90af9d9380a1f4d30386c8f9cc3ed
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79499659"
---
# <a name="manage-a-video-indexer-account-connected-to-azure"></a>Gestire un account di Video Indexer connesso ad Azure

Questo articolo illustra come gestire un account di Video Indexer connesso alla sottoscrizione di Azure e a un account di servizi multimediali di Azure.

> [!NOTE]
> Per fare rettifiche alla configurazione dell'account come illustrato in questo argomento, è necessario essere proprietari dell'account di Video Indexer.

## <a name="prerequisites"></a>Prerequisiti

Collegare l'account di Video Indexer ad Azure come descritto in [Creare un account di Video Indexer connesso ad Azure](connect-to-azure.md).

Assicurarsi di seguire i [Prerequisiti](connect-to-azure.md#prerequisites) ed esaminare le [Considerazioni](connect-to-azure.md#considerations) riportate nell'articolo.

## <a name="examine-account-settings"></a>Esaminare le impostazioni dell'account

Questa sezione esamina le impostazioni dell'account di Video Indexer.

Per visualizzare le impostazioni:

1. Fare clic sull'icona utente nell'angolo in alto a destra e selezionare **Impostazioni**.

    ![Impostazioni in Video Indexer](./media/manage-account-connected-to-azure/select-settings.png)

2. Nella pagina **Impostazioni** selezionare la scheda **Account**.

Se l'account dell'indicizzatore video è connesso ad Azure, vengono visualizzati gli elementi seguenti:

* Immettere il nome dell'account Servizi multimediali sottostante.
* Il numero di processi di indicizzazione in esecuzione e in coda.
* Il numero e il tipo di unità riservate allocate.

Se è necessario apportare alcune modifiche all'account, verranno visualizzati gli errori e gli avvisi rilevanti relativi alla configurazione dell'account nella pagina **Impostazioni** . I messaggi contengono collegamenti a posizioni esatte nel portale di Azure in cui è necessario apportare modifiche. Per altre informazioni, vedere la sezione relativa agli [errori e avvisi](#errors-and-warnings) qui di seguito.

## <a name="repair-the-connection-to-azure"></a>Ripristinare la connessione ad Azure

Nella finestra di dialogo **Aggiorna connessione a servizi multimediali di Azure** della pagina [video Indexer](https://www.videoindexer.ai/) , viene richiesto di specificare i valori per le impostazioni seguenti:

|Impostazione|Descrizione|
|---|---|
|ID della sottoscrizione di Azure|L'ID sottoscrizione può essere recuperato dal portale di Azure. Fare clic su **tutti i servizi** nel riquadro a sinistra e cercare "sottoscrizioni". Selezionare **Sottoscrizioni** e scegliere l'ID desiderato dall'elenco delle sottoscrizioni.|
|Nome del gruppo di risorse di Servizi multimediali di Azure|Nome del gruppo di risorse in cui è stato creato l'account di Servizi multimediali.|
|ID applicazione|L'ID dell'applicazione Azure AD (con le autorizzazioni per l'account di servizi multimediali specificato) creato per questo account di Video Indexer. <br/><br/>Per ottenere l'ID app, passare a portale di Azure. Nell'account di servizi multimediali scegliere l'account e passare ad **accesso all'API**. Selezionare **Connetti all'API servizi multimediali con l'entità** -> servizio**app Azure ad**. Copiare i parametri pertinenti.|
|Chiave applicazione|Chiave dell'applicazione Azure AD associata all'account di servizi multimediali specificato in precedenza. <br/><br/>Per ottenere la chiave dell'app, passare a portale di Azure. Nell'account di servizi multimediali scegliere l'account e passare ad **accesso all'API**. Selezionare **Connetti all'API servizi multimediali con l'entità** -> servizio**Gestisci** -> **certificati applicazione & segreti**. Copiare i parametri pertinenti.|

## <a name="autoscale-reserved-units"></a>Unità riservate di scalabilità automatica

La pagina **Impostazioni** consente di impostare la scalabilità automatica di media reserved Unit (UR). Se l'opzione è **Attiva**, è possibile allocare il numero massimo di unità riservate (UR) e assicurarsi che Video Indexer arresti/avvii le UR automaticamente. Con questa opzione non vengono addebitati costi aggiuntivi per il tempo di inattività, ma non è possibile attendere che i processi di indicizzazione vengano completati molto tempo quando il carico di indicizzazione è elevato.

La scalabilità automatica non viene ridimensionata al di sotto di 1 UR o superiore al limite predefinito dell'account di servizi multimediali. Per aumentare il limite, creare una richiesta di servizio. Per informazioni su quote e limitazioni e su come aprire un ticket di supporto, vedere [Quote e limitazioni](../../media-services/previous/media-services-quotas-and-limitations.md).

![Unità riservate di scalabilità automatica Video Indexer](./media/manage-account-connected-to-azure/autoscale-reserved-units.png)

## <a name="errors-and-warnings"></a>Errori e avvisi

Se l'account richiede delle rettifiche, vengono visualizzati gli errori e gli avvisi relativi alla configurazione dell'account nella pagina **Impostazioni**. I messaggi contengono collegamenti a posizioni esatte nel portale di Azure in cui è necessario apportare modifiche. Questa sezione fornisce altri dettagli relativi ai messaggi di errore e di avviso.

* EventGrid

    È necessario registrare il provider di risorse EventGrid usando il portale di Azure. Nella [portale di Azure](https://portal.azure.com/)passare a **sottoscrizioni** > [sottoscrizione] > **ResourceProviders** > **Microsoft. EventGrid**. Se non è nello stato **registrato** , selezionare **registra**. La registrazione richiede alcuni minuti.

* endpoint di streaming

    Assicurarsi che l'account dei Servizi multimediali sottostante abbia l'**Endpoint di streaming** predefinito in stato avviato. In caso contrario, non sarà possibile guardare i video di questo account di servizi multimediali o in Video Indexer.

* Media reserved unit

    È necessario allocare le Unità riservate multimediali nella risorsa dei Servizi multimediali per poter indicizzare i video. Per ottimizzare le prestazioni di indicizzazione, si consiglia di allocare almeno 10 unità riservate S3. Per le informazioni sui prezzi, vedere la sezione delle domande frequenti nella pagina dei [prezzi dei Servizi multimediali](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="next-steps"></a>Passaggi successivi

È possibile interagire a livello di codice con l'account di valutazione o con account Video Indexer connessi ad Azure seguendo le istruzioni riportate in: [usare le API](video-indexer-use-apis.md).

Usare lo stesso Azure AD utente usato per la connessione ad Azure.
