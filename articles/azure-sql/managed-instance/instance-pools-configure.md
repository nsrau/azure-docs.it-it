---
title: Distribuire Istanza gestita SQL in un pool di istanze
titleSuffix: Azure SQL Managed Instance
description: Questo articolo descrive come creare e gestire i pool di Istanza gestita SQL di Azure (anteprima).
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab
ms.date: 09/05/2019
ms.openlocfilehash: 9bd98d69c9a941e8da08fc7ab798c37b1a22f0bc
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86498408"
---
# <a name="deploy-azure-sql-managed-instance-to-an-instance-pool"></a>Distribuire Istanza gestita SQL di Azure in un pool di istanze
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Questo articolo fornisce informazioni dettagliate su come creare un [pool di istanze](instance-pools-overview.md) e distribuirvi istanza gestita di Azure SQL. 

## <a name="instance-pool-operations"></a>Operazioni dei pool di istanze

La tabella seguente illustra le operazioni disponibili correlate ai pool di istanze e la relativa disponibilità nel portale di Azure e in PowerShell.

|Comando|Portale di Azure|PowerShell|
|:---|:---|:---|
|Creare un pool di istanze|No|Sì|
|Aggiornare un pool di istanze (numero limitato di proprietà)|No |Sì |
|Controllare l'utilizzo e le proprietà del pool di istanze|No|Sì |
|Eliminare un pool di istanze|No|Sì|
|Creare un'istanza gestita all'interno di un pool di istanze|No|Sì|
|Aggiornare l'utilizzo delle risorse per un'istanza gestita|Sì |Sì|
|Controllare l'utilizzo e le proprietà di un'istanza gestita|Sì|Sì|
|Eliminare un'istanza gestita dal pool|Sì|Sì|
|Creare un database nell'istanza nel pool|Sì|Sì|
|Eliminare un database da SQL Istanza gestita|Sì|Sì|

