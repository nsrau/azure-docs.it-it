---
title: Creare un account di Video Indexer nel portale di Azure
titlesuffix: Azure Media Services
description: Questo articolo illustra come creare un account di Video Indexer nel portale di Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 01/12/2019
ms.author: juliako
ms.openlocfilehash: 55828ea2235e42920a5179846d81711b1ada5dc2
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2019
ms.locfileid: "54261554"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>Creare un account di Video Indexer connesso ad Azure

Al momento della creazione di un account di Video Indexer, è possibile scegliere un account di valutazione gratuito (in cui si ottiene un certo numero di minuti di indicizzazione gratuita) o un'opzione a pagamento (in cui non si è limitati dalla quota). Con la versione di valutazione gratuita, Video Indexer offre fino a 600 minuti di indicizzazione gratuita per gli utenti di siti Web e fino a 2400 minuti di indicizzazione gratuita per gli utenti di API. Con l'opzione a pagamento, si crea un account di Video Indexer connesso alla sottoscrizione di Azure e a un account di Servizi multimediali di Azure. Il pagamento viene effettuato per i minuti di indicizzazione, nonché in base ai costi correlati all'account multimediale. 

Questo articolo illustra come creare un account di Video Indexer connesso a una sottoscrizione di Azure e a un account di Servizi multimediali di Azure. L'argomento illustra i passaggi per la connessione ad Azure usando il flusso automatico (impostazione predefinita). Viene anche descritto come connettersi ad Azure manualmente (impostazioni avanzate).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure.

    Se non si dispone ancora di una sottoscrizione di Azure, registrarsi per la [versione di valutazione gratuita di Azure](https://azure.microsoft.com/free/).

* Un dominio di Azure Active Directory (AD).

    Se non si dispone di un dominio di Azure AD, creare il dominio nella sottoscrizione di Azure. Per altre informazioni, vedere [Gestione dei nomi di dominio personalizzati in Azure Active Directory](../../active-directory/users-groups-roles/domains-manage.md)

* Un utente e un membro del dominio di Azure AD. Questo membro verrà usato per la connessione dell'account di Video Indexer ad Azure.

    L'utente deve essere un utente di Azure AD con un account aziendale o dell'istituto di istruzione e non un account personale, ad esempio outlook.com, live.com o hotmail.com.

    ![tutti gli utenti AAD](./media/create-account/all-aad-users.png)

### <a name="additional-prerequisites-for-automatic-flow"></a>Prerequisiti aggiuntivi per il flusso automatico

Un utente e un membro del dominio di Azure AD. Questo membro verrà usato per la connessione dell'account di Video Indexer ad Azure.

L'utente deve essere un membro nella sottoscrizione di Azure con un ruolo **Proprietario** o con i ruoli **Collaboratore** e **Amministratore accessi utente**. Un utente può essere aggiunto due volte, con due ruoli: una volta con quello di collaboratore e una seconda volta con quello di amministratore degli accessi.

![controllo di accesso](./media/create-account/access-control-iam.png)

### <a name="additional-prerequisites-for-manual-flow"></a>Prerequisiti aggiuntivi per il flusso manuale

Registrare il provider di risorse EventGrid usando il portale di Azure.

Nel [portale di Azure](https://portal.azure.com/) passare a **Sottoscrizioni**->[sottoscrizione]->**Provider di risorse**. 

Cercare **Microsoft.Media** e **Microsoft.EventGrid**. Se lo stato non è "Registrato", fare clic su **Registra**. La registrazione richiede alcuni minuti.

![EventGrid](./media/create-account/event-grid.png)

## <a name="connect-to-azure"></a>Connect to Azure

1. Passare al sito Web di [Video Indexer](https://www.videoindexer.ai/) ed eseguire l'accesso.

2. Fare clic sul pulsante **Connessione ad Azure**:

    ![connessione ad Azure](./media/create-account/connect-to-azure.png)

3. Quando viene visualizzato l'elenco delle sottoscrizioni, selezionare la sottoscrizione da usare.

    ![connessione di Video Indexer ad Azure](./media/create-account/connect-vi-to-azure-subscription.png)

4. Selezionare un'area di Azure dalle posizioni supportate: Stati Uniti occidentali 2, Europa settentrionale o Asia orientale.
5. In **Account Servizi multimediali di Azure** scegliere una delle opzioni seguenti:

    * Per creare un nuovo account di Servizi multimediali, selezionare **Crea nuovo gruppo di risorse**. Specificare il nome per il gruppo di risorse.

        Azure creerà il nuovo account nella sottoscrizione, incluso un nuovo account di Archiviazione di Azure. Il nuovo account di Servizi multimediali ha una configurazione iniziale predefinita con un endpoint di streaming e 10 unità riservate S3.
    * Per usare un account di Servizi multimediali esistente, selezionare **Usa risorsa esistente**. Nell'elenco degli account selezionare l'account.

        L'account di Servizi multimediali deve avere la stessa area dell'account di Video Indexer. 

        > [!NOTE]
        > Per ridurre al minimo la durata dell'indicizzazione e i problemi di velocità effettiva bassa, è consigliabile impostare il tipo e il numero delle [Unità riservate](../previous/media-services-scale-media-processing-overview.md ) su **10 unità riservate S3** nell'account di Servizi multimediali. Vedere [Modificare il tipo di unità riservata](../previous/media-services-portal-scale-media-processing.md).

    * Per configurare manualmente la connessione, fare clic sul collegamento **Switch to manual configuration** (Passa alla configurazione manuale).

        Per informazioni dettagliate, vedere la sezione seguente [Connettersi ad Azure manualmente (opzione avanzata)](#connect-to-azure-manually-advanced-option).
6. Al termine, scegliere **Connetti**. L'operazione potrebbe richiedere alcuni minuti. 

    Dopo la connessione ad Azure, il nuovo account di Video Indexer viene visualizzato nell'elenco degli account:

    ![nuovo account](./media/create-account/new-account.png)

7. Passare al nuovo account:

    ![Account di Video Indexer](./media/create-account/vi-account.png)

## <a name="connect-to-azure-manually-advanced-option"></a>Connettersi ad Azure manualmente (opzione avanzata)

Se la connessione ad Azure non è riuscita, è possibile tentare di risolvere il problema connettendosi manualmente.

> [!NOTE]
> È consigliabile avere i seguenti tre account nella stessa area: l'account Video Indexer connesso con l'account di Servizi multimediali, nonché l'account di archiviazione di Azure connesso allo stesso account di Servizi multimediali.

### <a name="create-and-configure-a-media-services-account"></a>Creare e configurare un account di Servizi multimediali di Azure

1. Usare il portale di [Azure](https://portal.azure.com/) per creare un account di Servizi multimediali di Azure, come descritto in [Creare un account](../previous/media-services-portal-create-account.md).

    Quando si crea un account di archiviazione per l'account di Servizi multimediali, selezionare **StorageV2** (Archiviazione v2) per il tipo di account e **Con ridondanza geografica** per i campi di replica.

    ![Nuovo account di Servizi multimediali di Azure](./media/create-account/create-ams-account1.png)

    > [!NOTE]
    > Assicurarsi di annotare i nomi della risorsa e dell'account di Servizi multimediali. I nomi saranno necessari nei passaggi della sezione che segue.

2. Impostare il tipo e il numero di [Unità riservate](../previous/media-services-scale-media-processing-overview.md ) su **Unità riservate 10 S3** nell'account di Servizi multimediali creato. Vedere [Modificare il tipo di unità riservata](../previous/media-services-portal-scale-media-processing.md).
3. Per poter riprodurre i video nell'applicazione Web Video Indexer, è necessario avviare l'**endpoint di streaming** predefinito del nuovo account di Servizi multimediali.

    Nel nuovo account di Servizi multimediali fare clic su **Endpoint di streaming**. Selezionare l'endpoint di streaming e premere Avvia.

    ![Nuovo account di Servizi multimediali di Azure](./media/create-account/create-ams-account2.png)

4. Per consentire a Video Indexer di eseguire l'autenticazione nell'API Servizi multimediali, è necessario creare un'applicazione AD. I passaggi seguenti consentono di eseguire il processo di autenticazione Azure AD descritto in [Introduzione all'autenticazione di Azure AD tramite il portale di Azure](../previous/media-services-portal-get-started-with-aad.md):

    1. Nel nuovo account di Servizi multimediali selezionare **Accesso all'API**.
    2. Selezionare il [metodo di autenticazione basata sull'entità servizio](../previous/media-services-portal-get-started-with-aad.md#service-principal-authentication).
    3. Ottenere l'ID client e il segreto client come descritto nella sezione [Ottenere l'ID client e il segreto client](../previous/media-services-portal-get-started-with-aad.md#get-the-client-id-and-client-secret).

        Dopo aver selezionato **Impostazioni**->**Chiavi**, aggiungere la **Descrizione** e scegliere **Salva**. Verrà popolato il valore della chiave.

        Se la chiave scade, il proprietario dell'account dovrà contattare il supporto di Video Indexer per rinnovare la chiave.

        > [!NOTE]
        > Assicurarsi di annotare il valore della chiave e l'ID applicazione. I nomi saranno necessari nei passaggi della sezione che segue.

### <a name="connect-manually"></a>Connettersi manualmente

Nella finestra di dialogo **Connect Video Indexer to an Azure subscription** (Connetti Video Indexer a una sottoscrizione di Azure) della pagina [Video Indexer](https://www.videoindexer.ai/) fare clic sul collegamento **Switch to manual configuration** (Passa a configurazione manuale).

Nella finestra di dialogo specificare le informazioni seguenti:

|Impostazione|DESCRIZIONE|
|---|---|
|Area dell'account di Video Indexer|Il nome dell'area dell'account di Video Indexer. Per migliorare le prestazioni e ridurre i costi, si consiglia di specificare il nome dell'area in cui si trovano le risorse dei Servizi multimediali di Azure e un account di Archiviazione di Azure. |
|Tenant di Azure Active Directory (AAD)|Il nome del tenant di Azure AD, ad esempio "contoso.onmicrosoft.com". Le informazioni sul tenant possono essere recuperate dal portale di Azure. Posizionare il cursore sul nome dell'utente connesso nell'angolo in alto a destra. Trovare il nome a destra di **Dominio**.|
|ID sottoscrizione|La sottoscrizione di Azure in cui deve essere creata la connessione. L'ID sottoscrizione può essere recuperato dal portale di Azure. Fare clic su **All services** (Tutti i servizi) nel pannello a sinistra e cercare "sottoscrizioni". Selezionare **Sottoscrizioni** e scegliere l'ID desiderato dall'elenco delle sottoscrizioni.|
|Nome del gruppo di risorse di Servizi multimediali di Azure|Nome del gruppo di risorse in cui è stato creato l'account di Servizi multimediali.|
|Nome della risorsa dei servizi multimediali|Nome dell'account di Servizi multimediali di Azure creato nella sezione precedente.|
|ID applicazione|ID dell'applicazione di Azure AD con le autorizzazioni per l'account di Servizi multimediali specificato creato nella sezione precedente.|
|Chiave applicazione|Chiave dell'applicazione Azure AD creata nella sezione precedente. |

## <a name="considerations"></a>Considerazioni

Tenere presenti le seguenti considerazioni relative a Servizi multimediali di Azure:

* Se la connessione è stata eseguita automaticamente, nella sottoscrizione di Azure vengono visualizzati un nuovo gruppo di risorse, un account di Servizi multimediali e un account di archiviazione.
* Se la connessione viene eseguita automaticamente, Video Indexer imposta le **Unità riservate** multimediali su 10 unità S3:

    ![Unità riservate di Servizi multimediali](./media/create-account/ams-reserved-units.png)

* Se viene eseguita la connessione a un account di Servizi multimediali esistente, Video Indexer non modifica la configurazione esistente di **Unità riservate** multimediali.

   Può essere necessario modificare il tipo e numero di unità riservate multimediali, in base al carico pianificato. Tenere presente che se il carico è elevato e le unità o la velocità non sono sufficienti, possono verificarsi errori di timeout durante l'elaborazione video.

* Se viene eseguita la connessione a un nuovo account di Servizi multimediali, Video Indexer avvia automaticamente l'**endpoint di streaming** predefinito nell'account:

    ![Endpoint di streaming di Servizi multimediali](./media/create-account/ams-streaming-endpoint.png)

    Gli endpoint di streaming hanno un tempo di avvio prolungato. Per questa ragione, potrebbe essere necessario attendere alcuni minuti dopo aver connesso l'account ad Azure per poter eseguire lo streaming e riprodurre i video nell'applicazione Web Video Indexer.

* Se viene eseguita la connessione a un account esistente di Servizi multimediali, Video Indexer non modifica la configurazione dell'endpoint di streaming predefinito. Se non sono in esecuzione **endpoint di streaming**, non sarà possibile guardare i video di questo account di Servizi multimediali o in Video Indexer.

## <a name="next-steps"></a>Passaggi successivi

È possibile interagire a livello di codice con l'account di valutazione e/o con gli account Video Indexer connessi ad Azure seguendo le istruzioni riportate in: [Usare le API](video-indexer-use-apis.md).

È consigliabile usare lo stesso utente di Azure AD usato durante la connessione ad Azure.


