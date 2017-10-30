---
title: Come creare, gestire o eliminare un account di archiviazione nel portale di Azure | Documentazione Microsoft
description: Creare un nuovo account di archiviazione, gestire le chiavi di accesso dell'account o eliminare un account di archiviazione nel portale di Azure. Informazioni sugli account di archiviazione Standard e Premium.
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 87c37da0-6cc6-4d88-a330-ef2896a1531d
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
f1_keywords: sql13.swb.windowsazurestorage.connect.f1
ms.date: 10/11/2017
ms.author: tamram
ms.openlocfilehash: db878f257f64ccc573a220c675cd373539dd5b17
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2017
---
# <a name="about-azure-storage-accounts"></a>Informazioni sugli account di archiviazione di Azure

[!INCLUDE [storage-selector-portal-create-storage-account](../../../includes/storage-selector-portal-create-storage-account.md)]

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

## <a name="overview"></a>Panoramica
Un account di archiviazione di Azure offre uno spazio dei nomi univoco per archiviare gli oggetti dati di Archiviazione di Azure e accedere a tali oggetti. Tutti gli oggetti in un account di archiviazione vengono fatturati insieme come gruppo. Per impostazione predefinita, i dati nel proprio account sono accessibili solo all'utente, ovvero al proprietario dell'account.

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

## <a name="storage-account-billing"></a>Fatturazione dell'account di archiviazione

[!INCLUDE [storage-account-billing-include](../../../includes/storage-account-billing-include.md)]

> [!NOTE]
> Quando si crea una macchina virtuale di Azure, un account di archiviazione viene creato automaticamente nel percorso di distribuzione se non si ha già un account di archiviazione in tale percorso. Non è quindi necessario attenersi alla procedura riportata sotto per creare un account di archiviazione per i dischi della macchina virtuale. Il nome dell'account di archiviazione sarà basato sul nome della macchina virtuale. Per altri dettagli, vedere la [documentazione relativa alle macchine virtuali di Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) .
> 
> 

## <a name="storage-account-endpoints"></a>Endpoint dell'account di archiviazione
Ogni oggetto archiviato in Archiviazione di Azure ha un indirizzo URL univoco. Il nome dell'account di archiviazione costituisce il sottodominio dell'indirizzo. La combinazione di sottodominio e nome di dominio, specifico di ogni servizio, costituisce un *endpoint* per l'account di archiviazione.

Ad esempio, se l'account di archiviazione si chiama *mystorageaccount*, gli endpoint predefiniti per l'account di archiviazione sono:

* Servizio BLOB: http://*mystorageaccount*.blob.core.windows.net
* Servizio tabelle: http://*mystorageaccount*.table.core.windows.net
* Servizio coda: http://*mystorageaccount*.queue.core.windows.net
* Servizio file: http://*mystorageaccount*.file.core.windows.net

> [!NOTE]
> Un account di archiviazione BLOB espone solo l'endpoint di servizio BLOB.
> 
> 

L'URL per accedere a un oggetto in un account di archiviazione viene formato aggiungendo la posizione dell'oggetto nell'account di archiviazione all'endpoint. Ad esempio, il formato di un indirizzo è simile al seguente: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

