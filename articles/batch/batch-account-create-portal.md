---
title: Creare un account Batch nel portale di Azure | Microsoft Docs
description: Informazioni su come creare un account Azure Batch nel portale di Azure per eseguire carichi di lavoro paralleli su larga scala nel cloud
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/20/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: 053e2ae7f382bc55a6638ccd612289ab2ba64e52
ms.contentlocale: it-it
ms.lasthandoff: 06/20/2017


---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Creare un account Batch nel portale di Azure

> [!div class="op_single_selector"]
> * [Portale di Azure](batch-account-create-portal.md)
> * [.NET per la gestione di Batch](batch-management-dotnet.md)
>
>

Informazioni su come creare un account Batch di Azure nel [portale di Azure][azure_portal], scegliere le proprietà dell'account adatte allo scenario di calcolo. Informazioni su dove trovare le proprietà di account importanti come le chiavi di accesso e gli URL dell'account.

Per informazioni sugli account e gli scenari Batch, vedere la [panoramica della funzionalità](batch-api-basics.md).



## <a name="create-a-batch-account"></a>Creare un account Batch

Usare il portale per creare un account Batch in una delle due *modalità di allocazione del pool*: la modalità **servizio Batch** o la modalità più recente di **sottoscrizione utente**, che richiede una maggiore configurazione. Per informazioni su queste due modalità, vedere la [panoramica della funzionalità](batch-api-basics.md#account). Per le funzionalità della modalità di sottoscrizione utente, vedere anche il [post del blog](https://blogs.technet.microsoft.com/windowshpc/2017/03/17/azure-batch-vnet-and-custom-image-support-for-virtual-machine-pools/).

## <a name="batch-service-mode"></a>Modalità di servizio Batch



1. Accedere al [portale di Azure][azure_portal].
2. Fare clic su **Nuovo** > **Calcolo** > **Servizio Batch**.

    ![Batch in Marketplace][marketplace_portal]
3. Verrà visualizzato il pannello **Nuovo account Batch** . Vedere le descrizioni di seguito di ciascun elemento del pannello.

    ![Creare un account Batch][account_portal]

    a. **Nome account**: il nome dell'account Batch scelto deve essere univoco nell'area di Azure in cui l'account viene creato. Vedere sotto **Località**. Il nome dell'account può contenere solo caratteri minuscoli o numeri e deve avere una lunghezza di 3-24 caratteri.

    b. **Sottoscrizione**: sottoscrizione in cui creare l'account Batch. Se è presente solo una sottoscrizione, è selezionata per impostazione predefinita.

    c. **Modalità di allocazione pool**: selezionare **Batch service**.

    c. **Gruppo di risorse**: selezionare un gruppo di risorse esistente per il nuovo account Batch. È possibile crearne facoltativamente uno nuovo.

    d. **Località**: area di Azure in cui creare l'account Batch. Solo le aree supportate dalla sottoscrizione e dal gruppo di risorse vengono visualizzate come opzioni.

    e. **Account di archiviazione** (facoltativo): account di Archiviazione di Azure per uso generico associato all'account Batch. Tale operazione è consigliata per la maggior parte degli account Batch. Per altri dettagli, vedere più avanti [Account di archiviazione di Azure collegato](#linked-azure-storage-account) .

4. Fare clic su **Crea** per creare l'account.

   Il portale indica che la distribuzione è in corso. Al termine, un viene visualizzata una notifica **Le distribuzioni sono riuscite** in **Notifiche**.

## <a name="user-subscription-mode"></a>Modalità di sottoscrizione utente

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Consentire ad Azure Batch di accedere alla sottoscrizione (operazione occasionale)
Quando si crea il primo account Batch in modalità di sottoscrizione utente, eseguire la procedura seguente per registrare la sottoscrizione con Batch. Se questa operazione è stata fatta precedentemente, passare alla sezione successiva.

1. Accedere al [portale di Azure][azure_portal].

2. Fare clic su **Altri servizi** > **Sottoscrizioni** e quindi fare clic sulla sottoscrizione che si desidera usare per l'account Batch.

3. Nel pannello **Sottoscrizioni**, fare clic su selezionare **Controllo di accesso (IAM)Aggiungi** > **Aggiungi**.

    ![Controllo di accesso alla sottoscrizione][subscription_access]

4. Nel pannello **Aggiungi autorizzazioni** selezionare il ruolo **Collaboratore** e cercare l'API Batch. Cercare ognuna di queste stringhe fino a trovare l'API:
    1. **MicrosoftAzureBatch**.
    2. **Microsoft Azure Batch**. I tenant di Azure AD più recenti potrebbero usare questo nome.
    3. **ddbf3205-c6bd-46ae-8127-60eb93363864** è l'ID dell'API Batch. 

5. Una volta trovata l'API Batch, selezionarla e fare clic su **Salva**.

    ![Aggiungere le autorizzazioni di Batch][add_permission]

### <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi
In modalità di sottoscrizione utente, è necessario un insieme di credenziali delle chiavi di Azure che appartiene allo stesso gruppo di risorse come l'account Batch da creare. Assicurarsi che il gruppo di risorse sia in un'area in cui Batch è [disponibile](https://azure.microsoft.com/regions/services/) e che supporta la sottoscrizione.

1. Nel [portale di Azure][azure_portal] fare clic su **Nuovo** > **Sicurezza e identità** > **Insieme di credenziali delle chiavi**.

2. Nel pannello **Crea l'insieme di credenziali delle chiavi** immettere un nome per l'insieme di credenziali delle chiavi e creare un gruppo di risorse nell'area desiderata per l'account Batch. Lasciare i valori predefiniti per le impostazioni rimanenti, quindi fare clic su **Crea**.

### <a name="create-a-batch-account"></a>Creare un account Batch

1. Nel [portale di Azure][azure_portal] fare clic su **Nuovo** > **Calcola** > **Servizio Batch**.

    ![Batch in Marketplace][marketplace_portal]
3. Verrà visualizzato il pannello **Nuovo account Batch** . Vedere le descrizioni di seguito di ciascun elemento del pannello.

    ![Creare un account Batch][account_portal_byos]

    a. **Nome account**: il nome dell'account Batch scelto deve essere univoco nell'area di Azure in cui l'account viene creato. Vedere sotto **Località**. Il nome dell'account può contenere solo caratteri minuscoli o numeri e deve avere una lunghezza di 3-24 caratteri.

    b. **Sottoscrizione**: se sono disponibili più sottoscrizioni, selezionare la sottoscrizione registrata con il servizio Batch.

    c. **Modalità di allocazione pool**: selezionare **Sottoscrizione utente**.

    d. **Insieme di credenziali delle chiavi**: selezionare l'insieme di credenziali delle chiavi creato per l'account Batch nella sezione precedente. In modo facoltativo creare un nuovo insieme di credenziali delle chiavi. Dopo aver selezionato l'insieme di credenziali, selezionare la casella di controllo per concedere l'accesso di Azure Batch all'insieme di credenziali delle chiavi.

    c. **Gruppo di risorse**: selezionare il gruppo di risorse in cui è stato creato l'insieme di credenziali delle chiavi.

    d. **Località**: area di Azure in cui è stato creato l'insieme di credenziali delle chiavi per l'account Batch.

    e. **Account di archiviazione** (facoltativo): account di Archiviazione di Azure per uso generico associato all'account Batch. Tale operazione è consigliata per la maggior parte degli account Batch. Per altri dettagli, vedere più avanti [Account di archiviazione di Azure collegato](#linked-azure-storage-account) .

4. Fare clic su **Crea** per creare l'account.

   Il portale indica che la distribuzione è in corso. Al termine, un viene visualizzata una notifica **Le distribuzioni sono riuscite** in **Notifiche**.



## <a name="view-batch-account-properties"></a>Visualizzare le proprietà dell'account Batch
Dopo avere creato l'account, è possibile aprire il pannello **Account Batch** per accedere alle impostazioni e alle proprietà. È possibile accedere a tutte le impostazioni e proprietà dell'account usando il menu di sinistra del pannello Account Batch.

![Pannello Account Batch nel portale di Azure][account_blade]

* **URL dell'account Batch**: quando si sviluppa un'applicazione con le [API Batch](batch-apis-tools.md#azure-accounts-for-batch-development), sarà necessario un URL dell'account per accedere alle risorse di Batch. Un URL dell'account Batch ha il seguente formato:

    `https://<account_name>.<region>.batch.azure.com`

![URL dell'account Batch nel portale][account_url]

* **Chiavi di accesso** (modalità servizio Batch): per autenticare l'accesso all'account Batch dall'applicazione sarà necessaria una chiave di accesso dell'account. Questa impostazione non è disponibile in modalità di sottoscrizione utente, in cui si usa l'autenticazione di Azure Active Directory.

    Per visualizzare o rigenerare le chiavi di accesso dell'account Batch, immettere `keys` nella casella di **ricerca** del menu a sinistra nel pannello Account Batch, quindi selezionare **Chiavi**.

    ![Chiavi dell'account Batch nel portale di Azure][account_keys]

[!INCLUDE [batch-pricing-include](../../includes/batch-pricing-include.md)]

## <a name="linked-azure-storage-account"></a>Account di archiviazione di Azure collegato

È possibile facoltativamente collegare un account di archiviazione di Azure per uso generico all'account Batch. La funzionalità [Pacchetti dell'applicazione](batch-application-packages.md) di Batch usa l'archivio BLOB di Azure, proprio come la libreria [Batch File Conventions .NET](batch-task-output.md). Queste funzionalità facoltative facilitano la distribuzione delle applicazioni eseguite dalle attività di Batch e il salvataggio permanente dei dati prodotti.

È consigliabile creare un nuovo account di archiviazione da usare esclusivamente con l'account Batch.

![Creazione di un account di archiviazione "Utilizzo generico"][storage_account]

> [!NOTE]
> Azure Batch supporta attualmente solo il tipo di account di archiviazione per utilizzo generico. Questo tipo di account è descritto nel passaggio 5, [Creare un account di archiviazione] (../storage/storage-create-storage-account.md#create-a-storage-account), in [Informazioni sugli account di archiviazione di Azure](../storage/storage-create-storage-account.md).
>
>

> [!WARNING]
> Prestare attenzione quando si rigenerano le chiavi di accesso di un account di archiviazione collegato. Rigenerare solo una chiave dell'account di archiviazione e fare clic su **Sincronizza chiavi** nel pannello dell'account di archiviazione collegato. Attendere cinque minuti per consentire la propagazione delle chiavi ai nodi di calcolo dei pool, quindi rigenerare e sincronizzare l'altra chiave, se necessario. Se si rigenerano entrambe le chiavi contemporaneamente, i nodi di calcolo non riusciranno a sincronizzare entrambe le chiavi e perderanno l'accesso all'account di archiviazione.
>
>

![Rigenerazione delle chiavi degli account di archiviazione][4]

## <a name="batch-service-quotas-and-limits"></a>Quote e limiti del servizio Batch
Tenere presente che, come alla sottoscrizione di Azure e ad altri servizi di Azure, agli account Batch vengono applicati determinati [limiti e quote](batch-quota-limit.md) . Le quote correnti per un account Batch vengono visualizzate nel portale nelle **Proprietà**dell'account.

![Quote dell'account Batch nel portale di Azure][quotas]



Inoltre, per aumentare molte di queste quote, è sufficiente inviare una richiesta di supporto gratuito per il prodotto nel portale di Azure. Per informazioni dettagliate sulla richiesta di aumenti delle quote, vedere [Quote e limiti per il servizio Azure Batch](batch-quota-limit.md) .

## <a name="other-batch-account-management-options"></a>Altre opzioni di gestione dell'account Batch
Oltre a usare il portale di Azure, è anche possibile creare e gestire account Batch con gli strumenti seguenti:

* [Cmdlet di PowerShell per Batch](batch-powershell-cmdlets-get-started.md)
* [Interfaccia della riga di comando di Azure](batch-cli-get-started.md)
* [.NET per la gestione di Batch](batch-management-dotnet.md)

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sui concetti e sulle funzionalità del servizio Batch, vedere [Panoramica delle funzionalità di Batch](batch-api-basics.md). L'articolo descrive le risorse primarie di Batch, ad esempio i pool, i nodi di calcolo, i processi e le attività e fornisce una panoramica delle funzionalità del servizio che consentono l'esecuzione di carichi di lavoro di calcolo su larga scala.
* Apprendere le nozioni di base dello sviluppo di un'applicazione abilitata per Batch con la [libreria client Batch .NET](batch-dotnet-get-started.md) o con [Python](batch-python-tutorial.md). Gli articoli introduttivi illustrano un'applicazione funzionante che usa il servizio Batch per eseguire un carico di lavoro in più nodi di calcolo e include l'uso di Archiviazione di Azure per lo staging e il recupero dei file del carico di lavoro.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[4]: ./media/batch-account-create-portal/batch_acct_04.png "Rigenerazione delle chiavi degli account di archiviazione"
[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG
[account_url]: ./media/batch-account-create-portal/account_url.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[quotas]: ./media/batch-account-create-portal/quotas.png
[subscription_access]: ./media/batch-account-create-portal/subscription_iam.png
[add_permission]: ./media/batch-account-create-portal/add_permission.png
[account_portal_byos]: ./media/batch-account-create-portal/batch_acct_portal_byos.png

