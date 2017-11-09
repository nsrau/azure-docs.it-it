---
title: Uso di Azure PowerShell con Archiviazione di Azure | Microsoft Docs
description: Informazioni su come usare i cmdlet di Azure PowerShell per Archiviazione di Azure.
services: storage
documentationcenter: na
author: robinsh
manager: timlt
ms.assetid: f4704f58-abc6-4f89-8b6d-1b1659746f5a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2017
ms.author: robinsh
ms.openlocfilehash: 1046e407bb4e9d07e91014384e9eba7b0c7020a8
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2017
---
# <a name="using-azure-powershell-with-azure-storage"></a>Uso di Azure PowerShell con Archiviazione di Azure

Azure PowerShell consente di creare e gestire risorse di Azure dalla riga di comando di PowerShell o negli script. Per Archiviazione di Azure, i cmdlet possono essere suddivisi in due categorie: piano di controllo e piano dati. I cmdlet del piano di controllo consentono di gestire l'account di archiviazione, ovvero creare account di archiviazione, impostare le proprietà, eliminare account di archiviazione, ruotare le chiavi di accesso e così via. I cmdlet del piano dati consentono invece di gestire i dati archiviati *nell'*account di archiviazione, ovvero caricare file BLOB, creare condivisioni file e aggiungere messaggi a una coda.

Questo articolo illustra le operazioni comunemente eseguite quando si usano i cmdlet del piano di gestione per gestire gli account di archiviazione. Si apprenderà come: 

> [!div class="checklist"]
> * Elencare gli account di archiviazione
> * Ottenere un riferimento a un account di archiviazione esistente
> * Creare un account di archiviazione 
> * Impostare le proprietà dell'account di archiviazione
> * Recuperare e rigenerare le chiavi dell'account
> * Proteggere l'accesso all'account di archiviazione 
> * Abilitare Analisi archiviazione

Questo articolo fornisce collegamenti a vari altri articoli su PowerShell per l'archiviazione, ad esempio su come abilitare e accedere ad Analisi archiviazione, come usare i cmdlet del piano dati e come accedere a cloud indipendenti di Azure, ad esempio il cloud per la Cina, il cloud per la Germania e il cloud per enti pubblici.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

Per questa esercitazione, è necessario il modulo Azure PowerShell 4.4 o versioni successive. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere come [installare il modulo Azure PowerShell](/powershell/azure/install-azurerm-ps). 

Per questa esercitazione è possibile digitare i comandi in una normale finestra di PowerShell oppure usare [Windows PowerShell Integrated Scripting Environment (ISE)](/powershell/scripting/getting-started/fundamental/windows-powershell-integrated-scripting-environment--ise-), digitare i comandi in un editor e quindi testare uno o più comandi contemporaneamente mentre si procede con l'esercitazione. È possibile evidenziare le righe che si vuole eseguire e fare clic su Esegui selezionati per eseguire solo i comandi corrispondenti.

Per altre informazioni sugli account di archiviazione, vedere [Introduzione ad Archiviazione](storage-introduction.md) e [Informazioni sugli account di archiviazione di Azure](storage-create-storage-account.md).

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere alla sottoscrizione di Azure con il comando `Login-AzureRmAccount` e seguire le istruzioni visualizzate.

```powershell
Login-AzureRmAccount
```

## <a name="list-the-storage-accounts-in-the-subscription"></a>Elencare gli account di archiviazione nella sottoscrizione

Eseguire il cdmlet [Get-AzureRMStorageAccount](/powershell/module/azurerm.resources/get-azurermstorageaccount) per recuperare l'elenco degli account di archiviazione disponibili nella sottoscrizione corrente. 

```powershell
Get-AzureRMStorageAccount | Select StorageAccountName, Location
```

## <a name="get-a-reference-to-a-storage-account"></a>Ottenere un riferimento a un account di archiviazione

A questo punto, è necessario un riferimento a un account di archiviazione. È possibile creare un nuovo account di archiviazione oppure ottenere un riferimento a un account di archiviazione esistente. La sezione seguente illustra entrambi i metodi. 

### <a name="use-an-existing-storage-account"></a>Usare un account di archiviazione esistente 

Per recuperare un account di archiviazione esistente, sono necessari il nome del gruppo di risorse e il nome dell'account di archiviazione. Impostare le variabili per questi due campi e quindi usare il cmdlet [Get AzureRmStorageAccount](/powershell/module/azurerm.storage/Get-AzureRmStorageAccount). 

```powershell
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"

$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName 
```

