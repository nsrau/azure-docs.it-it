---
title: Introduzione a PowerShell per Azure Batch | Microsoft Docs
description: Breve introduzione ai cmdlet di Azure PowerShell da usare per gestire le risorse Batch.
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: f9ad62c5-27bf-4e6b-a5bf-c5f5914e6199
ms.service: batch
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: powershell
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e33be6ed658e00250ea1e80cd7da4d348fb18296
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-batch-resources-with-powershell-cmdlets"></a>Gestire le risorse Batch con i cmdlet di PowerShell

Con i cmdlet di PowerShell per Azure Batch è possibile eseguire molte delle attività eseguite con le API Batch, il portale di Azure e l'interfaccia della riga di comando di Azure e creare i relativi script. Questa è una rapida introduzione ai cmdlet con cui è possibile gestire gli account Batch e usare risorse di Batch come pool, processi e attività.

Per un elenco completo di cmdlet Batch e per la sintassi dettagliata dei cmdlet, vedere [Informazioni di riferimento sui cmdlet di Azure Batch](/powershell/module/azurerm.batch/#batch).

Questo articolo si basa sui cmdlet di Azure PowerShell versione 3.0.0. È consigliabile eseguire di frequente l'aggiornamento di Azure PowerShell per sfruttare i vantaggi degli aggiornamenti e miglioramenti del servizio.

## <a name="prerequisites"></a>Prerequisiti
Eseguire queste operazioni per usare Azure PowerShell per gestire le risorse di Batch.

* [Come installare e configurare Azure PowerShell](/powershell/azure/overview)
* Eseguire il cmdlet **Login-AzureRmAccount** per connettersi alla sottoscrizione. I cmdlet di Azure Batch sono disponibili nel modulo Azure Resource Manager:
  
    `Login-AzureRmAccount`
* **Effettuare la registrazione con lo spazio dei nomi del provider di Batch**. Questa operazione deve essere eseguita solo **una volta per ogni sottoscrizione**.
  
    `Register-AzureRMResourceProvider -ProviderNamespace Microsoft.Batch`

## <a name="manage-batch-accounts-and-keys"></a>Gestire gli account e le chiavi Batch
### <a name="create-a-batch-account"></a>Creare un account Batch
**New-AzureRmBatchAccount** crea un account Batch in un gruppo di risorse specificato. Se non si ha già un gruppo di risorse, crearne uno eseguendo il cmdlet [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Specificare una delle aree di Azure nel parametro **location**, ad esempio "Stati Uniti centrali". Ad esempio:

    New-AzureRmResourceGroup –Name MyBatchResourceGroup –location "Central US"

Creare quindi un account Batch nel gruppo di risorse, specificando il nome dell'account in <*account_name*> e la località e il nome del gruppo di risorse. La creazione dell'account Batch può richiedere tempo. ad esempio:

    New-AzureRmBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName <res_group_name>

> [!NOTE]
> Il nome dell'account Batch deve essere univoco nell'area di Azure per il gruppo di risorse e contenere tra 3 e 24 caratteri (sono ammessi solo numeri e lettere minuscole).
> 
> 

### <a name="get-account-access-keys"></a>Ottenere le chiavi di accesso all'account
**Get-AzureRmBatchAccountKeys** mostra le chiavi di accesso associate a un account Azure Batch. Ad esempio, eseguire le operazioni seguenti per ottenere le chiavi primarie e secondarie dell'account creato.

    $Account = Get-AzureRmBatchAccountKeys –AccountName <account_name>

    $Account.PrimaryAccountKey

    $Account.SecondaryAccountKey

### <a name="generate-a-new-access-key"></a>Generare una nuova chiave di accesso
**New-AzureRmBatchAccountKey** genera una nuova chiave dell'account primaria o secondaria per un account Azure Batch. Ad esempio, per generare una nuova chiave primaria per l'account Batch, digitare:

    New-AzureRmBatchAccountKey -AccountName <account_name> -KeyType Primary

> [!NOTE]
> Per generare una nuova chiave secondaria, specificare "Secondary" per il parametro **KeyType** . È necessario rigenerare la chiave primaria e quella secondaria separatamente.
> 
> 

### <a name="delete-a-batch-account"></a>Eliminare un account Batch
**Remove-AzureRmBatchAccount** elimina un account Batch. ad esempio:

    Remove-AzureRmBatchAccount -AccountName <account_name>

Quando richiesto, confermare che si desidera rimuovere l'account. La rimozione di un account può richiedere alcuni minuti.

## <a name="create-a-batchaccountcontext-object"></a>Creare un oggetto BatchAccountContext
Per l'autenticazione con i cmdlet di PowerShell per Batch quando si creano e si gestiscono pool, processi, attività e altre risorse di Batch, creare prima di tutto un oggetto BatchAccountContext in cui archiviare il nome e le chiavi dell'account:

    $context = Get-AzureRmBatchAccountKeys -AccountName <account_name>

Passare l'oggetto BatchAccountContext nei cmdlet che usano il parametro **BatchContext** .

> [!NOTE]
> Per impostazione predefinita, la chiave primaria dell'account viene usata per l'autenticazione, ma è possibile selezionare in modo esplicito la chiave da usare modificando la proprietà **KeyInUse** dell'oggetto BatchAccountContext: `$context.KeyInUse = "Secondary"`.
> 
> 

## <a name="create-and-modify-batch-resources"></a>Creare e modificare le risorse Batch
Usare cmdlet come **New-AzureBatchPool**, **New-AzureBatchJob** e **New-AzureBatchTask** per creare risorse in un account Batch. Sono disponibili cmdlet **Get-** e **Set-** corrispondenti per aggiornare le proprietà delle risorse esistenti e cmdlet **Remove-** per rimuovere le risorse in un account Batch.

Quando si usano molti di questi cmdlet, oltre a passare un oggetto BatchContext è necessario passare oggetti contenenti le impostazioni dettagliate delle risorse, come illustrato nell'esempio seguente. Per altri esempi, vedere la Guida dettagliata dei singoli cmdlet.

### <a name="create-a-batch-pool"></a>Creare un pool di Batch
Quando si crea o si aggiorna un pool di Batch, si seleziona una configurazione di servizio cloud o di macchina virtuale per il sistema operativo nei nodi di calcolo. Vedere [Panoramica delle funzionalità di Batch per sviluppatori](batch-api-basics.md#pool). Se si specifica la configurazione di tipo servizio cloud, le immagini dei nodi di calcolo vengono create con una delle [versioni del sistema operativo guest di Azure](../cloud-services/cloud-services-guestos-update-matrix.md#releases). Se si specifica la configurazione di tipo macchina virtuale, è possibile specificare una delle immagini di VM Linux o Windows supportate elencate nel [Marketplace per Macchine virtuali di Azure][vm_marketplace] oppure fornire un'immagine personalizzata preparata dall'utente.

Quando si esegue **New-AzureBatchPool**, passare le impostazioni del sistema operativo in un oggetto PSCloudServiceConfiguration o PSVirtualMachineConfiguration. Il cmdlet seguente, ad esempio, crea un nuovo pool di Batch con nodi di calcolo di dimensione Small nella configurazione del servizio cloud, con immagine creata con la versione del sistema operativo più recente della famiglia 3 (Windows Server 2012). In questo caso, il parametro **CloudServiceConfiguration** specifica la variabile *$configuration* come oggetto PSCloudServiceConfiguration. Il parametro **BatchContext** specifica una variabile *$context* definita in precedenza come oggetto BatchAccountContext.

    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSCloudServiceConfiguration" -ArgumentList @(4,"*")

    New-AzureBatchPool -Id "AutoScalePool" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -AutoScaleFormula '$TargetDedicated=4;' -BatchContext $context

Il numero di nodi di calcolo di destinazione nel nuovo pool è determinato da una formula di ridimensionamento automatico. In questo caso, la formula è semplicemente **$TargetDedicated=4**e indica che il numero massimo di nodi di calcolo nel pool è 4.

## <a name="query-for-pools-jobs-tasks-and-other-details"></a>Query per pool, processi, attività e altri dettagli
Usare cmdlet come **Get-AzureBatchPool**, **Get-AzureBatchJob** e **Get-AzureBatchTask** per eseguire query per le entità create in un account Batch.

### <a name="query-for-data"></a>Eseguire query per ottenere dati
Ad esempio, utilizzare **Get-AzureBatchPools** per individuare i pool. Per impostazione predefinita, questo comando esegue una query per tutti i pool dell'account, presupponendo che l'oggetto BatchAccountContext sia già archiviato in *$context*:

    Get-AzureBatchPool -BatchContext $context

### <a name="use-an-odata-filter"></a>Usare un filtro OData
È possibile fornire un filtro OData tramite il parametro **Filter** per trovare solo gli oggetti a cui si è interessati. Ad esempio, è possibile trovare tutti i pool con nomi che iniziano con "myPool":

    $filter = "startswith(id,'myPool')"

    Get-AzureBatchPool -Filter $filter -BatchContext $context

Questo metodo non è flessibile come l'uso di "Where-Object" in una pipeline locale. Tuttavia, la query viene inviata al servizio Batch direttamente e quindi tutti i filtri vengono applicati sul lato server, consentendo un risparmio della larghezza di banda Internet.

### <a name="use-the-id-parameter"></a>Usare il parametro Id
In alternativa a un filtro OData, è possibile usare il parametro **Id** . Per eseguire una query per un pool specifico con Id "myPool":

    Get-AzureBatchPool -Id "myPool" -BatchContext $context

Il parametro **Id** supporta solo la ricerca di ID completi, senza caratteri jolly o filtri di tipo OData.

### <a name="use-the-maxcount-parameter"></a>Usare il parametro MaxCount
Per impostazione predefinita, ogni cmdlet restituisce un massimo di 1000 oggetti. Se si raggiunge questo limite, perfezionare il filtro in modo da restituire meno oggetti o impostare esplicitamente un limite massimo tramite il parametro **MaxCount** . ad esempio:

    Get-AzureBatchTask -MaxCount 2500 -BatchContext $context

Per rimuovere il limite superiore, impostare **MaxCount** su 0 o un valore inferiore.

### <a name="use-the-powershell-pipeline"></a>Usare la pipeline di PowerShell
I cmdlet Batch possono usare la pipeline di PowerShell per inviare dati tra i cmdlet. Questo equivale a specificare un parametro, ma rende più semplice l'uso di più entità.

Ad esempio, per trovare e visualizzare tutte le attività dell'account:

    Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context

Per riavviare ogni nodo di calcolo in un pool:

    Get-AzureBatchComputeNode -PoolId "myPool" -BatchContext $context | Restart-AzureBatchComputeNode -BatchContext $context

## <a name="application-package-management"></a>Gestione dei pacchetti dell'applicazione
I pacchetti dell'applicazione semplificano la distribuzione di applicazioni nei nodi di calcolo dei pool. Con i cmdlet di PowerShell per Batch, è possibile caricare e gestire pacchetti dell'applicazione nell'account Batch e distribuire versioni dei pacchetti nei nodi di calcolo.

**Creare** un'applicazione:

    New-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

**Aggiungere** un pacchetto dell'applicazione:

    New-AzureRmBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0" -Format zip -FilePath package001.zip

Impostare la **versione predefinita** per l'applicazione:

    Set-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -DefaultVersion "1.0"

**Elencare** i pacchetti dell'applicazione

    $application = Get-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

    $application.ApplicationPackages

**Eliminare** un pacchetto dell'applicazione

    Remove-AzureRmBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0"

**Eliminare** un'applicazione

    Remove-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

> [!NOTE]
> È necessario eliminare tutte le versioni dei pacchetti di un'applicazione prima di eliminare l'applicazione. Se si prova a eliminare un'applicazione che ha attualmente pacchetti, verrà visualizzato un errore di conflitto.
> 
> 

### <a name="deploy-an-application-package"></a>Distribuire un pacchetto dell'applicazione
Quando si crea un pool è possibile specificare uno o più pacchetti dell'applicazione da distribuire. Quando si specifica un pacchetto al momento della creazione del pool, il pacchetto viene distribuito in ogni nodo quando questo viene aggiunto al pool. I pacchetti vengono distribuiti anche quando un nodo viene riavviato o ne viene ricreata l'immagine.

Specificare l'opzione `-ApplicationPackageReference` quando si crea un pool per distribuire un pacchetto dell'applicazione nei nodi del pool quando vengono aggiunti al pool. Creare prima un oggetto **PSApplicationPackageReference** e configurarlo con l'ID applicazione e la versione del pacchetto da distribuire nei nodi di calcolo del pool:

    $appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

    $appPackageReference.ApplicationId = "MyBatchApplication"

    $appPackageReference.Version = "1.0"

Creare ora il pool e specificare l'oggetto di riferimento pacchetto come argomento per l'opzione `ApplicationPackageReferences`:

    New-AzureBatchPool -Id "PoolWithAppPackage" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -BatchContext $context -ApplicationPackageReferences $appPackageReference

Per altre informazioni sui pacchetti dell'applicazione, vedere [Distribuire le applicazioni nei nodi di calcolo con i pacchetti dell'applicazione Batch](batch-application-packages.md).

> [!IMPORTANT]
> Per usare i pacchetti dell'applicazione è necessario [collegare un account di archiviazione di Azure](#linked-storage-account-autostorage) all'account Batch.
> 
> 

### <a name="update-a-pools-application-packages"></a>Aggiornare i pacchetti dell’applicazione di un pool
Per aggiornare le applicazioni assegnate a un pool esistente, creare prima un oggetto PSApplicationPackageReference con le proprietà desiderate, ovvero ID applicazione e versione del pacchetto:

    $appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

    $appPackageReference.ApplicationId = "MyBatchApplication"

    $appPackageReference.Version = "2.0"

Ottenere quindi il pool da Batch, cancellare tutti i pacchetti esistenti, aggiungere il nuovo riferimento al pacchetto e aggiornare il servizio Batch con le nuove impostazioni del pool:

    $pool = Get-AzureBatchPool -BatchContext $context -Id "PoolWithAppPackage"

    $pool.ApplicationPackageReferences.Clear()

    $pool.ApplicationPackageReferences.Add($appPackageReference)

    Set-AzureBatchPool -BatchContext $context -Pool $pool

Le proprietà del pool saranno ora aggiornate nel servizio Batch. Per distribuire il nuovo pacchetto dell'applicazione nei nodi di calcolo del pool è tuttavia necessario riavviare o ricreare l'immagine di tali nodi. È possibile riavviare ogni nodo di un pool con questo comando:

    Get-AzureBatchComputeNode -PoolId "PoolWithAppPackage" -BatchContext $context | Restart-AzureBatchComputeNode -BatchContext $context

> [!TIP]
> È possibile distribuire più pacchetti dell'applicazione nei nodi di calcolo del pool. Per *aggiungere* un pacchetto dell'applicazione invece di sostituire i pacchetti attualmente distribuiti, omettere la riga `$pool.ApplicationPackageReferences.Clear()` indicata in precedenza.
> 
> 

## <a name="next-steps"></a>Passaggi successivi
* Per la sintassi dettagliata ed esempi dei cmdlet, vedere le [informazioni di riferimento sui cmdlet per Azure Batch](/powershell/module/azurerm.batch/#batch).
* Per altre informazioni sulle applicazioni e sui pacchetti dell'applicazione in Batch, vedere [Distribuire le applicazioni nei nodi di calcolo con i pacchetti dell'applicazione Batch](batch-application-packages.md).

[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/