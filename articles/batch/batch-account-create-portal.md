---
title: Creare un account Batch nel portale di Azure | Documentazione Microsoft
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
ms.date: 02/27/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 09be891b5385871554f45bc1f824b4351ffd3bc2
ms.lasthandoff: 03/21/2017


---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Creare un account Batch nel portale di Azure

> [!div class="op_single_selector"]
> * [Portale di Azure](batch-account-create-portal.md)
> * [.NET per la gestione di Batch](batch-management-dotnet.md)
> 
> 

Informazioni su come creare un account Azure Batch nel [Portale di Azure][azure_portal] e su dove trovare importanti proprietà dell'account, come le chiavi di accesso e gli URL degli account. Vengono anche indicati i prezzi di Batch e viene illustrato il collegamento di un account di archiviazione di Azure all'account Batch per poter usare i [pacchetti dell'applicazione](batch-application-packages.md) e [salvare in modo permanente l'output dei processi e delle attività](batch-task-output.md).

## <a name="create-a-batch-account"></a>Creare un account Batch
1. Accedere al [portale di Azure][azure_portal].
2. Fare clic su **Nuovo** > **Calcolo** > **Servizio Batch**.
   
    ![Batch in Marketplace][marketplace_portal]
3. Verrà visualizzato il pannello **Nuovo account Batch** . Per una descrizione degli elementi del pannello, vedere i punti dalla *a* alla *e* qui di seguito.
   
    ![Creare un account Batch][account_portal]
   
    a. **Nome account**: nome dell'account Batch. Il nome scelto deve essere univoco nell'area di Azure in cui il nuovo account verrà creato. Vedere sotto **Località**. Il nome dell'account può contenere solo caratteri minuscoli o numeri e deve avere una lunghezza di 3-24 caratteri.
   
    b. **Sottoscrizione**: sottoscrizione in cui creare l'account Batch. Se è presente solo una sottoscrizione, è selezionata per impostazione predefinita.
   
    c. **Gruppo di risorse**: selezionare un gruppo di risorse esistente per il nuovo account Batch. È possibile crearne facoltativamente uno nuovo.
   
    d. **Località**: area di Azure in cui creare l'account Batch. Solo le aree supportate dalla sottoscrizione e dal gruppo di risorse vengono visualizzate come opzioni.
   
    e. **Account di archiviazione** (facoltativo): account di archiviazione di Azure per utilizzo generico associato al nuovo account Batch. Per altri dettagli, vedere più avanti [Account di archiviazione di Azure collegato](#linked-azure-storage-account) .

4. Fare clic su **Crea** per creare l'account.
   
   Il portale indica che è in corso la **distribuzione** dell'account e al termine viene visualizzata la notifica **Le distribuzioni sono riuscite** in *Notifiche*.

## <a name="view-batch-account-properties"></a>Visualizzare le proprietà dell'account Batch
Dopo avere creato l'account, è possibile aprire il pannello **Account Batch** per accedere alle impostazioni e alle proprietà. È possibile accedere a tutte le impostazioni e proprietà dell'account usando il menu di sinistra del pannello Account Batch.

![Pannello Account Batch nel portale di Azure][account_blade]

* **URL dell'account Batch**: quando si sviluppa un'applicazione con le [API Batch](batch-apis-tools.md#batch-development-apis), sarà necessario un URL dell'account per accedere alle risorse di Batch. Un URL dell'account Batch ha il seguente formato:
  
    `https://<account_name>.<region>.batch.azure.com`

![URL dell'account Batch nel portale][account_url]

* **Chiavi di accesso**: per autenticare l'accesso all'account Batch dall'applicazione, sarà necessaria una chiave di accesso dell'account. Per visualizzare o rigenerare le chiavi di accesso dell'account Batch, immettere `keys` nella casella di **ricerca** del menu a sinistra nel pannello Account Batch, quindi selezionare **Chiavi**.
  
    ![Chiavi dell'account Batch nel portale di Azure][account_keys]

[!INCLUDE [batch-pricing-include](../../includes/batch-pricing-include.md)]

## <a name="linked-azure-storage-account"></a>Account di archiviazione di Azure collegato

Come accennato prima, è possibile facoltativamente collegare un account di archiviazione di Azure per utilizzo generico all'account Batch. La funzionalità [Pacchetti dell'applicazione](batch-application-packages.md) di Batch usa l'archivio BLOB di Azure, proprio come la libreria [Batch File Conventions .NET](batch-task-output.md). Queste funzionalità facoltative facilitano la distribuzione delle applicazioni eseguite dalle attività di Batch e il salvataggio permanente dei dati prodotti.

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

Tenere presenti queste quote quando si progettano i carichi di lavoro di Batch e se ne aumentano le prestazioni. Se, ad esempio, il pool non raggiunge il numero di destinazione di nodi di calcolo specificato, potrebbe essere stato raggiunto il limite di quota di core per l'account Batch.

La quota per gli account Batch è per area per sottoscrizione, quindi, per impostazione predefinita, è possibile avere più di un account Batch, purché in aree diverse. È possibile eseguire più carichi di lavoro Batch in un solo account Batch o distribuire i carichi di lavoro tra gli account Batch nella stessa sottoscrizione, ma in aree di Azure diverse.

Inoltre, per aumentare molte di queste quote, è sufficiente inviare una richiesta di supporto gratuito per il prodotto nel portale di Azure. Per informazioni dettagliate sulla richiesta di aumenti delle quote, vedere [Quote e limiti per il servizio Azure Batch](batch-quota-limit.md) .

## <a name="other-batch-account-management-options"></a>Altre opzioni di gestione dell'account Batch
Oltre a usare il portale di Azure, è anche possibile creare e gestire account Batch con gli strumenti seguenti:

* [Cmdlet di PowerShell per Batch](batch-powershell-cmdlets-get-started.md)
* [Interfaccia della riga di comando di Azure](../cli-install-nodejs.md)
* [.NET per la gestione di Batch](batch-management-dotnet.md)

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sui concetti e sulle funzionalità del servizio Batch, vedere [Panoramica delle funzionalità di Batch per sviluppatori](batch-api-basics.md) . L'articolo descrive le risorse primarie di Batch, ad esempio i pool, i nodi di calcolo, i processi e le attività e fornisce una panoramica delle funzionalità del servizio che consentono l'esecuzione di carichi di lavoro di calcolo su larga scala.
* Apprendere le nozioni di base dello sviluppo di un'applicazione abilitata per Batch con la [libreria client Batch .NET](batch-dotnet-get-started.md). L' [articolo introduttivo](batch-dotnet-get-started.md) illustra un'applicazione funzionante che usa il servizio Batch per eseguire un carico di lavoro in più nodi di calcolo e include l'uso di Archiviazione di Azure per lo staging e il recupero dei file del carico di lavoro.

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

