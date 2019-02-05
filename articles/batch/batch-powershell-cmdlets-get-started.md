---
title: Introduzione a PowerShell - Azure Batch | Microsoft Docs
description: Breve introduzione ai cmdlet di Azure PowerShell da usare per gestire le risorse Batch.
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: powershell
ms.workload: big-compute
ms.date: 01/15/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 3ceb7585a26290985cd5a2c523ad8094b1d1a40a
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55194237"
---
# <a name="manage-batch-resources-with-powershell-cmdlets"></a>Gestire le risorse Batch con i cmdlet di PowerShell

Con i cmdlet di PowerShell per Azure Batch è possibile eseguire molte delle attività eseguite con le API Batch, il portale di Azure e l'interfaccia della riga di comando di Azure e creare i relativi script. Questa è una rapida introduzione ai cmdlet con cui è possibile gestire gli account Batch e usare risorse di Batch come pool, processi e attività.

Per un elenco completo di cmdlet Batch e per la sintassi dettagliata dei cmdlet, vedere [Informazioni di riferimento sui cmdlet di Azure Batch](/powershell/module/az.batch).

Questo articolo si basa sui cmdlet del modulo Azure Batch versione 1.0.0. È consigliabile eseguire di frequente l'aggiornamento dei moduli di Azure PowerShell per sfruttare i vantaggi degli aggiornamenti e dei miglioramenti del servizio.

## <a name="prerequisites"></a>Prerequisiti

