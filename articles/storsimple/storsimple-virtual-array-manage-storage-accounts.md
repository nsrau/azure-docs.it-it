---
title: Gestire le credenziali dell'account di archiviazione per l'array virtuale StorSimple | Documentazione Microsoft
description: Spiega come usare la pagina Configura Gestione dispositivi di StorSimple per aggiungere, modificare, eliminare o ruotare le chiavi di sicurezza per le credenziali dell'account di archiviazione associate all'array virtuale StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 234bf8bb-d5fe-40be-9d25-721d7482bc3b
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: a4ce2d329d0e1399cffaf886adf2b95e34b9cd7b
ms.contentlocale: it-it
ms.lasthandoff: 08/21/2017

---
# <a name="use-storsimple-device-manager-to-manage-storage-account-credentials-for-storsimple-virtual-array"></a>Usare Gestione dispositivi StorSimple per gestire le credenziali dell'account di archiviazione per l'array virtuale StorSimple

## <a name="overview"></a>Panoramica
La sezione **Configurazione** del pannello del servizio Gestione dispositivi StorSimple dell'array virtuale StorSimple illustra i parametri globali del servizio che possono essere creati nel servizio StorSimple Manager. Questi parametri possono essere applicati a tutti i dispositivi connessi al servizio e includono:

* Credenziali dell'account di archiviazione
* Record di controllo di accesso
  
  ![Dashboard del servizio Gestione dispositivi](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccts-dashboard.png)  

Questa esercitazione illustra come aggiungere, modificare o eliminare le credenziali dell'account di archiviazione per l'array virtuale StorSimple. Le informazioni contenute in questa esercitazione si applicano solo all'array virtuale StorSimple. Per informazioni su come gestire gli account di archiviazione della serie 8000, vedere [Utilizzare il servizio StorSimple Manager per gestire l'account di archiviazione](storsimple-manage-storage-accounts.md).

Le credenziali dell'account di archiviazione contengono le credenziali usate dal dispositivo per accedere all'account di archiviazione con il provider di servizi cloud. Per gli account di archiviazione di Microsoft Azure, queste sono le credenziali, ad esempio il nome dell'account e la chiave di accesso primaria.

Nel pannello **Credenziali dell'account di archiviazione** tutte le credenziali dell'account di archiviazione create per la sottoscrizione di fatturazione vengono visualizzate in un formato tabulare con le informazioni seguenti:

* **Nome** : il nome univoco assegnato all'account al momento della creazione.
* **SSL abilitato** : se SSL è abilitato e la comunicazione tra dispositivo e cloud avviene tramite il canale protetto.
  
  ![Sezione Configurazione](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccountcredentials-blade.png)

Le attività più comuni relative alle credenziali dell'account di archiviazione che possono essere eseguite nel pannello **Credenziali dell'account di archiviazione** sono:

* Aggiungere una credenziale dell'account di archiviazione
* Modificare una credenziale dell'account di archiviazione
* Eliminare una credenziale dell'account di archiviazione

## <a name="types-of-storage-account-credentials"></a>Tipi di credenziali dell'account di archiviazione
Esistono tre tipi di credenziali dell'account di archiviazione che è possibile usare con il dispositivo StorSimple.