È anche possibile configurare un nome di dominio personalizzato da usare con l'account di archiviazione. Per altre informazioni, vedere l'articolo su come [configurare un nome di dominio personalizzato per l'endpoint di archiviazione BLOB](../blobs/storage-custom-domain-name.md). È anche possibile eseguire la configurazione con PowerShell. Per altre informazioni, vedere il cmdlet [Set-AzureRmStorageAccount](/powershell/module/azurerm.storage/set-azurermstorageaccount) .  


## <a name="create-a-storage-account"></a>Creare un account di archiviazione
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nel portale di Azure espandere il menu a sinistra per aprire il menu dei servizi e scegliere **Altri servizi**. Scorrere quindi verso il basso fino ad **Archiviazione** e scegliere **Account di archiviazione**. Nella finestra **Account di archiviazione** visualizzata scegliere **Aggiungi**.
3. Immettere un nome per l'account di archiviazione. Per informazioni dettagliate sul modo in cui questo nome verrà usato per indirizzare oggetti in Archiviazione di Azure, vedere [Endpoint dell'account di archiviazione](#storage-account-endpoints) .
   
   > [!NOTE]
   > I nomi degli account di archiviazione devono avere una lunghezza compresa tra 3 e 24 caratteri e possono contenere solo numeri e lettere minuscole.
   > 
   > Nome dell'account di archiviazione deve essere univoco all'interno di Azure. Il portale di Azure indica se il nome dell'account di archiviazione selezionato è già in uso.
   > 
   > 
4. Specificare il modello di distribuzione da usare: **Resource Manager** o **Classica**. **Gestione risorse** è il modello di distribuzione consigliato. Per altre informazioni, vedere [Comprendere la distribuzione di Gestione delle risorse e distribuzione classica](../../azure-resource-manager/resource-manager-deployment-model.md).
   
   > [!NOTE]
   > Gli account di archiviazione BLOB possono essere creati solo usando il modello di distribuzione di Resource Manager.

5. Selezionare il tipo di account di archiviazione: **Utilizzo generico** o **Archivio BLOB**. **Utilizzo generico** .
   
    Se è stata selezionata l'opzione **Utilizzo generico**, specificare il livello di prestazioni: **Standard** o **Premium**. Il livello predefinito è **Standard**. Per informazioni dettagliate sugli account di archiviazione Standard e Premium, vedere [Introduzione ad Archiviazione di Microsoft Azure](storage-introduction.md) e [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](storage-premium-storage.md).
   
    Se è stata selezionata l'opzione **Archivio BLOB**, specificare il livello di accesso come **frequente** o **sporadico**. Il livello predefinito è **Frequente**. Per informazioni dettagliate, vedere l'articolo [Archivio BLOB di Azure: livelli di archiviazione ad accesso frequente e sporadico](../blobs/storage-blob-storage-tiers.md) .
6. Selezionare l'opzione di replica per l'account di archiviazione: **Archiviazione con ridondanza locale**, **Archiviazione con ridondanza geografica**, **Archiviazione con ridondanza geografica e accesso in lettura** o **Archiviazione con ridondanza della zona**. L'opzione predefinita è **Archiviazione con ridondanza geografica e accesso in lettura**. Per altre informazioni sulle opzioni di replica di Archiviazione di Azure, vedere [Replica di Archiviazione di Azure](storage-redundancy.md).
7. Selezionare la sottoscrizione in cui creare il nuovo account di archiviazione.
8. Specificare un nuovo gruppo di risorse o selezionarne uno esistente. Per altre informazioni sui gruppi di risorse, vedere [Panoramica di Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).
9. Selezionare la posizione geografica dell'account di archiviazione. Per altre informazioni sui servizi disponibili in aree specifiche, vedere [Aree di Azure](https://azure.microsoft.com/regions/#services) .
10. Fare clic su **Crea** per creare l'account di archiviazione.

## <a name="manage-your-storage-account"></a>Gestire l'account di archiviazione
### <a name="change-your-account-configuration"></a>Modificare la configurazione dell'account
Dopo aver creato l'account di archiviazione, è possibile modificarne la configurazione, ad esempio modificando l'opzione di replica usata per l'account o il livello di accesso per un account di archiviazione BLOB. Nel [portale di Azure](https://portal.azure.com) passare all'account di archiviazione e in **IMPOSTAZIONI** fare clic su **Configurazione** per visualizzare e/o modificare la configurazione dell'account.

> [!NOTE]
> A seconda del livello di prestazioni che scelto durante la creazione dell'account di archiviazione, alcune opzioni di replica potrebbero non essere disponibili.
> 
> 

La modifica dell'opzione di replica modifica i prezzi. Per informazioni più dettagliate, vedere la pagina [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/) .

Per gli account di archiviazione BLOB, la modifica del livello di accesso potrebbe comportare costi per la modifica stessa oltre alla variazione dei prezzi. Per informazioni dettagliate, vedere [Archivio BLOB di Azure: livelli di archiviazione ad accesso frequente e sporadico - Prezzi e fatturazione](../blobs/storage-blob-storage-tiers.md#pricing-and-billing) .

### <a name="manage-your-storage-access-keys"></a>Gestire le chiavi di accesso alle risorse di archiviazione
Quando si crea un account di archiviazione, Azure genera due chiavi di accesso alle risorse di archiviazione da 512 bit, che vengono utilizzate per l'autenticazione quando si accede all'account di archiviazione. Fornendo due chiavi di accesso alle risorse di archiviazione, Azure consente di rigenerare le chiavi senza interruzioni per il servizio di archiviazione o l'accesso a tale servizio.

> [!NOTE]
> È consigliabile non condividere le chiavi di accesso alle risorse di archiviazione con altri utenti. Per permettere l'accesso alle risorse di archiviazione senza fornire le chiavi di accesso, è possibile usare una *firma di accesso condiviso*. Una firma di accesso condiviso fornisce l'accesso a una risorsa nell'account per un intervallo di tempo definito e con le autorizzazioni specificate. Vedere [Using Shared Access Signatures (SAS)](storage-dotnet-shared-access-signature-part-1.md) (Uso di firme di accesso condiviso) per altre informazioni.
> 
> 
<a id="view-and-copy-storage-access-keys"/></a>
#### <a name="view-and-copy-storage-access-keys"></a>Visualizzare e copiare le chiavi di accesso alle risorse di archiviazione
Nel [portale di Azure](https://portal.azure.com) passare all'account di archiviazione, fare clic su **Tutte le impostazioni** e quindi su **Chiavi di accesso** per visualizzare, copiare e rigenerare le chiavi di accesso dell'account. Il pannello **Chiavi di accesso** include anche le stringhe di connessione preconfigurate che usano le chiavi primarie e secondarie, che è possibile copiare per usarle nelle applicazioni.

#### <a name="regenerate-storage-access-keys"></a>Rigenerazione delle chiavi di accesso alle risorse di archiviazione
Si consiglia di modificare periodicamente le chiavi di accesso all'account di archiviazione per mantenere le connessioni dell'archiviazione sicure. Vengono assegnate due chiavi di accesso in modo da mantenere le connessioni all'account di archiviazione mediante una chiave di accesso mentre si rigenera l'altra.

> [!WARNING]
> La rigenerazione delle chiavi di accesso può influire sui servizi in Azure e sulle applicazioni che dipendono dall'account di archiviazione. È necessario aggiornare tutti i client che usano la chiave di accesso per accedere all'account di archiviazione per l'uso della nuova chiave.
> 
> 

**Servizi multimediali** : se si dispone di servizi multimediali dipendenti dall'account di archiviazione, è necessario risincronizzare le chiavi di accesso con il proprio servizio multimediale dopo la rigenerazione delle chiavi.

**Applicazioni** : se sono presenti applicazioni Web o servizi cloud che usano l'account di archiviazione, le connessioni andranno perse se si rigenerano le chiavi, a meno che non venga eseguito il rollover delle chiavi.

**Strumenti di esplorazione di archiviazione** : se si usano [strumenti di esplorazione di archiviazione](storage-explorers.md), sarà probabilmente necessario aggiornare la chiave di archiviazione usata da tali applicazioni.

Di seguito è riportato il processo per la rotazione delle chiavi di accesso alle risorse di archiviazione:

1. Aggiornare le stringhe di connessione nel codice dell'applicazione in modo che facciano riferimento alla chiave di accesso secondaria dell'account di archiviazione.
2. Rigenerare la chiave di accesso primaria per l'account di archiviazione. Nel pannello **Chiavi di accesso** fare clic su **Rigenerare Key1** e quindi su **Sì** per confermare che si desidera generare una nuova chiave.
3. Aggiornare le stringhe di connessione nel codice in modo che facciano riferimento alla nuova chiave di accesso primaria.
4. Rigenerare la chiave di accesso secondaria nello stesso modo.

## <a name="delete-a-storage-account"></a>Eliminare un account di archiviazione
Per rimuovere un account di archiviazione che non si usa più, passare all'account di archiviazione nel [portale di Azure](https://portal.azure.com)e fare clic su **Elimina**. Se si elimina un account di archiviazione, viene eliminato l'intero account, inclusi tutti i dati in esso contenuti.

> [!WARNING]
> Non è possibile ripristinare un account di archiviazione eliminato, né recuperare gli elementi che conteneva prima dell'eliminazione. Assicurarsi di eseguire il backup di tutti gli elementi da salvare prima di eliminare l'account. Lo stesso vale per tutte le risorse nell'account: dopo aver eliminato un BLOB, una tabella, una coda o un file, non è più possibile recuperarlo.
> 

Se si prova a eliminare un account di archiviazione associato a una macchina virtuale di Azure, potrebbe essere visualizzato un errore che informa che l'account di archiviazione è ancora in uso. Per risolvere questo errore, vedere l'articolo su come [risolvere gli errori durante l'eliminazione degli account di archiviazione](../common/storage-resource-manager-cannot-delete-storage-account-container-vhd.md).

## <a name="next-steps"></a>Passaggi successivi
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) è un'app autonoma gratuita di Microsoft che consente di rappresentare facilmente dati di Archiviazione di Azure in Windows, macOS e Linux.
* [Livelli Frequente e Non frequente dell'archiviazione BLOB di Azure](../blobs/storage-blob-storage-tiers.md)
* [Replica di Archiviazione di Azure](storage-redundancy.md)
* [Configurare le stringhe di connessione di archiviazione di Azure](../storage-configure-connection-string.md)
* [Trasferire dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md)
* [Blog del team di Archiviazione di Azure](http://blogs.msdn.com/b/windowsazurestorage/).