* [Installare e configurare il modulo Azure PowerShell](/powershell/azure/overview). Per installare un modulo di Azure Batch specifico, ad esempio una versione non definitiva del modulo, vedere [PowerShell Gallery](https://www.powershellgallery.com/packages/Az.Batch/1.0.0).

* Eseguire il cmdlet **Connect-AzAccount** per connettersi alla sottoscrizione. I cmdlet di Azure Batch sono disponibili nel modulo Azure Resource Manager:

  ```PowerShell
  Connect-AzAccount
  ```

* **Effettuare la registrazione con lo spazio dei nomi del provider di Batch**. È necessario eseguire questa operazione solo **una volta per ogni sottoscrizione**.
  
  ```PowerShell
  Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
  ```

## <a name="manage-batch-accounts-and-keys"></a>Gestire gli account e le chiavi Batch

### <a name="create-a-batch-account"></a>Creare un account Batch

**New-AzBatchAccount** crea un account Batch in un gruppo di risorse specificato. Se non si ha già un gruppo di risorse, crearne uno eseguendo il cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Specificare una delle aree di Azure nel parametro **location**, ad esempio "Stati Uniti centrali". Ad esempio: 

```PowerShell
New-AzResourceGroup –Name MyBatchResourceGroup –Location "Central US"
```

Creare quindi un account Batch nel gruppo di risorse. Specificare il nome dell'account in <*account_name*> e la località e il nome del gruppo di risorse. La creazione dell'account Batch può richiedere tempo. Ad esempio: 

```PowerShell
New-AzBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName <res_group_name>
```

> [!NOTE]
> Il nome dell'account Batch deve essere univoco nell'area di Azure per il gruppo di risorse e contenere tra 3 e 24 caratteri (sono ammessi solo numeri e lettere minuscole).

### <a name="get-account-access-keys"></a>Ottenere le chiavi di accesso all'account

**Get-AzBatchAccountKeys** mostra le chiavi di accesso associate a un account Azure Batch. Ad esempio, eseguire le operazioni seguenti per ottenere le chiavi primarie e secondarie dell'account creato.

 ```PowerShell
$Account = Get-AzBatchAccountKeys –AccountName <account_name>

$Account.PrimaryAccountKey

$Account.SecondaryAccountKey
```

### <a name="generate-a-new-access-key"></a>Generare una nuova chiave di accesso

**New-AzBatchAccountKey** genera una nuova chiave dell'account primaria o secondaria per un account Azure Batch. Ad esempio, per generare una nuova chiave primaria per l'account Batch, digitare:

```PowerShell
New-AzBatchAccountKey -AccountName <account_name> -KeyType Primary
```

> [!NOTE]
> Per generare una nuova chiave secondaria, specificare "Secondary" per il parametro **KeyType** . È necessario rigenerare la chiave primaria e quella secondaria separatamente.

### <a name="delete-a-batch-account"></a>Eliminare un account Batch

**Remove-AzBatchAccount** elimina un account Batch. Ad esempio: 

```PowerShell
Remove-AzBatchAccount -AccountName <account_name>
```

Quando richiesto, confermare che si desidera rimuovere l'account. La rimozione di un account può richiedere alcuni minuti.

## <a name="create-a-batchaccountcontext-object"></a>Creare un oggetto BatchAccountContext

È possibile eseguire l'autenticazione per la gestione di risorse Batch usando l'autenticazione con chiave condivisa o l'autenticazione di Azure Active Directory. Per eseguire l'autenticazione usando i cmdlet di PowerShell per Batch, creare prima un oggetto BatchAccountContext in cui archiviare le credenziali dell'account o l'identità. Passare l'oggetto BatchAccountContext nei cmdlet che usano il parametro **BatchContext** .

### <a name="shared-key-authentication"></a>Autenticazione della chiave condivisa

```PowerShell
$context = Get-AzBatchAccountKeys -AccountName <account_name>
```

> [!NOTE]
> Per impostazione predefinita, la chiave primaria dell'account viene usata per l'autenticazione, ma è possibile selezionare in modo esplicito la chiave da usare modificando la proprietà **KeyInUse** dell'oggetto BatchAccountContext: `$context.KeyInUse = "Secondary"`.

### <a name="azure-active-directory-authentication"></a>Autenticazione di Azure Active Directory

```PowerShell
$context = Get-AzBatchAccount -AccountName <account_name>
```

## <a name="create-and-modify-batch-resources"></a>Creare e modificare le risorse Batch

Usare cmdlet come **New-AzBatchPool**, **New-AzBatchJob** e **New-AzBatchTask** per creare risorse in un account Batch. Sono disponibili cmdlet **Get-** e **Set-** corrispondenti per aggiornare le proprietà delle risorse esistenti e cmdlet **Remove-** per rimuovere le risorse in un account Batch.

Quando si usano molti di questi cmdlet, oltre a passare un oggetto BatchContext è necessario passare oggetti contenenti le impostazioni dettagliate delle risorse, come illustrato nell'esempio seguente. Per altri esempi, vedere la Guida dettagliata dei singoli cmdlet.

### <a name="create-a-batch-pool"></a>Creare un pool di Batch

Quando si crea o si aggiorna un pool di Batch, si seleziona una configurazione di servizi cloud o di macchina virtuale per il sistema operativo nei nodi di calcolo. Vedere [Panoramica delle funzionalità di Batch](batch-api-basics.md#pool). Se si specifica la configurazione di servizi cloud, le immagini dei nodi di calcolo vengono create con una delle [versioni del sistema operativo guest di Azure](../cloud-services/cloud-services-guestos-update-matrix.md#releases). Se si specifica la configurazione di tipo macchina virtuale, è possibile specificare una delle immagini di VM Linux o Windows supportate elencate nel [Marketplace per Macchine virtuali di Azure][vm_marketplace] oppure fornire un'immagine personalizzata preparata dall'utente.

Quando si esegue **New-AzBatchPool**, passare le impostazioni del sistema operativo in un oggetto PSCloudServiceConfiguration o PSVirtualMachineConfiguration. Il frammento di codice seguente, ad esempio, crea un pool di Batch con nodi di calcolo di dimensioni Standard_A1 nella configurazione della macchina virtuale. L'immagine viene creata con Ubuntu Server 18.04-LTS. In questo caso, il parametro **VirtualMachineConfiguration** specifica la variabile *$configuration* come oggetto PSVirtualMachineConfiguration. Il parametro **BatchContext** specifica una variabile *$context* definita in precedenza come oggetto BatchAccountContext.

```PowerShell
$imageRef = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSImageReference" -ArgumentList @("UbuntuServer","Canonical","18.04.0-LTS")

$configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSVirtualMachineConfiguration" -ArgumentList @($imageRef, "batch.node.ubuntu 18.04")

New-AzBatchPool -Id "mypspool" -VirtualMachineSize "Standard_a1" -VirtualMachineConfiguration $configuration -AutoScaleFormula '$TargetDedicated=4;' -BatchContext $context
```

Il numero di nodi di calcolo di destinazione nel nuovo pool è calcolato da una formula di ridimensionamento automatico. In questo caso, la formula è semplicemente **$TargetDedicated=4**e indica che il numero massimo di nodi di calcolo nel pool è 4.

## <a name="query-for-pools-jobs-tasks-and-other-details"></a>Query per pool, processi, attività e altri dettagli

Usare cmdlet come **Get-AzBatchPool**, **Get-AzBatchJob** e **Get-AzBatchTask** per eseguire query per le entità create in un account Batch.

### <a name="query-for-data"></a>Eseguire query per ottenere dati

Ad esempio, usare **Get-AzBatchPools** per trovare i pool. Per impostazione predefinita, questo comando esegue una query per tutti i pool dell'account, presupponendo che l'oggetto BatchAccountContext sia già archiviato in *$context*:

```PowerShell
Get-AzBatchPool -BatchContext $context
```

### <a name="use-an-odata-filter"></a>Usare un filtro OData

È possibile fornire un filtro OData tramite il parametro **Filter** per trovare solo gli oggetti a cui si è interessati. Ad esempio, è possibile trovare tutti i pool con ID che iniziano con "myPool":

```PowerShell
$filter = "startswith(id,'myPool')"

Get-AzBatchPool -Filter $filter -BatchContext $context
```

Questo metodo non è flessibile come l'uso di "Where-Object" in una pipeline locale. Tuttavia, la query viene inviata al servizio Batch direttamente e quindi tutti i filtri vengono applicati sul lato server, consentendo un risparmio della larghezza di banda Internet.

### <a name="use-the-id-parameter"></a>Usare il parametro Id

In alternativa a un filtro OData, è possibile usare il parametro **Id** . Per eseguire una query per un pool specifico con Id "myPool":

```PowerShell
Get-AzBatchPool -Id "myPool" -BatchContext $context
```

Il parametro **Id** supporta solo la ricerca di ID completi, senza caratteri jolly o filtri di tipo OData.

### <a name="use-the-maxcount-parameter"></a>Usare il parametro MaxCount

Per impostazione predefinita, ogni cmdlet restituisce un massimo di 1000 oggetti. Se si raggiunge questo limite, perfezionare il filtro in modo da restituire meno oggetti o impostare esplicitamente un limite massimo tramite il parametro **MaxCount** . Ad esempio: 

```PowerShell
Get-AzBatchTask -MaxCount 2500 -BatchContext $context
```

Per rimuovere il limite superiore, impostare **MaxCount** su 0 o un valore inferiore.

### <a name="use-the-powershell-pipeline"></a>Usare la pipeline di PowerShell

I cmdlet Batch usano la pipeline di PowerShell per inviare dati tra i cmdlet. Questo equivale a specificare un parametro, ma rende più semplice l'uso di più entità.

Ad esempio, per trovare e visualizzare tutte le attività dell'account:

```PowerShell
Get-AzBatchJob -BatchContext $context | Get-AzBatchTask -BatchContext $context
```

Per riavviare ogni nodo di calcolo in un pool:

```PowerShell
Get-AzBatchComputeNode -PoolId "myPool" -BatchContext $context | Restart-AzBatchComputeNode -BatchContext $context
```

## <a name="application-package-management"></a>Gestione dei pacchetti dell'applicazione

I pacchetti dell'applicazione semplificano la distribuzione di applicazioni nei nodi di calcolo dei pool. Con i cmdlet di PowerShell per Batch, è possibile caricare e gestire pacchetti dell'applicazione nell'account Batch e distribuire versioni dei pacchetti nei nodi di calcolo.

**Creare** un'applicazione:

```PowerShell
New-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"
```

**Aggiungere** un pacchetto dell'applicazione:

```PowerShell
New-AzBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0" -Format zip -FilePath package001.zip
```

Impostare la **versione predefinita** per l'applicazione:

```PowerShell
Set-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -DefaultVersion "1.0"
```

**Elencare** i pacchetti dell'applicazione

```PowerShell
$application = Get-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

$application.ApplicationPackages
```

**Eliminare** un pacchetto dell'applicazione

```PowerShell
Remove-AzBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0"
```

**Eliminare** un'applicazione

```PowerShell
Remove-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"
```

> [!NOTE]
> È necessario eliminare tutte le versioni dei pacchetti di un'applicazione prima di eliminare l'applicazione. Se si prova a eliminare un'applicazione che ha attualmente pacchetti, verrà visualizzato un errore di conflitto.

### <a name="deploy-an-application-package"></a>Distribuire un pacchetto dell'applicazione

Quando si crea un pool è possibile specificare uno o più pacchetti dell'applicazione da distribuire. Quando si specifica un pacchetto al momento della creazione del pool, il pacchetto viene distribuito in ogni nodo quando questo viene aggiunto al pool. I pacchetti vengono distribuiti anche quando un nodo viene riavviato o ne viene ricreata l'immagine.

Specificare l'opzione `-ApplicationPackageReference` quando si crea un pool per distribuire un pacchetto dell'applicazione nei nodi del pool quando vengono aggiunti al pool. Creare prima un oggetto **PSApplicationPackageReference** e configurarlo con l'ID applicazione e la versione del pacchetto da distribuire nei nodi di calcolo del pool:

```PowerShell
$appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

$appPackageReference.ApplicationId = "MyBatchApplication"

$appPackageReference.Version = "1.0"
```

Creare ora il pool e specificare l'oggetto di riferimento pacchetto come argomento per l'opzione `ApplicationPackageReferences`:

```PowerShell
New-AzBatchPool -Id "PoolWithAppPackage" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -BatchContext $context -ApplicationPackageReferences $appPackageReference
```

Per altre informazioni sui pacchetti dell'applicazione, vedere [Distribuire le applicazioni nei nodi di calcolo con i pacchetti dell'applicazione Batch](batch-application-packages.md).

> [!IMPORTANT]
> Per usare i pacchetti dell'applicazione è necessario [collegare un account di archiviazione di Azure](#linked-storage-account-autostorage) all'account Batch.

### <a name="update-a-pools-application-packages"></a>Aggiornare i pacchetti dell’applicazione di un pool

Per aggiornare le applicazioni assegnate a un pool esistente, creare prima un oggetto PSApplicationPackageReference con le proprietà desiderate, ovvero ID applicazione e versione del pacchetto:

```PowerShell
$appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

$appPackageReference.ApplicationId = "MyBatchApplication"

$appPackageReference.Version = "2.0"

```

Ottenere quindi il pool da Batch, cancellare tutti i pacchetti esistenti, aggiungere il nuovo riferimento al pacchetto e aggiornare il servizio Batch con le nuove impostazioni del pool:

```PowerShell
$pool = Get-AzBatchPool -BatchContext $context -Id "PoolWithAppPackage"

$pool.ApplicationPackageReferences.Clear()

$pool.ApplicationPackageReferences.Add($appPackageReference)

Set-AzBatchPool -BatchContext $context -Pool $pool
```

Le proprietà del pool saranno ora aggiornate nel servizio Batch. Per distribuire il nuovo pacchetto dell'applicazione nei nodi di calcolo del pool è tuttavia necessario riavviare o ricreare l'immagine di tali nodi. È possibile riavviare ogni nodo di un pool con questo comando:

```PowerShell
Get-AzBatchComputeNode -PoolId "PoolWithAppPackage" -BatchContext $context | Restart-AzBatchComputeNode -BatchContext $context
```

> [!TIP]
> È possibile distribuire più pacchetti dell'applicazione nei nodi di calcolo del pool. Per *aggiungere* un pacchetto dell'applicazione invece di sostituire i pacchetti attualmente distribuiti, omettere la riga `$pool.ApplicationPackageReferences.Clear()` indicata in precedenza.

## <a name="next-steps"></a>Passaggi successivi

* Per la sintassi dettagliata ed esempi dei cmdlet, vedere le [informazioni di riferimento sui cmdlet per Azure Batch](/powershell/module/az.batch).
* Per altre informazioni sulle applicazioni e sui pacchetti dell'applicazione in Batch, vedere [Distribuire le applicazioni nei nodi di calcolo con i pacchetti dell'applicazione Batch](batch-application-packages.md).

[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/