Si dispone adesso di $storageAccount, che fa riferimento a un account di archiviazione esistente.

### <a name="create-a-storage-account"></a>Creare un account di archiviazione 

Lo script seguente illustra come creare un account di archiviazione per utilizzo generico usando [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount). Dopo aver creato l'account è necessario recuperarne il contesto, che può essere usato nei comandi successivi anziché specificare l'autenticazione con ogni chiamata.

```powershell
# Get list of locations and select one.
Get-AzureRmLocation | select Location 
$location = "eastus"

# Create a new resource group.
$resourceGroup = "teststoragerg"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location 

# Set the name of the storage account and the SKU name. 
$storageAccountName = "testpshstorage"
$skuName = "Standard_LRS"
    
# Create the storage account.
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName $skuName

# Retrieve the context. 
$ctx = $storageAccount.Context
```

Lo script usa i cmdlet di PowerShell seguenti: 

*   [Get-AzureRmLocation](/powershell/module/azurerm.storage/Get-AzureRmLocation): recupera un elenco delle posizioni valide. Nell'esempio viene usata la posizione `eastus`.

*   [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/New-AzureRmResourceGroup): crea un nuovo gruppo di risorse. Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Il gruppo di risorse dell'esempio è chiamato `teststoragerg`. 

*   [New-AzureRmStorageAccount](/powershell/module/azurerm.resources/New-AzureRmStorageAcccount): crea l'account di archiviazione effettivo. Nell'esempio viene usato `testpshstorage`.

Il nome dello SKU indica il tipo di replica per l'account di archiviazione, ad esempio l'archiviazione con ridondanza locale. Per altre informazioni sulla replica, vedere [Replica di Archiviazione di Azure](storage-redundancy.md).

> [!IMPORTANT]
> Il nome per l'account di archiviazione è univoco in Azure e deve essere in minuscolo. Per informazioni sulle limitazioni e le convenzioni relative ai nomi, vedere [Assegnazione di nome e riferimento a contenitori, BLOB e metadati](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).
> 

Si dispone ora di un nuovo account di archiviazione e di un riferimento ad esso. 

## <a name="manage-the-storage-account"></a>Gestire l'account di archiviazione

È stato così ottenuto un riferimento a un nuovo account di archiviazione o a un account di archiviazione esistente. La sezione seguente illustra alcuni dei comandi che è possibile usare per gestire l'account di archiviazione personale.

### <a name="storage-account-properties"></a>Proprietà dell'account di archiviazione

Per modificare le impostazioni di un account di archiviazione, usare [Set-AzureRmStorageAccount](/powershell/module/azurerm.resources/Set-AzureRmStorageAccount). Sebbene non sia possibile modificare la posizione di un account di archiviazione o del gruppo di risorse in cui si trova, è possibile modificare molte altre proprietà. Di seguito sono elencate alcune delle proprietà che è possibile modificare tramite PowerShell.

* Il **dominio personalizzato** assegnato all'account di archiviazione.

* I **tag** assegnati all'account di archiviazione. I tag vengono usati spesso per categorizzare le risorse a scopi di fatturazione.

* Lo **SKU**, ovvero l'impostazione di replica per l'account di archiviazione, ad esempio LRS per l'archiviazione con ridondanza locale. È possibile, ad esempio, modificare Standard\_LRS in Standard\_GRS o Standard\_RAGRS. Tenere presente che non è possibile modificare Standard ZRS o Premium LRS in altri SKU o modificare altri SKU in questi. 

* Il **livello di accesso** per gli account di archiviazione BLOB. Il valore per il livello di accesso è impostato su **Frequente** o **Sporadico** e consente di ridurre al minimo i costi selezionando il livello di accesso più adatto al modo in cui viene usato l'account di archiviazione. Per altre informazioni, vedere [Livelli di archiviazione ad accesso frequente, ad accesso sporadico e archivio](../blobs/storage-blob-storage-tiers.md).

* L'impostazione di crittografia del servizio di archiviazione per l'archiviazione BLOB e/o l'archiviazione di file. Per altre informazioni sulla Crittografia del servizio di archiviazione, vedere [Crittografia del servizio di archiviazione](storage-service-encryption.md).

* Consentire solo traffico HTTPS. 

### <a name="manage-the-access-keys"></a>Gestire le chiavi di accesso

Un account di archiviazione di Azure viene fornito con due chiavi. Per recuperare le chiavi, usare [Get-AzureRmStorageAccountKey](/powershell/module/AzureRM.Storage/Get-AzureRmStorageAccountKey). In questo esempio viene recuperata la prima chiave. Per recuperare l'altra, usare `Value[1]` anziché `Value[0]`.