[Comandi di PowerShell](https://docs.microsoft.com/powershell/module/az.sql/)disponibili:

|Cmdlet |Descrizione |
|:---|:---|
|[New-AzSqlInstancePool](/powershell/module/az.sql/new-azsqlinstancepool/) | Crea un pool di Istanza gestita SQL. |
|[Get-AzSqlInstancePool](/powershell/module/az.sql/get-azsqlinstancepool/) | Restituisce informazioni su un pool di istanze. |
|[Set-AzSqlInstancePool](/powershell/module/az.sql/set-azsqlinstancepool/) | Imposta le proprietà per un pool di istanze in SQL Istanza gestita. |
|[Remove-AzSqlInstancePool](/powershell/module/az.sql/remove-azsqlinstancepool/) | Rimuove un pool di istanze in SQL Istanza gestita. |
|[Get-AzSqlInstancePoolUsage](/powershell/module/az.sql/get-azsqlinstancepoolusage/) | Restituisce informazioni sull'utilizzo del pool di SQL Istanza gestita. |


Per usare PowerShell, [installare la versione più recente di PowerShell Core](https://docs.microsoft.com/powershell/scripting/install/installing-powershell#powershell) e seguire le istruzioni per [installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Per le operazioni correlate alle istanze sia all'interno di pool che singole, usare i [comandi per istanze gestite](api-references-create-manage-instance.md#powershell-create-and-configure-managed-instances) standard. La proprietà *instance pool name* deve essere popolata quando si usano questi comandi per un'istanza in un pool.

## <a name="deployment-process"></a>Processo di distribuzione

Per distribuire un'istanza gestita in un pool di istanze, è necessario innanzitutto distribuire il pool di istanze, che è un'operazione monouso a esecuzione prolungata in cui la durata coincide con la distribuzione di una [singola istanza creata in una subnet vuota](sql-managed-instance-paas-overview.md#management-operations). Successivamente, è possibile distribuire un'istanza gestita nel pool, che è un'operazione relativamente veloce che richiede in genere fino a cinque minuti. Il parametro del pool di istanze deve essere specificato in modo esplicito come parte di questa operazione.

Nell'anteprima pubblica, entrambe le azioni sono supportate solo con i modelli PowerShell e Azure Resource Manager. L'esperienza del portale di Azure non è attualmente disponibile.

Dopo la distribuzione di un'istanza gestita in un pool, è *possibile* usare il portale di Azure per modificarne le proprietà nella pagina del piano tariffario.

## <a name="create-a-virtual-network-with-a-subnet"></a>Creare una rete virtuale con una subnet 

Per inserire più pool di istanze all'interno della stessa rete virtuale, vedere gli articoli seguenti:

- [Determinare le dimensioni della subnet VNet per istanza gestita SQL di Azure](vnet-subnet-determine-size.md).
- Creare una rete virtuale e una subnet nuove usando il [modello del portale di Azure](virtual-network-subnet-create-arm-template.md) o seguire le istruzioni per [preparare una rete virtuale esistente](vnet-existing-add-subnet.md).
 

## <a name="create-an-instance-pool"></a>Creare un pool di istanze 

Dopo aver completato i passaggi precedenti, è possibile creare un pool di istanze.

Ai pool di istanze si applicano le restrizioni seguenti:

- Nell'anteprima pubblica sono disponibili solo i pool per utilizzo generico e di quinta generazione.
- Il nome del pool può contenere solo lettere minuscole, numeri e trattini e non può iniziare con un trattino.
- Se si desidera utilizzare Vantaggio Azure Hybrid, viene applicata a livello di pool di istanze. È possibile impostare il tipo di licenza durante la creazione del pool o aggiornarlo in qualsiasi momento dopo la creazione.

> [!IMPORTANT]
> La distribuzione di un pool di istanze è un'operazione a esecuzione prolungata che richiede circa 4,5 ore.

Per ottenere i parametri di rete:

```powershell
$virtualNetwork = Get-AzVirtualNetwork -Name "miPoolVirtualNetwork" -ResourceGroupName "myResourceGroup"
$subnet = Get-AzVirtualNetworkSubnetConfig -Name "miPoolSubnet" -VirtualNetwork $virtualNetwork
```

Per creare un pool di istanze:

```powershell
$instancePool = New-AzSqlInstancePool `
  -ResourceGroupName "myResourceGroup" `
  -Name "mi-pool-name" `
  -SubnetId $subnet.Id `
  -LicenseType "LicenseIncluded" `
  -VCore 8 `
  -Edition "GeneralPurpose" `
  -ComputeGeneration "Gen5" `
  -Location "westeurope"
```

> [!IMPORTANT]
> Poiché la distribuzione di un pool di istanze è un'operazione a esecuzione prolungata, è necessario attenderne il completamento prima di eseguire uno dei passaggi successivi contenuti in questo articolo.

## <a name="create-a-managed-instance"></a>Creare un'istanza gestita

Dopo la corretta distribuzione del pool di istanze, è possibile creare un'istanza gestita al suo interno.

Eseguire il comando seguente per creare un'istanza gestita:

```powershell
$instanceOne = $instancePool | New-AzSqlInstance -Name "mi-one-name" -VCore 2 -StorageSizeInGB 256
```

La distribuzione di un'istanza all'interno di un pool richiede un paio di minuti. Una volta creata la prima istanza, è possibile creare istanze aggiuntive:

```powershell
$instanceTwo = $instancePool | New-AzSqlInstance -Name "mi-two-name" -VCore 4 -StorageSizeInGB 512
```

## <a name="create-a-database"></a>Creazione di un database 

Per creare e gestire database in un'istanza gestita all'interno di un pool, usare i comandi a istanza singola.

Per creare un database all'interno di un'istanza gestita:

```powershell
$poolinstancedb = New-AzSqlInstanceDatabase -Name "mipooldb1" -InstanceName "poolmi-001" -ResourceGroupName "myResourceGroup"
```


## <a name="get-pool-usage"></a>Ottenere l'utilizzo del pool 
 
Per ottenere un elenco di istanze all'interno di un pool:

```powershell
$instancePool | Get-AzSqlInstance
```


Per ottenere i dati di uso delle risorse del pool:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage
```


Per ottenere una panoramica dettagliata sull'uso del pool e delle istanze al suo interno:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage –ExpandChildren
```

Per elencare i database in un'istanza:

```powershell
$databases = Get-AzSqlInstanceDatabase -InstanceName "pool-mi-001" -ResourceGroupName "resource-group-name"
```


> [!NOTE]
> È previsto un limite di 100 database per pool (non per istanza).


## <a name="scale"></a>Scalabilità 


Dopo aver popolato un'istanza gestita con i database, è possibile che vengano raggiunti i limiti dell'istanza relativi ad archiviazione o prestazioni. In tal caso, se il limite di uso del pool non è stato superato, è possibile ridimensionare l'istanza.
Il ridimensionamento di un'istanza gestita all'interno di un pool è un'operazione che richiede un paio di minuti. Il prerequisito per il ridimensionamento è disporre di vCore e risorse di archiviazione a livello di pool di istanze.

Per aggiornare il numero di vCore e le dimensioni di archiviazione:

```powershell
$instanceOne | Set-AzSqlInstance -VCore 8 -StorageSizeInGB 512 -InstancePoolName "mi-pool-name"
```


Per aggiornare solo le dimensioni di archiviazione:

```powershell
$instance | Set-AzSqlInstance -StorageSizeInGB 1024 -InstancePoolName "mi-pool-name"
```

## <a name="connect"></a>Connessione 

Per connettersi a un'istanza gestita in un pool è necessario eseguire i due passaggi seguenti:

1. [Abilitare l'endpoint pubblico per l'istanza](#enable-the-public-endpoint).
2. [Aggiungere una regola in ingresso al gruppo di sicurezza di rete (NSG)](#add-an-inbound-rule-to-the-network-security-group).

Una volta completati entrambi i passaggi, è possibile connettersi all'istanza tramite un indirizzo endpoint pubblico, una porta e le credenziali fornite durante la creazione dell'istanza. 

### <a name="enable-the-public-endpoint"></a>Abilitare l'endpoint pubblico

L'abilitazione dell'endpoint pubblico per un'istanza può essere eseguita tramite il portale di Azure o tramite il comando PowerShell seguente:


```powershell
$instanceOne | Set-AzSqlInstance -InstancePoolName "pool-mi-001" -PublicDataEndpointEnabled $true
```

Questo parametro può essere impostato anche durante la creazione dell'istanza.

### <a name="add-an-inbound-rule-to-the-network-security-group"></a>Aggiungere una regola in ingresso al gruppo di sicurezza di rete 

Questo passaggio può essere eseguito tramite il portale di Azure o i comandi di PowerShell e in qualsiasi momento dopo la preparazione della subnet per l'istanza gestita.

Per informazioni dettagliate, vedere [Consentire il traffico dell'endpoint pubblico nel gruppo di sicurezza di rete](public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group).


## <a name="move-an-existing-single-instance-to-a-pool"></a>Spostare un'istanza singola esistente in un pool
 
Lo spostamento delle istanze all'interno e all'esterno di un pool è uno dei limiti dell'anteprima pubblica. Una soluzione alternativa si basa sul ripristino temporizzato dei database da un'istanza esterna a un pool a un'istanza già presente in un pool. 

Entrambe le istanze devono trovarsi nella stessa sottoscrizione e nella stessa area. Il ripristino tra le aree e tra le sottoscrizioni non è supportato al momento.

Questo processo ha un periodo di tempo di inattività.

Per spostare i database esistenti:

1. Sospendere i carichi di lavoro nell'istanza gestita da cui si esegue la migrazione.
2. Generare script per creare database di sistema ed eseguirli nell'istanza all'interno del pool di istanze.
3. Eseguire un ripristino temporizzato di ogni database dalla singola istanza all'istanza nel pool.

    ```powershell
    $resourceGroupName = "my resource group name"
    $managedInstanceName = "my managed instance name"
    $databaseName = "my source database name"
    $pointInTime = "2019-08-21T08:51:39.3882806Z"
    $targetDatabase = "name of the new database that will be created"
    $targetResourceGroupName = "resource group of instance pool"
    $targetInstanceName = "pool instance name"
       
    Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
      -ResourceGroupName $resourceGroupName `
      -InstanceName $managedInstanceName `
      -Name $databaseName `
      -PointInTime $pointInTime `
      -TargetInstanceDatabaseName $targetDatabase `
      -TargetResourceGroupName $targetResourceGroupName `
      -TargetInstanceName $targetInstanceName
    ```

4. Puntare l'applicazione alla nuova istanza e riprendere i carichi di lavoro.

Se sono presenti più database, ripetere il processo per ogni database.


## <a name="next-steps"></a>Passaggi successivi

- Per un elenco di confronto delle funzionalità, vedere [Confronto tra le funzionalità: database SQL di Azure e SQL Server](../database/features-comparison.md).
- Per ulteriori informazioni sulla configurazione di VNet, vedere la pagina relativa alla [configurazione di SQL istanza gestita VNet](connectivity-architecture-overview.md).
- Per una guida introduttiva per la creazione di un'istanza gestita e il ripristino di un database da un file di backup, vedere [creare un'istanza gestita](instance-create-quickstart.md).
- Per un'esercitazione sull'uso del servizio migrazione del database di Azure per la migrazione, vedere la pagina relativa alla [migrazione istanza gestita SQL con il servizio migrazione del database](../../dms/tutorial-sql-server-to-managed-instance.md).
- Per il monitoraggio avanzato delle prestazioni del database SQL Istanza gestita con l'Intelligence per la risoluzione dei problemi incorporata, vedere [monitorare istanza gestita SQL di Azure con analisi SQL di Azure](../../azure-monitor/insights/azure-sql.md).
- Per informazioni sui prezzi, vedere la pagina relativa ai [prezzi di SQL istanza gestita](https://azure.microsoft.com/pricing/details/sql-database/managed/).
