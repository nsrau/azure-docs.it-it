---
title: Creare un account Batch nel portale di Azure | Microsoft Docs
description: Informazioni su come creare un account Azure Batch nel portale di Azure per eseguire carichi di lavoro paralleli su larga scala nel cloud
services: batch
documentationcenter: 
author: dlepow
manager: jeconnoc
editor: 
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/14/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2e1b5ae05429cba34e3ea71f95414417bffd99a6
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Creare un account Batch nel portale di Azure

> [!div class="op_single_selector"]
> * [Azure portal](batch-account-create-portal.md)
> * [.NET per la gestione di Batch](batch-management-dotnet.md)
>
>

Informazioni su come creare un account Batch di Azure nel [portale di Azure][azure_portal], scegliere le proprietà dell'account adatte allo scenario di calcolo. Informazioni su dove trovare le proprietà di account importanti come le chiavi di accesso e gli URL dell'account.

Per informazioni sugli account e gli scenari Batch, vedere la [panoramica della funzionalità](batch-api-basics.md).



## <a name="create-a-batch-account"></a>Creare un account Batch

> [!NOTE]
> Quando si crea un account Batch, è in genere consigliabile scegliere la modalità **Servizio Batch** predefinita, in cui i pool vengono associati in background nelle sottoscrizioni gestite da Azure. Nella modalità **Sottoscrizione utente** alternativa, che non è più consigliata per la maggior parte degli scenari, le VM e le altre risorse di Batch vengono create direttamente nella sottoscrizione durante la creazione di un pool. Per creare un account Batch in modalità Sottoscrizione utente, è anche necessario registrare la sottoscrizione in Azure Batch e associare l'account ad Azure Key Vault.

1. Accedere al [portale di Azure][azure_portal].
2. Fare clic su **Nuovo** e cercare nel Marketplace **Servizio Batch**.

    ![Batch in Marketplace][marketplace_portal]
3. Selezionare **Servizio Batch**, fare clic su **Crea** e quindi immettere le impostazioni in **Nuovo account Batch**. Vedere i dettagli seguenti.

    ![Creare un account Batch][account_portal]

    a. **Nome account**: il nome scelto deve essere univoco all'interno dell'area di Azure in cui viene creato l'account. Vedere **Località** di seguito. Il nome dell'account può contenere solo caratteri minuscoli o numeri e deve avere una lunghezza di 3-24 caratteri.

    b. **Sottoscrizione**: sottoscrizione in cui creare l'account Batch. Se è presente solo una sottoscrizione, è selezionata per impostazione predefinita.

    c. **Modalità di allocazione del pool**: se questa impostazione è visualizzata, accettare il valore predefinito **Servizio Batch**.

    c. **Gruppo di risorse**: selezionare un gruppo di risorse esistente per il nuovo account Batch. È possibile crearne facoltativamente uno nuovo.

    d. **Località**: area di Azure in cui creare l'account Batch. Solo le aree supportate dalla sottoscrizione e dal gruppo di risorse vengono visualizzate come opzioni.

    e. **Account di archiviazione** (facoltativo): account di Archiviazione di Azure per uso generico associato all'account Batch. Tale operazione è consigliata per la maggior parte degli account Batch. Per altri dettagli, vedere [Account di archiviazione di Azure collegato](#linked-azure-storage-account) più avanti in questo articolo.

4. Fare clic su **Crea** per creare l'account.



## <a name="view-batch-account-properties"></a>Visualizzare le proprietà dell'account Batch
Dopo avere creato l'account, fare clic sull'account per accedere alle impostazioni e proprietà associate. È possibile accedere a tutte le impostazioni e proprietà dell'account usando il menu a sinistra.

![Pagina Account Batch nel portale di Azure][account_blade]