```powershell
$storageAccountKey = `
    (Get-AzureRmStorageAccountKey `
    -ResourceGroupName $resourceGroup `
    -Name $storageAccountName).Value[0]
```

Per rigenerare la chiave, usare [New-AzureRmStorageAccountKey](/powershell/module/AzureRM.Storage/New-AzureRmStorageAccountKey). 

```powershell
New-AzureRmStorageAccountKey -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -KeyName key1 
```

Per rigenerare l'altra chiave, usare `key2` come nome di chiave anziché `key1`.

Rigenerare una delle chiavi e recuperarla nuovamente per visualizzare il nuovo valore.

> [!NOTE] 
> È consigliabile eseguire un'attenta pianificazione prima di rigenerare la chiave per un account di archiviazione di produzione. La rigenerazione di una o di entrambe le chiavi rende non valido l'accesso per qualsiasi applicazione che usa la chiave rigenerata. Per altre informazioni, vedere [Rigenerazione delle chiavi di accesso alle risorse di archiviazione](storage-create-storage-account.md#regenerate-storage-access-keys).


### <a name="delete-a-storage-account"></a>Eliminare un account di archiviazione 

Per eliminare un account di archiviazione, usare [Remove-AzureRmStorageAccount](/powershell/module/azurerm.storage/Remove-AzureRmStorageAccount).

```powershell
Remove-AzureRmStorageAccount -ResourceGroup $resourceGroup -AccountName $storageAccountName
```

> [!IMPORTANT]
> Quando si elimina un account di archiviazione, vengono eliminati anche tutti gli asset archiviati nell'account. Se si elimina un account involontariamente, contattare immediatamente il supporto e aprire un ticket per il ripristino dell'account di archiviazione. Il ripristino dei dati non è garantito, ma talvolta funziona. Non creare un nuovo account di archiviazione con lo stesso nome di quello precedente finché il ticket di supporto non è stato risolto. 
>

### <a name="protect-your-storage-account-using-vnets-and-firewalls"></a>Proteggere l'account di archiviazione tramite reti virtuali e firewall

Per impostazione predefinita, tutti gli account di archiviazione sono accessibili da qualsiasi rete con accesso a Internet. È possibile tuttavia configurare le regole di rete in modo da consentire che solo le applicazioni di determinate reti virtuali possano accedere a un account di archiviazione. Per altre informazioni, vedere [Configurare i firewall e le reti virtuali di Archiviazione di Azure](storage-network-security.md). 

Questo articolo illustra come gestire queste impostazioni usando i cmdlet di PowerShell seguenti:
* [Add-AzureRmStorageAccountNetworkRule](/powershell/module/AzureRM.Storage/Add-AzureRmStorageAccountNetworkRule)
* [Update-AzureRmStorageAccountNetworkRuleSet](/powershell/module/azurerm.storage/update-azurermstorageaccountnetworkruleset)
* [Remove-AzureRmStorageAccountNetworkRule](/powershell/module/azurerm.storage/remove-azurermstorage-account-networkrule)

## <a name="use-storage-analytics"></a>Usare Analisi archiviazione  

[Analisi archiviazione di Azure](storage-analytics.md) è composto da [Storage Analytics Metrics](/rest/api/storageservices/about-storage-analytics-metrics) (Metriche di Analisi archiviazione) e [Storage Analytics Logging](/rest/api/storageservices/about-storage-analytics-logging) (Registrazione di Analisi archiviazione). 

**Storage Analytics Metrics** (Metriche di Analisi archiviazione) consente di raccogliere le metriche relative agli account di archiviazione di Azure che è possibile usare per monitorare l'integrità di un account di archiviazione. Le metriche possono essere abilitate per BLOB, file, tabelle e code.

**Storage Analytics Logging** (Registrazione di Analisi archiviazione) viene eseguita sul lato server e consente di registrare i dettagli di tutte richieste inviate all'account di archiviazione, indipendentemente dall'esito positivo o negativo. Questi log consentono di visualizzare i dettagli delle operazioni di lettura, scrittura ed eliminazione a fronte delle proprie tabelle, code e BLOB, nonché i motivi per cui le richieste non sono riuscite. La registrazione non è disponibile per File di Azure.

È possibile configurare il monitoraggio tramite il [portale di Azure](https://portal.azure.com) o PowerShell oppure nel codice tramite la libreria del client di archiviazione. 

> [!NOTE]
> Con PowerShell è possibile anche abilitare l'analisi al minuto, mentre questa funzionalità non è consentita dal portale di Azure.
>

* Per informazioni su come abilitare e visualizzare i dati di Metriche di archiviazione con PowerShell, vedere [Abilitazione di Metriche di archiviazione di Azure e visualizzazione dei dati delle metriche](storage-enable-and-view-metrics.md#how-to-enable-metrics-using-powershell).

* Per informazioni su come abilitare e recuperare i dati di registrazione di archiviazione con PowerShell, vedere [How to enable Storage Logging using PowerShell](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data#how-to-enable-storage-logging-using-powershell) (Come abilitare la registrazione di archiviazione con PowerShell) e [Finding your Storage Logging log data](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data#finding-your-storage-logging-log-data) (Trovare i dati del log di registrazione di archiviazione).

* Per informazioni dettagliate sull'uso di Metriche di archiviazione e Registrazione archiviazione per la risoluzione dei problemi di archiviazione, vedere [Monitoraggio, diagnosi e risoluzione dei problemi del servizio di archiviazione di Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="manage-the-data-in-the-storage-account"></a>Gestire i dati nell'account di archiviazione

Fino a qui si è appreso come gestire l'account di archiviazione con PowerShell. Gli articoli seguenti illustrano come accedere agli oggetti dati nell'account di archiviazione.

* [Eseguire operazioni in Archiviazione Blob di Azure con Azure PowerShell](../blobs/storage-how-to-use-blobs-powershell.md)
* [Come usare PowerShell per gestire File di Azure](../files/storage-how-to-use-files-powershell.md)
* [Eseguire operazioni nell'archivio code di Azure con Azure PowerShell](../queues/storage-powershell-how-to-use-queues.md)

## <a name="azures-independently-deployed-clouds"></a>Cloud distribuiti in modo indipendente di Azure

La maggior parte delle persone usa il cloud pubblico di Azure per la distribuzione globale di Azure. Per motivi di sovranità e altro, sono disponibili anche alcune distribuzioni indipendenti di Microsoft Azure, denominate "ambienti". Ecco gli ambienti disponibili:

* [Cloud di Azure per enti pubblici](https://azure.microsoft.com/features/gov/)
* [Cloud di Azure per la Cina gestito da 21Vianet in Cina](http://www.windowsazure.cn/)
* [Cloud di Azure per la Germania](../../germany/germany-welcome.md)

Per informazioni su come accedere a questi cloud e al relativo spazio di archiviazione con PowerShell, vedere [Managing Storage in the Azure independent clouds using PowerShell (Gestione dell'archiviazione nei cloud indipendenti di Azure con PowerShell)](storage-powershell-independent-clouds.md).

## <a name="clean-up-resources"></a>Pulire le risorse

Se per questa esercitazione sono stati creati un nuovo gruppo di risorse e un account di archiviazione, è possibile rimuovere tutti gli asset creati rimuovendo il gruppo di risorse. Così facendo vengono eliminate anche tutte le risorse in esso contenute. In questo caso, rimuove l'account di archiviazione creato e il gruppo di risorse stesso.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```
## <a name="next-steps"></a>Passaggi successivi

Questo articolo illustra le operazioni comunemente eseguite quando si usano i cmdlet del piano di gestione per gestire gli account di archiviazione. Si è appreso come: 

> [!div class="checklist"]
> * Elencare gli account di archiviazione
> * Ottenere un riferimento a un account di archiviazione esistente
> * Creare un account di archiviazione 
> * Impostare le proprietà dell'account di archiviazione
> * Recuperare e rigenerare le chiavi dell'account
> * Proteggere l'accesso all'account di archiviazione 
> * Abilitare Analisi archiviazione

Questo articolo offre anche riferimenti a vari altri articoli, ad esempio su come gestire gli oggetti dati, come abilitare Analisi archiviazione e come accedere a cloud indipendenti di Azure, ad esempio il cloud per la Cina, il cloud per la Germania e il cloud per enti pubblici. Di seguito sono riportati alcuni altri articoli correlati e alcune risorse di riferimento:

* [Cmdlet di PowerShell per il piano di controllo di Archiviazione di Azure](/powershell/module/AzureRM.Storage/)
* [Cmdlet di PowerShell per il piano dati di Archiviazione di Azure](/powershell/module/azure.storage/)
* [Riferimenti Windows PowerShell](https://msdn.microsoft.com/library/ms714469.aspx)