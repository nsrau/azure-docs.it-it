---
title: Creare un account di Video Indexer connesso ad Azure
titleSuffix: Azure Media Services
description: Informazioni su come creare un account Video Indexer connesso ad Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/08/2020
ms.author: juliako
ms.openlocfilehash: 405533aad8247350d45cc53009abe6b58a511264
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83005937"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>Creare un account di Video Indexer connesso ad Azure

Al momento della creazione di un account di Video Indexer, è possibile scegliere un account di valutazione gratuito (in cui si ottiene un certo numero di minuti di indicizzazione gratuiti) o un'opzione a pagamento (senza limiti di quota). Con la versione di valutazione gratuita, Video Indexer offre fino a 600 minuti di indicizzazione gratuita per gli utenti di siti Web e fino a 2.400 minuti di indicizzazione gratuita per gli utenti di API. Con l'opzione a pagamento, è possibile creare un account di Video Indexer connesso alla sottoscrizione di Azure e a un account di servizi multimediali di Azure. Paghi per minuti indicizzati e per gli addebiti relativi all'account multimediale.

Questo articolo illustra come creare un account di Video Indexer connesso a una sottoscrizione di Azure e a un account di Servizi multimediali di Azure. L'argomento illustra i passaggi per la connessione ad Azure usando il flusso automatico (impostazione predefinita). Viene anche descritto come connettersi ad Azure manualmente (impostazioni avanzate).