* **Credenziali dell'account di archiviazione generate automaticamente**: come il nome suggerisce, questo tipo di credenziale viene generato automaticamente alla creazione del servizio. Per altre informazioni sulla creazione della credenziale dell'account di archiviazione, vedere [Creare un nuovo servizio](storsimple-virtual-array-manage-service.md#create-a-service).
* **Credenziali dell'account di archiviazione nella sottoscrizione del servizio**: le credenziali dell'account di archiviazione di Azure associate alla stessa sottoscrizione del servizio. Per altre informazioni su come vengono create queste credenziali dell'account di archiviazione, vedere [Informazioni sugli account di archiviazione di Azure](../storage/common/storage-create-storage-account.md).
* **Credenziali dell'account di archiviazione esterne alla sottoscrizione del servizio**: le credenziali dell'account di archiviazione di Azure non associate al servizio e probabilmente esistenti prima che il servizio fosse creato.

## <a name="add-a-storage-account-credential"></a>Aggiungere una credenziale dell'account di archiviazione
È possibile aggiungere una credenziale dell'account di archiviazione al servizio Gestione dispositivi StorSimple indicando un nome descrittivo univoco e le credenziali di accesso collegate all'account di archiviazione. È inoltre possibile scegliere di abilitare la modalità SSL (Secure Socket Layer) per creare un canale protetto per la comunicazione di rete tra il dispositivo e il cloud.

È possibile creare più account per un provider del servizio cloud specificato. Durante il salvataggio della credenziale dell'account di archiviazione, il servizio tenta di comunicare con il provider di servizi cloud. Le credenziali e i dati di accesso forniti vengono autenticati in questa fase. La credenziale dell'account di archiviazione viene creata solo se l'autenticazione ha esito positivo. Se l'autenticazione non riesce, viene visualizzato un messaggio di errore appropriato.

Usare le procedure seguenti per aggiungere le credenziali dell'account di archiviazione di Azure:

* Per aggiungere una credenziale dell'account di archiviazione con la stessa sottoscrizione di Azure del servizio Gestione dispositivi
* Per aggiungere le credenziali di un account di archiviazione di Azure al di fuori della sottoscrizione del servizio Gestione dispositivi

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>Per aggiungere una credenziale dell'account di archiviazione con la stessa sottoscrizione di Azure del servizio Gestione dispositivi

1. Passare al servizio Gestione dispositivi, selezionare e fare doppio clic. Si apre il pannello **Panoramica**.
2. Selezionare **Credenziali dell'account di archiviazione** nella sezione **Configurazione**.
3. Fare clic su **Aggiungi**.
4. Nel pannello **Aggiungi un account di archiviazione** eseguire le operazioni seguenti:
   
    1. Per **Sottoscrizione** selezionare **Corrente**.
    2. Specificare il nome dell'account di archiviazione di Azure.
    3. Selezionare**Abilita** per creare un canale sicuro per la comunicazione di rete tra il dispositivo StorSimple e il cloud. Selezionare **Disabilita** solo se si opera all'interno di un cloud privato.
    4. Fare clic su **Aggiungi**. Quando la creazione dell'account di archiviazione viene completata, si riceve una notifica.<br></br>
   
        ![Aggiungere le credenziali di un account di archiviazione esistente](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

#### <a name="to-add-an-azure-storage-account-credential-that-is-outside-of-the-device-manager-service-subscription"></a>Per aggiungere le credenziali di un account di archiviazione di Azure al di fuori della sottoscrizione del servizio Gestione dispositivi

1. Passare al servizio Gestione dispositivi, selezionare e fare doppio clic. Si apre il pannello **Panoramica**.
2. Selezionare **Credenziali dell'account di archiviazione** nella sezione **Configurazione**. Elenca le credenziali di un qualsiasi account di archiviazione esistente associate al servizio Gestione dispositivi StorSimple.
3. Fare clic su **Aggiungi**.
4. Nel pannello **Aggiungi un account di archiviazione** eseguire le operazioni seguenti:
   
    1. Per **Sottoscrizione** selezionare **Altro**.
   
    2. Specificare il nome della credenziale dell'account di archiviazione di Azure.
   
    3. Nella casella di testo **Chiave di accesso dell'account di archiviazione** specificare la chiave di accesso primaria per la credenziale dell'account di archiviazione di Azure. Per ottenere questa chiave, passare al servizio Archiviazione di Azure, selezionare la credenziale dell'account di archiviazione e fare clic su **Gestisci chiavi di accesso**. È ora possibile copiare la chiave di accesso primaria.
   
    4. Per abilitare SSL, fare clic sul pulsante **Abilita** per creare un canale sicuro per la comunicazione di rete tra il servizio Gestione dispositivi StorSimple e il cloud. Fare clic sul pulsante **Disabilita** solo se si opera all'interno di un cloud privato.
   
    5. Fare clic su **Aggiungi**. Quando la creazione della credenziale dell'account di archiviazione viene completata, si riceve una notifica.

5. La credenziale dell'account di archiviazione appena creata viene visualizzata nel pannello del servizio Gestione dispositivi StorSimple in **Credenziali dell'account di archiviazione**.
   
    ![Aggiungere una credenziale dell'account di archiviazione all'esterno della sottoscrizione del servizio Gestione dispositivi](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-outside-storageacct.png)

## <a name="edit-a-storage-account-credential"></a>Modificare una credenziale dell'account di archiviazione
È possibile modificare una credenziale dell'account di archiviazione usata dal dispositivo. Se si modifica una credenziale dell'account di archiviazione attualmente in uso, i campi disponibili per la modifica sono la chiave di accesso e la modalità SSL per la credenziale dell'account di archiviazione. È possibile indicare la nuova chiave di accesso alle risorse di archiviazione o modificare la selezione **Abilita modalità SSL** e salvare le impostazioni aggiornate.

#### <a name="to-edit-a-storage-account-credential"></a>Per modificare una credenziale dell'account di archiviazione
1. Passare al servizio Gestione dispositivi, selezionare e fare doppio clic. Si apre il pannello **Panoramica**.
2. Selezionare **Credenziali dell'account di archiviazione** nella sezione **Configurazione**. Elenca le credenziali di un qualsiasi account di archiviazione esistente associate al servizio Gestione dispositivi StorSimple.
3. Nell'elenco tabulare delle credenziali dell'account di archiviazione, selezionare e fare doppio clic sull'account che si vuole modificare.
4. Nel pannello **Proprietà** delle credenziali dell'account di archiviazione eseguire le operazioni seguenti:
   
   1. Se necessario, è possibile modificare la selezione **Abilita SSL**.
   2. È possibile rigenerare le chiavi di accesso delle credenziali dell'account di archiviazione. Per altre informazioni, vedere l'articolo [Rigenerazione delle chiavi di accesso alle risorse di archiviazione](../storage/common/storage-create-storage-account.md#manage-your-storage-access-keys). Indicare la nuova chiave della credenziale dell'account di archiviazione. Per un account di archiviazione di Azure si tratta della chiave di accesso primaria.
   3. Fare clic su **Salva** nella parte superiore del pannello **Proprietà** per salvare le impostazioni. Le impostazioni vengono aggiornate nel pannello **Credenziali dell'account di archiviazione**.
      
      ![Modificare una credenziale dell'account di archiviazione](./media/storsimple-virtual-array-manage-storage-accounts/ova-edit-storageacct.png)

## <a name="delete-a-storage-account-credential"></a>Eliminare una credenziale dell'account di archiviazione
> [!IMPORTANT]
> È possibile eliminare una credenziale dell'account di archiviazione solo se non è in uso. Se una credenziale dell'account di archiviazione è in uso, si riceve una notifica.
> 
> 

#### <a name="to-delete-a-storage-account-credential"></a>Per eliminare una credenziale dell'account di archiviazione
1. Passare al servizio Gestione dispositivi, selezionare e fare doppio clic. Si apre il pannello **Panoramica**.
2. Selezionare **Credenziali dell'account di archiviazione** nella sezione **Configurazione**. Elenca le credenziali di un qualsiasi account di archiviazione esistente associate al servizio Gestione dispositivi StorSimple.
3. Nell'elenco tabulare delle credenziali dell'account di archiviazione, selezionare e fare doppio clic sull'account che si vuole eliminare.
4. Nel pannello **Proprietà** delle credenziali dell'account di archiviazione eseguire le operazioni seguenti:
   
   1. Fare clic su **Elimina** per eliminare le credenziali.
   2. Quando viene richiesta la conferma, fare clic su **Sì** per continuare con l'eliminazione. L'elenco tabulare viene aggiornato per riflettere le modifiche.
      
      ![Eliminare una credenziale dell'account di archiviazione](./media/storsimple-virtual-array-manage-storage-accounts/ova-del-storageacct.png)

## <a name="synchronizing-storage-account-credential-keys"></a>Sincronizzazione delle chiavi della credenziale dell'account di archiviazione esistente
Per motivi di sicurezza, la rotazione delle chiavi è spesso un requisito nei centri dati. Un amministratore di Microsoft Azure può rigenerare oppure modificare la chiave primaria o secondaria accedendo direttamente alla credenziale dell'account di archiviazione tramite il servizio Archiviazione di Microsoft Azure. Il servizio Gestione dispositivi StorSimple non visualizza la modifica automaticamente.

Per comunicare la modifica al servizio Gestione dispositivi StorSimple, è necessario accedere al servizio Gestione dispositivi StorSimple, accedere alla credenziale dell'account di archiviazione e quindi sincronizzare la chiave primaria o secondaria, a seconda di quale chiave è stata modificata. Quindi, il servizio ottiene la chiave più recente, la crittografa e la invia al dispositivo.

#### <a name="to-synchronize-keys-for-storage-account-credentials-in-the-same-subscription-as-the-service-azure-only"></a>Per sincronizzare le chiavi per le credenziali dell'account di archiviazione nella stessa sottoscrizione del servizio (solo Azure)
1. Nel pannello di destinazione del servizio selezionare il servizio, fare doppio clic sul nome del servizio e nella sezione **Configurazione** fare clic su **Credenziali dell'account di archiviazione**.
2. Nell'elenco delle credenziali dell'account di archiviazione nel pannello **Credenziali dell'account di archiviazione** selezionare la credenziale dell'account di archiviazione di cui si vuole sincronizzare le chiavi.
3. Nel pannello **Proprietà** per le credenziali dell'account di archiviazione selezionato eseguire le operazioni seguenti:
   
    1. Fare clic su **Altro**, quindi su **Sincronizza chiave di accesso**.
   
    2. Quando viene richiesta la conferma, fare clic su **Sincronizza chiave** per completare la sincronizzazione.
    
4. Nel servizio Gestione dispositivi StorSimple è necessario aggiornare la chiave precedentemente modificata nel servizio Archiviazione di Microsoft Azure. Nel pannello **Sincronizza la chiave dell'account di archiviazione**, se la chiave di accesso primaria è stata modificata (rigenerata), fare clic su Primaria e quindi su **Sincronizza chiave**. Se è stata modificata la chiave secondaria, fare clic su **Secondaria**, quindi fare clic su **Sincronizza chiave**.
   
    ![Sincronizzare la chiave di accesso](./media/storsimple-virtual-array-manage-storage-accounts/ova-sync-acess-key.png)

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [amministrare StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).