* **URL dell'account Batch**: quando si sviluppa un'applicazione con le [API Batch](batch-apis-tools.md#azure-accounts-for-batch-development), è necessario un URL dell'account per accedere alle risorse di Batch. Un URL dell'account Batch ha il seguente formato:

    `https://<account_name>.<region>.batch.azure.com`

![URL dell'account Batch nel portale][account_url]

* **Chiavi di accesso**: per autenticare l'accesso all'account Batch dall'applicazione, è possibile usare una chiave di accesso dell'account. Batch supporta anche l'autenticazione di Azure Active Directory.

    Per visualizzare o rigenerare le chiavi di accesso, selezionare **Chiavi**.

    ![Chiavi dell'account Batch nel portale di Azure][account_keys]

[!INCLUDE [batch-pricing-include](../../includes/batch-pricing-include.md)]

## <a name="linked-azure-storage-account"></a>Account di archiviazione di Azure collegato

È possibile collegare un account di archiviazione di Azure per uso generico all'account Batch e questa operazione può essere utile per molti scenari. La funzionalità [Pacchetti dell'applicazione](batch-application-packages.md) di Batch usa l'archivio BLOB di Azure, proprio come la libreria [Batch File Conventions .NET](batch-task-output.md). Queste funzionalità facoltative facilitano la distribuzione delle applicazioni eseguite dalle attività di Batch e il salvataggio permanente dei dati prodotti.

È consigliabile creare un nuovo account di archiviazione da usare esclusivamente con l'account Batch. Azure Batch supporta attualmente solo il tipo di account di archiviazione per utilizzo generico. Questo tipo di account è descritto nel passaggio 5 [Creare un account di archiviazione](../storage/common/storage-create-storage-account.md#create-a-storage-account) in [Informazioni sugli account di archiviazione di Azure](../storage/common/storage-create-storage-account.md).

![Creazione di un account di archiviazione per utilizzo generico][storage_account]

> [!NOTE]
> Prestare attenzione quando si rigenerano le chiavi di accesso di un account di archiviazione collegato. Rigenerare solo una chiave dell'account di archiviazione e fare clic su **Sincronizza chiavi** nella pagina dell'account di archiviazione collegato. Attendere cinque minuti per consentire la propagazione delle chiavi ai nodi di calcolo dei pool, quindi rigenerare e sincronizzare l'altra chiave, se necessario. Se si rigenerano entrambe le chiavi contemporaneamente, i nodi di calcolo non riusciranno a sincronizzare entrambe le chiavi e perderanno l'accesso all'account di archiviazione.
>
>

![Rigenerazione delle chiavi degli account di archiviazione][4]

## <a name="additional-configuration-for-user-subscription-mode"></a>Configurazione aggiuntiva per la modalità Sottoscrizione utente

Se si sceglie di creare un account Batch in modalità Sottoscrizione utente, prima di creare l'account eseguire questi passaggi aggiuntivi.

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Consentire ad Azure Batch di accedere alla sottoscrizione (operazione occasionale)
Quando si crea il primo account Batch in modalità Sottoscrizione utente, è necessario registrare la sottoscrizione in Batch. Se questa operazione è stata fatta precedentemente, passare alla sezione successiva.

1. Accedere al [portale di Azure][azure_portal].

2. Fare clic su **Altri servizi** > **Sottoscrizioni** e quindi fare clic sulla sottoscrizione che si desidera usare per l'account Batch.

3. Nella pagina **Sottoscrizione** fare clic su **Controllo di accesso (IAM)** > **Aggiungi**.

    ![Controllo di accesso alla sottoscrizione][subscription_access]

4. Nella pagina **Aggiungi autorizzazioni** selezionare il ruolo **Collaboratore** e cercare l'API Batch. Cercare ognuna di queste stringhe fino a trovare l'API:
    1. **MicrosoftAzureBatch**.
    2. **Microsoft Azure Batch**. I tenant di Azure AD più recenti potrebbero usare questo nome.
    3. **ddbf3205-c6bd-46ae-8127-60eb93363864** è l'ID dell'API Batch. 

5. Una volta trovata l'API Batch, selezionarla e fare clic su **Salva**.

    ![Aggiungere le autorizzazioni di Batch][add_permission]

### <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi
In modalità di sottoscrizione utente, è necessario un insieme di credenziali delle chiavi di Azure che appartiene allo stesso gruppo di risorse come l'account Batch da creare. Assicurarsi che il gruppo di risorse sia in un'area in cui Batch è [disponibile](https://azure.microsoft.com/regions/services/) e che supporta la sottoscrizione.

1. Nel [portale di Azure][azure_portal] fare clic su **Nuovo** > **Sicurezza e identità** > **Insieme di credenziali delle chiavi**.

2. Nella pagina **Crea insieme di credenziali delle chiavi** immettere un nome per l'insieme di credenziali delle chiavi e creare un gruppo di risorse nell'area desiderata per l'account Batch. Lasciare i valori predefiniti per le impostazioni rimanenti, quindi fare clic su **Crea**.




## <a name="batch-service-quotas-and-limits"></a>Quote e limiti del servizio Batch
Come per la sottoscrizione di Azure e altri servizi di Azure, agli account Batch vengono applicati determinati [limiti e quote](batch-quota-limit.md). Le quote correnti per un account Batch sono visualizzate in **Quote**.

![Quote dell'account Batch nel portale di Azure][quotas]



Per aumentare molte di queste quote, è possibile inviare una richiesta di supporto gratuito per il prodotto nel portale di Azure. Per informazioni dettagliate sulla richiesta di aumenti delle quote, vedere [Quote e limiti per il servizio Azure Batch](batch-quota-limit.md) .

## <a name="other-batch-account-management-options"></a>Altre opzioni di gestione dell'account Batch
Oltre a usare il portale di Azure, è possibile creare e gestire account Batch anche con gli strumenti seguenti:

* [Cmdlet di PowerShell per Batch](batch-powershell-cmdlets-get-started.md)
* [Interfaccia della riga di comando di Azure](batch-cli-get-started.md)
* [.NET per la gestione di Batch](batch-management-dotnet.md)

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sui concetti e sulle funzionalità del servizio Batch, vedere [Panoramica delle funzionalità di Batch](batch-api-basics.md). L'articolo descrive le risorse primarie di Batch, tra cui pool, nodi di calcolo, processi e attività, e fornisce una panoramica delle funzionalità del servizio per carichi di lavoro di calcolo su larga scala.
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

