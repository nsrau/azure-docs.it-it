---
title: Gestire le credenziali dell'account di archiviazione StorSimple per i dispositivi Microsoft Azure StorSimple serie 8000 | Microsoft Docs
description: Viene illustrato come utilizzare la pagina Configura di Gestione dispositivi StorSimple per aggiungere, modificare, eliminare o ruotare le chiavi di protezione per un account di archiviazione.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 36058ad69ea670998b50cf9038741c294a5b79ab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-storage-account-credentials"></a>Usare il servizio Gestione dispositivi StorSimple per gestire le credenziali degli account di archiviazione

## <a name="overview"></a>Panoramica

La sezione **Configurazione** del pannello del servizio Gestione dispositivi StorSimple illustra tutti i parametri globali del servizio che possono essere creati nel servizio Gestione dispositivi StorSimple. Questi parametri possono essere applicati a tutti i dispositivi connessi al servizio e includono:

* Credenziali dell'account di archiviazione
* Modelli di larghezza di banda 
* Record di controllo di accesso 

In questa esercitazione viene illustrato come aggiungere, modificare o eliminare le credenziali degli account di archiviazione o ruotare le chiavi di protezione per un account di archiviazione.

 ![Elenco di credenziali dell'account di archiviazione](./media/storsimple-8000-manage-storage-accounts/createnewstorageacct6.png)  

Gli account di archiviazione contengono le credenziali usate dal dispositivo StorSimple per accedere all'account di archiviazione con il provider di servizi cloud. Per gli account di archiviazione di Microsoft Azure, queste sono le credenziali, ad esempio il nome dell'account e la chiave di accesso primaria. 

Nel pannello **Credenziali dell'account di archiviazione** tutti gli account di archiviazione creati per la sottoscrizione di fatturazione vengono visualizzati in un formato tabulare con le informazioni seguenti:

* **Nome** : il nome univoco assegnato all'account al momento della creazione.
* **SSL abilitato** : se SSL è abilitato e la comunicazione tra dispositivo e cloud avviene tramite il canale protetto.
* **Utilizzato da** : il numero di volumi che utilizzano l’account di archiviazione.

Le attività più comuni relative agli account di archiviazione che possono essere eseguite sono:

* Aggiungere un account di archiviazione 
* Modificare un account di archiviazione 
* Eliminare un account di archiviazione 
* Rotazione delle chiavi degli account di archiviazione 

## <a name="types-of-storage-accounts"></a>Tipi di account di archiviazione

Esistono tre tipi di account di archiviazione che è possibile utilizzare con il dispositivo StorSimple.

* **Account di archiviazione generati automaticamente** : come suggerito dal nome, questo tipo di account di archiviazione viene generato automaticamente quando viene inizialmente creato il servizio. Per altre informazioni sulla creazione di questo account di archiviazione, vedere il [Passaggio 1: Creare un nuovo servizio](storsimple-8000-deployment-walkthrough-u2.md#step-1-create-a-new-service) in [Distribuire il dispositivo StorSimple locale](storsimple-8000-deployment-walkthrough-u2.md). 
* **Account di archiviazione nella sottoscrizione del servizio** : sono gli account di archiviazione di Azure associati alla stessa sottoscrizione del servizio. Per ulteriori informazioni su come vengono creati questi account di archiviazione, vedere [Informazioni sugli account di archiviazione di Azure](../storage/common/storage-create-storage-account.md). 
* **Account di archiviazione esterni alla sottoscrizione al servizio** : sono gli account di archiviazione di Azure non associati al servizio e probabilmente esistenti prima che il servizio fosse creato.

## <a name="add-a-storage-account"></a>Aggiungere un account di archiviazione

È possibile aggiungere un account di archiviazione fornendo un nome descrittivo univoco e le credenziali di accesso collegate all'account di archiviazione (con il provider del servizio cloud specificato). È inoltre possibile scegliere di abilitare la modalità SSL (Secure Socket Layer) per creare un canale protetto per la comunicazione di rete tra il dispositivo e il cloud.

È possibile creare più account per un provider del servizio cloud specificato. Tenere presente, tuttavia, che dopo aver creato un account di archiviazione non è possibile modificare il provider di servizi cloud.

Durante il salvataggio dell'account di archiviazione, il servizio tenta di comunicare con il provider di servizi cloud. Le credenziali e i dati di accesso forniti verranno autenticati in questa fase. L’account di archiviazione viene creato solo se l'autenticazione ha esito positivo. Se l'autenticazione non riesce, verrà visualizzato un messaggio di errore appropriato.

Usare le procedure seguenti per aggiungere le credenziali dell'account di archiviazione di Azure:

* Per aggiungere una credenziale dell'account di archiviazione con la stessa sottoscrizione di Azure del servizio Gestione dispositivi
* Per aggiungere le credenziali di un account di archiviazione di Azure al di fuori della sottoscrizione del servizio Gestione dispositivi

[!INCLUDE [add-a-storage-account-update2](../../includes/storsimple-8000-configure-new-storage-account-u2.md)]

#### <a name="to-add-an-azure-storage-account-credential-outside-of-the-storsimple-device-manager-service-subscription"></a>Per aggiungere le credenziali di un account di archiviazione di Azure al di fuori della sottoscrizione del servizio Gestione dispositivi StorSimple

1. Passare al servizio Gestione dispositivi StorSimple, selezionare e fare doppio clic. Si apre il pannello **Panoramica**.
2. Selezionare **Credenziali dell'account di archiviazione** nella sezione **Configurazione**. Elenca le credenziali di un qualsiasi account di archiviazione esistente associate al servizio Gestione dispositivi StorSimple.
3. Fare clic su **Aggiungi**.
4. Nel pannello **Aggiungi credenziali dell'account di archiviazione** seguire questa procedura:
   
    1. Per **Sottoscrizione** selezionare **Altro**.
   
    2. Specificare il nome della credenziale dell'account di archiviazione di Azure.
   
    3. Nella casella di testo **Chiave di accesso dell'account di archiviazione** specificare la chiave di accesso primaria per la credenziale dell'account di archiviazione di Azure. Per ottenere questa chiave, passare al servizio Archiviazione di Azure, selezionare la credenziale dell'account di archiviazione e fare clic su **Gestisci chiavi di accesso**. È ora possibile copiare la chiave di accesso primaria.
   
    4. Per abilitare SSL, fare clic sul pulsante **Abilita** per creare un canale sicuro per la comunicazione di rete tra il servizio Gestione dispositivi StorSimple e il cloud. Fare clic sul pulsante **Disabilita** solo se si opera all'interno di un cloud privato.
   
    5. Fare clic su **Aggiungi**. Quando la creazione della credenziale dell'account di archiviazione viene completata, si riceve una notifica.

5. La credenziale dell'account di archiviazione appena creata viene visualizzata nel pannello del servizio Gestione dispositivi StorSimple in **Credenziali dell'account di archiviazione**.
   


## <a name="edit-a-storage-account"></a>Modificare un account di archiviazione

È possibile modificare un account di archiviazione utilizzato da un contenitore di volumi. Se si modifica un account di archiviazione in uso, l'unico campo disponibile per la modifica è la chiave di accesso dell'account di archiviazione. È possibile fornire la nuova chiave di accesso di archiviazione e salvare le impostazioni aggiornate.

#### <a name="to-edit-a-storage-account"></a>Per modificare un account di archiviazione

1. Passare al servizio Gestione dispositivi StorSimple. Nella sezione **Configurazione** fare clic su **Credenziali dell'account di archiviazione**.

    ![Credenziali dell'account di archiviazione](./media/storsimple-8000-manage-storage-accounts/editstorageacct1.png)

2. Nel pannello **Credenziali dell'account di archiviazione** selezionare e fare clic sulle credenziali da modificare nell'elenco. 

3. È possibile modificare la selezione **Abilita SSL**. È anche possibile fare clic su **Altro...**  e selezionare **Sincronizza chiave di accesso** per ruotare le chiavi di accesso agli account di archiviazione. Andare a [Rotazione delle chiavi degli account di archiviazione](#key-rotation-of-storage-accounts) per ulteriori informazioni su come eseguire la rotazione delle chiavi. Dopo avere modificato le impostazioni, fare clic su **Salva**. 

    ![Salvare le credenziali dell'account di archiviazione modificate](./media/storsimple-8000-manage-storage-accounts/editstorageacct3.png)

4. Alla richiesta di conferma fare clic su **Sì**. 

    ![Confermare le modifiche](./media/storsimple-8000-manage-storage-accounts/editstorageacct4.png)

Le impostazioni verranno aggiornate e salvate per l'account di archiviazione. 

## <a name="delete-a-storage-account"></a>Eliminare un account di archiviazione

> [!IMPORTANT]
> È possibile eliminare un account di archiviazione solo se non viene utilizzato da un contenitore di volumi. Se un account di archiviazione viene utilizzato da un contenitore di volumi, eliminare prima il contenitore di volumi e poi l'account di archiviazione associato.

#### <a name="to-delete-a-storage-account"></a>Per eliminare un account di archiviazione

1. Passare al servizio Gestione dispositivi StorSimple. Nella sezione **Configurazione** fare clic su **Credenziali dell'account di archiviazione**.

2. Nell'elenco tabulare di account di archiviazione, passare il mouse sull'account che si desidera eliminare. Fare clic con il pulsante destro del mouse per richiamare il menu di scelta rapida e quindi fare clic su **Elimina**.

    ![Eliminare le credenziali dell'account di archiviazione](./media/storsimple-8000-manage-storage-accounts/deletestorageacct1.png)

3. Quando viene richiesta la conferma, fare clic su **Sì** per continuare con l'eliminazione. L’elenco tabulare verrà aggiornato per riflettere le modifiche.

    ![Conferma dell'eliminazione](./media/storsimple-8000-manage-storage-accounts/deletestorageacct2.png)

## <a name="key-rotation-of-storage-accounts"></a>Rotazione delle chiavi degli account di archiviazione

Per motivi di sicurezza, la rotazione delle chiavi è spesso un requisito nei centri dati. Ogni sottoscrizione di Microsoft Azure può essere associata a uno o più account di archiviazione. L'accesso a tali account di archiviazione è controllato dalle chiavi di sottoscrizione e di accesso associate a ogni account di archiviazione. 

Quando si crea un account di archiviazione, Microsoft Azure genera due chiavi di accesso alle risorse di archiviazione da 512 bit, che vengono utilizzate per l'autenticazione quando si accede all'account di archiviazione. Fornendo due chiavi di accesso alle risorse di archiviazione, Azure consente di rigenerare le chiavi senza interruzioni per il servizio di archiviazione o l'accesso a tale servizio. La chiave in uso è la chiave *primaria* e la chiave di backup è indicata come chiave *secondaria*. Una di queste due chiavi deve essere specificata quando il dispositivo StorSimple di Microsoft Azure accede al provider di servizi di archiviazione cloud.

## <a name="what-is-key-rotation"></a>Che cos'è la rotazione delle chiavi?

In genere, le applicazioni utilizzano solo una delle chiavi per accedere ai dati. Dopo un determinato periodo di tempo, è possibile impostare alcune applicazioni perché utilizzino la seconda chiave. Dopo avere impostato le applicazioni per la chiave secondaria, è possibile ritirare la prima chiave e quindi generare una nuova chiave. Utilizzando due chiavi in questo modo le applicazioni possono accedere ai dati senza tempi di inattività.

Le chiavi degli account di archiviazione vengono sempre archiviate nel servizio in formato crittografato. Tuttavia, possono essere reimpostate tramite il servizio Gestione dispositivi StorSimple. Il servizio consente di ottenere la chiave primaria e la chiave secondaria per tutti gli account di archiviazione nella stessa sottoscrizione, inclusi gli account creati nel servizio di archiviazione e gli account di archiviazione predefiniti generati in fase di creazione del servizio Gestione dispositivi StorSimple. Il servizio Gestione dispositivi StorSimple otterrà sempre queste chiavi dal portale di Azure classico e le archivierà in modo crittografato.

## <a name="rotation-workflow"></a>Flusso di lavoro di rotazione

Un amministratore di Microsoft Azure può rigenerare oppure modificare la chiave primaria o secondaria accedendo direttamente all'account di archiviazione (tramite il servizio di archiviazione di Microsoft Azure). Il servizio Gestione dispositivi StorSimple non visualizza la modifica automaticamente.

Per comunicare la modifica al servizio Gestione dispositivi StorSimple, sarà necessario accedere al servizio Gestione dispositivi StorSimple, accedere all'account di archiviazione e quindi sincronizzare la chiave primaria o secondaria, a seconda di quale chiave è stata modificata. Quindi, il servizio ottiene la chiave più recente, la crittografa e la invia al dispositivo.

#### <a name="to-synchronize-keys-for-storage-accounts-in-the-same-subscription-as-the-service"></a>Per sincronizzare le chiavi per gli account di archiviazione nella stessa sottoscrizione del servizio 
1. Passare al servizio Gestione dispositivi StorSimple. Nella sezione **Configurazione** fare clic su **Credenziali dell'account di archiviazione**.
2. Dall'elenco tabulare degli account di archiviazione, fare clic su quello che si desidera modificare. 

    ![sincronizzare le chiavi](./media/storsimple-8000-manage-storage-accounts/syncaccesskey1.png)

3. Fare clic su **...Altro** e quindi selezionare **Sincronizza chiave di accesso per ruotare**.   

    ![sincronizzare le chiavi](./media/storsimple-8000-manage-storage-accounts/syncaccesskey2.png)

4. Nel servizio Gestione dispositivi StorSimple è necessario aggiornare la chiave precedentemente modificata nel servizio Archiviazione di Microsoft Azure. Se la chiave di accesso primaria è stata modificata (rigenerata), selezionare la chiave **primaria**. Se è stata modificata la chiave secondaria, selezionare la chiave **secondaria**. Fare clic su **Sincronizza chiave**.
      
      ![sincronizzare le chiavi](./media/storsimple-8000-manage-storage-accounts/syncaccesskey3.png)

Al termine della sincronizzazione della chiave, si riceve una notifica.

#### <a name="to-synchronize-keys-for-storage-accounts-outside-of-the-service-subscription"></a>Per sincronizzare le chiavi per gli account di archiviazione esterni alla sottoscrizione del servizio
1. Nella pagina **Servizi** fare clic sulla scheda **Configura**.
2. Fare clic su **Aggiungi/modifica account di archiviazione**.
3. Nella finestra di dialogo procedere come segue:
   
   1. Selezionare l'account di archiviazione con la chiave di accesso che si desidera aggiornare.
   2. Sarà necessario aggiornare la chiave di accesso di archiviazione nel servizio Gestione dispositivi StorSimple. In questo caso, è possibile visualizzare la chiave di accesso di archiviazione. Immettere la nuova chiave nella casella **Chiave di accesso dell'account di archiviazione**. 
   3. Salvare le modifiche. La chiave di accesso dell’account di archiviazione appare aggiornata.

## <a name="next-steps"></a>Passaggi successivi
* Ulteriori informazioni sulla [sicurezza di StorSimple](storsimple-8000-security.md).
* Altre informazioni sull'[uso del servizio Gestione dispositivi StorSimple per amministrare il dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