Se si passa da una *versione di valutazione* a un account a *pagamento* video indexer, è possibile scegliere di copiare tutti i video e la personalizzazione del modello nel nuovo account, come illustrato nella sezione [importare il contenuto dall'account di valutazione](#import-your-content-from-the-trial-account) .

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure.

    Se non si dispone ancora di una sottoscrizione di Azure, registrarsi per la [versione di valutazione gratuita di Azure](https://azure.microsoft.com/free/).

* Un dominio Azure Active Directory (Azure AD).

    Se non si dispone di un dominio di Azure AD, creare il dominio nella sottoscrizione di Azure. Per ulteriori informazioni, vedere [gestione dei nomi di dominio personalizzati nel Azure ad](../../active-directory/users-groups-roles/domains-manage.md)

* Un utente nel dominio di Azure AD con un ruolo di **amministratore dell'applicazione** . Questo membro verrà usato per la connessione dell'account di Video Indexer ad Azure.

    Questo utente deve essere Azure AD utente con un account aziendale o dell'Istituto di istruzione. Non usare un account personale, ad esempio outlook.com, live.com o hotmail.com.

    ![tutti gli utenti AAD](./media/create-account/all-aad-users.png)

### <a name="additional-prerequisites-for-automatic-flow"></a>Prerequisiti aggiuntivi per il flusso automatico

* Un utente e un membro del dominio di Azure AD.

    Questo membro verrà usato per la connessione dell'account di Video Indexer ad Azure.

    L'utente deve essere un membro nella sottoscrizione di Azure con un ruolo **Proprietario** o con i ruoli **Collaboratore** e **Amministratore accessi utente**. Un utente può essere aggiunto due volte, con due ruoli. una volta con quello di collaboratore e una seconda volta con quello di amministratore degli accessi.

    ![controllo di accesso](./media/create-account/access-control-iam.png)

### <a name="additional-prerequisites-for-manual-flow"></a>Prerequisiti aggiuntivi per il flusso manuale

* Registrare il provider di risorse EventGrid usando il portale di Azure.

    Nel [portale di Azure](https://portal.azure.com/) passare a **Sottoscrizioni**->[sottoscrizione]->**Provider di risorse**.

    Cercare **Microsoft.Media** e **Microsoft.EventGrid**. Se lo stato non è "Registrato", fare clic su **Registra**. La registrazione richiede alcuni minuti.

    ![EventGrid](./media/create-account/event-grid.png)

## <a name="connect-to-azure"></a>Connettersi ad Azure

> [!NOTE]
> Se la sottoscrizione di Azure usa l'autenticazione a più fattori basata su certificati, è fondamentale eseguire i passaggi seguenti in un dispositivo in cui sono installati i certificati necessari.

1. Passare al sito Web di [Video Indexer](https://www.videoindexer.ai/) ed eseguire l'accesso.

2. Selezionare il pulsante **Crea nuovo account** :

    ![Crea nuovo account Video Indexer](./media/create-account/connect-to-azure.png)

3. Quando viene visualizzato l'elenco delle sottoscrizioni, selezionare la sottoscrizione da usare.

    ![Connetti Video Indexer ad Azure](./media/create-account/connect-vi-to-azure-subscription.png)

4. Selezionare un'area di Azure dalle posizioni supportate: Stati Uniti occidentali 2, Europa settentrionale o Asia orientale.
5. In **Account Servizi multimediali di Azure** scegliere una delle opzioni seguenti:

    * Per creare un nuovo account di Servizi multimediali, selezionare **Crea nuovo gruppo di risorse**. Specificare il nome per il gruppo di risorse.

        Azure creerà il nuovo account nella sottoscrizione, incluso un nuovo account di Archiviazione di Azure. Il nuovo account di servizi multimediali ha una configurazione iniziale predefinita con un endpoint di streaming e 10 unità riservate S3.
    * Per usare un account di Servizi multimediali esistente, selezionare **Usa risorsa esistente**. Nell'elenco degli account selezionare l'account.

        L'account di Servizi multimediali deve avere la stessa area dell'account di Video Indexer.

        > [!NOTE]
        > Per ridurre al minimo la durata dell'indicizzazione e la velocità effettiva bassa, è consigliabile modificare il tipo e il numero di [unità riservate](../previous/media-services-scale-media-processing-overview.md ) nell'account di servizi multimediali a **10 unità riservate S3**. Vedere [usare il portale per modificare le unità riservate](../previous/media-services-portal-scale-media-processing.md).

    * Per configurare manualmente la connessione, selezionare il collegamento **passa a configurazione manuale** .

        Per informazioni dettagliate, vedere la sezione seguente [Connettersi ad Azure manualmente (opzione avanzata)](#connect-to-azure-manually-advanced-option).
6. Al termine, scegliere **Connetti**. L'operazione potrebbe richiedere alcuni minuti.

    Dopo la connessione ad Azure, il nuovo account di Video Indexer viene visualizzato nell'elenco degli account:

    ![nuovo account](./media/create-account/new-account.png)

7. Passare al nuovo account.

## <a name="connect-to-azure-manually-advanced-option"></a>Connettersi ad Azure manualmente (opzione avanzata)

Se la connessione ad Azure non è riuscita, è possibile tentare di risolvere il problema connettendosi manualmente.

> [!NOTE]
> È consigliabile che nella stessa area siano presenti i tre account seguenti: l'account Video Indexer che si sta connettendo con l'account di servizi multimediali, nonché l'account di archiviazione di Azure connesso allo stesso account di servizi multimediali.

### <a name="create-and-configure-a-media-services-account"></a>Creare e configurare un account di Servizi multimediali di Azure

1. Usare il portale di [Azure](https://portal.azure.com/) per creare un account di Servizi multimediali di Azure, come descritto in [Creare un account](../previous/media-services-portal-create-account.md).

    Quando si crea un account di archiviazione per l'account di servizi multimediali, selezionare **archiviazione V2** per tipo di account e **ridondanza geografica (GRS)** per i campi di replica.

    ![Nuovo account AMS](./media/create-account/create-ams-account1.png)

    > [!NOTE]
    > Assicurarsi di annotare i nomi della risorsa e dell'account di Servizi multimediali. Saranno necessari per i passaggi nella sezione successiva.

2. Modificare il tipo e il numero di [unità riservate](../previous/media-services-scale-media-processing-overview.md ) in **10 unità riservate S3** nell'account di servizi multimediali creato. Vedere [usare il portale per modificare le unità riservate](../previous/media-services-portal-scale-media-processing.md).
3. Prima di poter riprodurre i video nell'app Web di Video Indexer, è necessario avviare l' **endpoint di streaming** predefinito del nuovo account di servizi multimediali.

    Nel nuovo account di servizi multimediali selezionare **endpoint di streaming**. Selezionare quindi l'endpoint di streaming e premere Avvia.

    ![Nuovo account AMS](./media/create-account/create-ams-account2.png)

4. Per Video Indexer di eseguire l'autenticazione con l'API di servizi multimediali, è necessario creare un'app AD. I passaggi seguenti consentono di eseguire il processo di autenticazione Azure AD descritto in [Introduzione all'autenticazione di Azure AD tramite il portale di Azure](../previous/media-services-portal-get-started-with-aad.md):

    1. Nel nuovo account di Servizi multimediali selezionare **Accesso all'API**.
    2. Selezionare il [metodo di autenticazione basata sull'entità servizio](../previous/media-services-portal-get-started-with-aad.md).
    3. Ottenere l'ID client e il segreto client

        Dopo aver selezionato **le chiavi delle impostazioni** -> **Keys**, aggiungere la **Descrizione**, fare clic su **Salva**e il valore della chiave viene popolato.

        Se la chiave scade, il proprietario dell'account dovrà contattare Video Indexer supporto per rinnovare la chiave.

        > [!NOTE]
        > Assicurarsi di annotare il valore della chiave e l'ID applicazione. Sarà necessaria per i passaggi descritti nella sezione successiva.

### <a name="connect-manually"></a>Connettersi manualmente

Nella finestra di dialogo **Connect Video Indexer to an Azure subscription** (Connetti Video Indexer a una sottoscrizione di Azure) della pagina [Video Indexer](https://www.videoindexer.ai/) fare clic sul collegamento **Switch to manual configuration** (Passa a configurazione manuale).

Nella finestra di dialogo specificare le informazioni seguenti:

|Impostazione|Descrizione|
|---|---|
|Area dell'account di Video Indexer|Il nome dell'area dell'account di Video Indexer. Per ottenere prestazioni migliori e ridurre i costi, è consigliabile specificare il nome dell'area in cui si trovano le risorse di servizi multimediali di Azure e l'account di archiviazione di Azure. |
|Tenant di Azure AD|Il nome del tenant di Azure AD, ad esempio "contoso.onmicrosoft.com". Le informazioni sul tenant possono essere recuperate dal portale di Azure. Posizionare il cursore sul nome dell'utente connesso nell'angolo in alto a destra. Trovare il nome a destra di **Dominio**.|
|ID sottoscrizione|La sottoscrizione di Azure in cui deve essere creata la connessione. L'ID sottoscrizione può essere recuperato dal portale di Azure. Selezionare **tutti i servizi** nel riquadro a sinistra e cercare "sottoscrizioni". Selezionare **Sottoscrizioni** e scegliere l'ID desiderato dall'elenco delle sottoscrizioni.|
|Nome del gruppo di risorse di Servizi multimediali di Azure|Nome del gruppo di risorse in cui è stato creato l'account di Servizi multimediali.|
|Nome della risorsa dei servizi multimediali|Nome dell'account di Servizi multimediali di Azure creato nella sezione precedente.|
|ID applicazione|ID dell'applicazione di Azure AD con le autorizzazioni per l'account di Servizi multimediali specificato creato nella sezione precedente.|
|Chiave applicazione|Chiave dell'applicazione Azure AD creata nella sezione precedente. |

## <a name="import-your-content-from-the-trial-account"></a>Importa il contenuto dall'account di *valutazione*

Quando si [Crea un nuovo account](#connect-to-azure), è possibile importare il contenuto dall'account di *valutazione* al nuovo account. Se si seleziona l'opzione *Importa* nella finestra di dialogo **Crea un nuovo account in una sottoscrizione di Azure** , tutte le personalizzazioni di supporti e modelli di contenuto verranno copiate dall'account di *valutazione* al nuovo account.

La possibilità di importare il contenuto è valida per gli approcci automatici e manuali descritti in precedenza.

> [!NOTE]
> Il contenuto può essere importato una sola volta da ogni account.

## <a name="considerations"></a>Considerazioni

Tenere presenti le seguenti considerazioni relative a Servizi multimediali di Azure:

* Se la connessione è stata eseguita automaticamente, nella sottoscrizione di Azure vengono visualizzati un nuovo gruppo di risorse, un account di Servizi multimediali e un account di archiviazione.
* Se la connessione viene eseguita automaticamente, Video Indexer imposta le **Unità riservate** multimediali su 10 unità S3:

    ![Unità riservate di Servizi multimediali](./media/create-account/ams-reserved-units.png)

* Se ci si connette a un account di servizi multimediali esistente, Video Indexer non modifica la configurazione di media **reserved Units** esistente.

   Potrebbe essere necessario modificare il tipo e il numero di media reserved unit in base al carico pianificato. Tenere presente che se il carico è elevato e le unità o la velocità non sono sufficienti, possono verificarsi errori di timeout durante l'elaborazione video.

* Se viene eseguita la connessione a un nuovo account di Servizi multimediali, Video Indexer avvia automaticamente l'**endpoint di streaming** predefinito nell'account:

    ![Endpoint di streaming di Servizi multimediali](./media/create-account/ams-streaming-endpoint.png)

    Gli endpoint di streaming hanno un tempo di avvio considerevole. Potrebbero quindi essere necessari alcuni minuti a partire dal momento in cui l'account è stato connesso ad Azure fino a quando i video non possono essere trasmessi e controllati nell'app Web Video Indexer.

* Se ci si connette a un account di servizi multimediali esistente, Video Indexer non modifica la configurazione dell'endpoint di streaming predefinito. Se non è presente alcun **endpoint di streaming**in esecuzione, non è possibile guardare i video di questo account di servizi multimediali o in video Indexer.

## <a name="next-steps"></a>Passaggi successivi

È possibile interagire a livello di codice con l'account di valutazione e/o con gli account Video Indexer connessi ad Azure seguendo le istruzioni riportate in: [usare le API](video-indexer-use-apis.md).

È consigliabile usare lo stesso utente di Azure AD usato durante la connessione ad Azure.
