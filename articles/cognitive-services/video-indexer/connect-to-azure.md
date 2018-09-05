---
title: Creare un account di Video Indexer connesso ad Azure | Microsoft Docs
description: Questo articolo illustra come creare un account di Video Indexer connesso ad Azure.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 08/05/2018
ms.author: juliako
ms.openlocfilehash: 64a38ba617a1cc5fe1fdb3473e3cb88a49d89bb0
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/23/2018
ms.locfileid: "42744752"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>Creare un account di Video Indexer connesso ad Azure

Al momento della creazione di un account di Video Indexer, è possibile scegliere un account di valutazione gratuito (in cui si ottiene un certo numero di minuti di indicizzazione gratuita) o un'opzione a pagamento (in cui non si è limitati dalla quota). Con la versione di valutazione gratuita, Video Indexer offre fino a 600 minuti di indicizzazione gratuita per gli utenti di siti Web e fino a 2400 minuti di indicizzazione gratuita per gli utenti di API. Con l'opzione a pagamento, si crea un account di Video Indexer connesso alla sottoscrizione di Azure e a un account di Servizi multimediali di Azure. Il pagamento viene effettuato per i minuti di indicizzazione, nonché in base ai costi correlati all'account multimediale. 

Questo articolo illustra come creare un account di Video Indexer connesso a una sottoscrizione di Azure e a un account di Servizi multimediali di Azure. 

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. 

    Se non si dispone ancora di una sottoscrizione di Azure, registrarsi per la [versione di valutazione gratuita di Azure](https://azure.microsoft.com/free/).

* Un dominio di Azure Active Directory (AD). 

    Se non si dispone di un dominio di Azure AD, creare il dominio nella sottoscrizione di Azure. Per altre informazioni, vedere [Gestione dei nomi di dominio personalizzati in Azure Active Directory](../../active-directory/users-groups-roles/domains-manage.md)

* Un utente e un membro del dominio di Azure AD. Questo membro verrà usato per la connessione dell'account di Video Indexer ad Azure.

    L'utente deve soddisfare i criteri seguenti:

    * Essere un utente di Azure AD con un account aziendale o dell'istituto di istruzione, anziché un account personale come outlook.com, live.com o hotmail.com.
        
        ![tutti gli utenti AAD](./media/create-account/all-aad-users.png)

    *  Essere un membro nella sottoscrizione di Azure con un ruolo Proprietario o con i ruoli Collaboratore e Amministratore accessi utente. Un utente può essere aggiunto due volte, con due ruoli: una volta con quello di collaboratore e una seconda volta con quello di amministratore degli accessi.

        ![controllo di accesso](./media/create-account/access-control-iam.png)

* Registrare il provider di risorse EventGrid usando il portale di Azure.

    Nel [portale di Azure](https://portal.azure.com/) passare a **Sottoscrizioni** > [sottoscrizione] > **ResourceProviders** > **Microsoft.EventGrid**. Se lo stato non è "Registrato", fare clic su **Registra**. La registrazione richiede alcuni minuti. 

    ![EventGrid](./media/create-account/event-grid.png)

## <a name="connect-to-azure"></a>Connect to Azure

1. Accedere con tale utente e fare clic sul pulsante **Connect to Azure** (Connetti ad Azure):

    ![connessione ad Azure](./media/create-account/connect-to-azure.png)

2. Quando viene visualizzato l'elenco delle sottoscrizioni, selezionare la sottoscrizione da usare. 

    ![connessione di Video Indexer ad Azure](./media/create-account/connect-vi-to-azure-subscription.png)

3. Selezionare un'area di Azure dalle posizioni supportate: Stati Uniti occidentali 2, Europa settentrionale o Asia orientale.
4. In **Account Servizi multimediali di Azure** scegliere una delle opzioni seguenti:

    * Per creare un nuovo account di Servizi multimediali, selezionare **Crea nuovo gruppo di risorse**. Specificare il nome per il gruppo di risorse.

        Azure creerà il nuovo account nella sottoscrizione, incluso un nuovo account di Archiviazione di Azure. Il nuovo account di Servizi multimediali ha una configurazione iniziale predefinita con un endpoint di streaming e 10 unità riservate S3.
    * Per usare un account di Servizi multimediali esistente, selezionare **Usa risorsa esistente**. Nell'elenco degli account selezionare l'account.

        L'account di Servizi multimediali deve avere la stessa area dell'account di Video Indexer. Per ridurre al minimo la durata dell'indicizzazione e i problemi di velocità effettiva bassa, impostare il tipo e il numero delle unità riservate su **10 unità riservate S3** nell'account di Servizi multimediali.
    * Per configurare manualmente la connessione, scegliere il collegamento **Switch to manual configuration** (Passa alla configurazione manuale) e specificare le informazioni necessarie:

    ![connessione di Video Indexer ad Azure](./media/create-account/connect-vi-to-azure-subscription-2.png)

5. Al termine, scegliere **Connetti**. L'operazione potrebbe richiedere alcuni minuti. 

    Dopo la connessione ad Azure, il nuovo account di Video Indexer viene visualizzato nell'elenco degli account:

    ![nuovo account](./media/create-account/new-account.png)

6. Passare al nuovo account: 

    ![Account di Video Indexer](./media/create-account/vi-account.png)

## <a name="considerations"></a>Considerazioni

Tenere presenti le seguenti considerazioni relative a Servizi multimediali di Azure:

* Se è stata eseguita la connessione a un nuovo account di Servizi multimediali, nella sottoscrizione di Azure verranno visualizzati un nuovo gruppo di risorse, un account di Servizi multimediali e un account di archiviazione.
* Se è stata eseguita la connessione a un nuovo account di Servizi multimediali, Video Indexer imposterà l'opzione **Unità riservate** per i supporti su 10 unità S3:

    ![Unità riservate di Servizi multimediali](./media/create-account/ams-reserved-units.png)

* Se è stata eseguita la connessione a un account esistente di Servizi multimediali, Video Indexer non modifica la configurazione esistente di **Unità riservate** per i supporti.

    Potrebbe essere necessario modificare il tipo e numero di **Unità riservate** per i supporti, in base al carico pianificato. Tenere presente che se il carico è elevato e le unità o la velocità non sono sufficienti, possono verificarsi errori di timeout durante l'elaborazione video.

* Se è stata eseguita la connessione a un nuovo account di Servizi multimediali, Video Indexer avvia automaticamente un **endpoint di streaming** predefinito in tale account:

    ![Endpoint di streaming di Servizi multimediali](./media/create-account/ams-streaming-endpoint.png)

* Se è stata eseguita la connessione a un account esistente di Servizi multimediali, Video Indexer non modifica la configurazione degli endpoint di streaming predefiniti. Se non sono in esecuzione **endpoint di streaming**, non sarà possibile guardare i video di questo account di Servizi multimediali o in Video Indexer.

## <a name="next-steps"></a>Passaggi successivi

È possibile interagire a livello di codice con l'account di valutazione e/o con gli account Video Indexer connessi ad Azure seguendo le istruzioni riportate in: [Usare le API](video-indexer-use-apis.md).

È consigliabile usare lo stesso utente di Azure AD usato durante la connessione ad Azure.


