---
title: Gestire un account di Video Indexer
titleSuffix: Azure Media Services
description: Informazioni su come gestire un account dell'indicizzatore video connesso ad Azure.Learn how to manage a Video Indexer account connected to Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 12/16/2019
ms.author: juliako
ms.openlocfilehash: 5b9ec1c04dd90af9d9380a1f4d30386c8f9cc3ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499659"
---
# <a name="manage-a-video-indexer-account-connected-to-azure"></a>Gestire un account di Video Indexer connesso ad Azure

Questo articolo illustra come gestire un account dell'indicizzatore video connesso alla sottoscrizione di Azure e a un account di Servizi multimediali di Azure.This article demonstrates how to manage a Video Indexer account that's connected to your Azure subscription and an Azure Media Services account.

> [!NOTE]
> Per fare rettifiche alla configurazione dell'account come illustrato in questo argomento, è necessario essere proprietari dell'account di Video Indexer.

## <a name="prerequisites"></a>Prerequisiti

Collegare l'account di Video Indexer ad Azure come descritto in [Creare un account di Video Indexer connesso ad Azure](connect-to-azure.md).

Assicurarsi di seguire i [Prerequisiti](connect-to-azure.md#prerequisites) ed esaminare le [Considerazioni](connect-to-azure.md#considerations) riportate nell'articolo.

## <a name="examine-account-settings"></a>Esaminare le impostazioni dell'account

Questa sezione esamina le impostazioni dell'account di Video Indexer.

Per visualizzare le impostazioni:

1. Fare clic sull'icona dell'utente nell'angolo in alto a destra e selezionare **Impostazioni**.

    ![Impostazioni nell'indicizzatore video](./media/manage-account-connected-to-azure/select-settings.png)

2. Nella pagina **Impostazioni** selezionare la scheda **Account**.

Se l'account dell'indicizzatore Video è connesso ad Azure, vengono visualizzati i tipi seguenti:If your Videos Indexer account is connected to Azure, you see the following things:

* Immettere il nome dell'account Servizi multimediali sottostante.
* Il numero di processi di indicizzazione in esecuzione e in coda.
* Numero e tipo di unità riservate allocate.

Se il tuo account necessita di alcune modifiche, vedrai errori e avvisi pertinenti sulla configurazione dell'account nella pagina **Impostazioni.** I messaggi contengono collegamenti a posizioni esatte nel portale di Azure in cui è necessario apportare modifiche. Per altre informazioni, vedere la sezione relativa agli [errori e avvisi](#errors-and-warnings) qui di seguito.

## <a name="repair-the-connection-to-azure"></a>Ripristinare la connessione ad AzureRepair the connection to Azure

Nella **finestra** di dialogo Aggiorna connessione a Servizi multimediali di Azure della pagina [Indicizzatore video](https://www.videoindexer.ai/) viene richiesto di fornire i valori per le impostazioni seguenti:

|Impostazione|Descrizione|
|---|---|
|ID sottoscrizione di Azure|L'ID sottoscrizione può essere recuperato dal portale di Azure. Clicca su **Tutti i servizi** nel pannello di sinistra e cerca "iscrizioni". Selezionare **Sottoscrizioni** e scegliere l'ID desiderato dall'elenco delle sottoscrizioni.|
|Nome del gruppo di risorse di Servizi multimediali di Azure|Nome del gruppo di risorse in cui è stato creato l'account di Servizi multimediali.|
|ID applicazione|ID applicazione di Azure AD (con autorizzazioni per l'account di Servizi multimediali specificato) creato per questo account dell'indicizzatore video. <br/><br/>Per ottenere l'ID app, passare al portale di Azure.To get the app ID, navigate to Azure portal. Nell'account Servizi multimediali scegliere l'account e passare ad **Accesso API**. Selezionare **Connetti all'API di Servizi multimediali con l'entità** -> servizio**Azure AD App**. Copiare i parametri pertinenti.|
|Chiave applicazione|Chiave dell'applicazione Azure AD associata all'account di Servizi multimediali specificato in precedenza. <br/><br/>Per ottenere la chiave dell'app, passare al portale di Azure.To get the app key, navigate to Azure portal. Nell'account Servizi multimediali scegliere l'account e passare ad **Accesso API**. Selezionare **Connetti all'API di Servizi multimediali con l'entità servizio** -> Gestisci i segreti dei & dei certificati**dell'applicazione** -> **Certificates & secrets**. Copiare i parametri pertinenti.|

## <a name="autoscale-reserved-units"></a>Scalabilità automatica delle unità riservate

La pagina **Impostazioni** consente di impostare la scalabilità automatica delle unità riservate ai supporti (RU). Se l'opzione è **Attiva**, è possibile allocare il numero massimo di unità riservate (UR) e assicurarsi che Video Indexer arresti/avvii le UR automaticamente. Con questa opzione, non si paga denaro aggiuntivo per il tempo di inattività, ma non si attende il completamento dei processi di indicizzazione per un lungo periodo di tempo quando il carico di indicizzazione è elevato.

La scalabilità automatica non viene ridimensionata al di sotto di 1 RU o al di sopra del limite predefinito dell'account di Servizi multimediali. Per aumentare il limite, creare una richiesta di servizio. Per informazioni su quote e limitazioni e su come aprire un ticket di supporto, vedere [Quote e limitazioni](../../media-services/previous/media-services-quotas-and-limitations.md).

![Scalabilità automatica delle unità riservate Indicizzatore video](./media/manage-account-connected-to-azure/autoscale-reserved-units.png)

## <a name="errors-and-warnings"></a>Errori e avvisi

Se l'account richiede delle rettifiche, vengono visualizzati gli errori e gli avvisi relativi alla configurazione dell'account nella pagina **Impostazioni**. I messaggi contengono collegamenti a posizioni esatte nel portale di Azure in cui è necessario apportare modifiche. Questa sezione fornisce altri dettagli relativi ai messaggi di errore e di avviso.

* EventGrid

    È necessario registrare il provider di risorse EventGrid usando il portale di Azure. Nel [portale di Azure](https://portal.azure.com/)passare a **Sottoscrizioni** > [sottoscrizione] > **ResourceProviders** > **Microsoft.EventGrid**. Se non è presente nello stato **Registrato,** selezionare **Registra**. La registrazione richiede alcuni minuti.

* endpoint di streaming

    Assicurarsi che l'account dei Servizi multimediali sottostante abbia l'**Endpoint di streaming** predefinito in stato avviato. In caso contrario, non è possibile guardare i video da questo account di Servizi multimediali o in Video Indexer.

* Unità riservate ai supporti

    È necessario allocare le Unità riservate multimediali nella risorsa dei Servizi multimediali per poter indicizzare i video. Per ottimizzare le prestazioni di indicizzazione, si consiglia di allocare almeno 10 unità riservate S3. Per le informazioni sui prezzi, vedere la sezione delle domande frequenti nella pagina dei [prezzi dei Servizi multimediali](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="next-steps"></a>Passaggi successivi

È possibile interagire a livello di codice con l'account di valutazione o gli account dell'indicizzatore video connessi ad Azure seguendo le istruzioni in: [Usare le API](video-indexer-use-apis.md).

Usare lo stesso utente di Azure AD usato per la connessione ad Azure.Use the same Azure AD user you used when connecting to Azure.
