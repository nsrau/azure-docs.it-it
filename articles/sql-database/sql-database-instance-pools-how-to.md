---
title: Guida alle procedure per i pool di istanze (anteprima)Instance pools how-to guide (preview)
description: Questo articolo descrive come creare e gestire i pool di istanze del database SQL di Azure (anteprima).
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab
ms.date: 09/05/2019
ms.openlocfilehash: 4a27165d929cc9bc5f18e372f7f108887e466e43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299363"
---
# <a name="azure-sql-database-instance-pools-preview-how-to-guide"></a>Guida alle procedure relative ai pool di istanze di database SQL di Azure (anteprima)Azure SQL Database instance pools (preview) how-to how-to guide

In questo articolo vengono fornite informazioni dettagliate su come creare e gestire pool di [istanze.](sql-database-instance-pools.md)

## <a name="instance-pool-operations"></a>Operazioni del pool di istanze

The following table shows the available operations related to instance pools and their availability in the Azure portal and PowerShell.

|Comando|Portale di Azure|PowerShell|
|:---|:---|:---|
|Creare il pool di istanze|No|Sì|
|Aggiorna pool di istanze (numero limitato di proprietà)|No |Sì |
|Controllare l'utilizzo e le proprietà del pool di istanze|No|Sì |
|Elimina pool di istanze|No|Sì|
|Creare un'istanza gestita all'interno del pool di istanzeCreate managed instance inside instance pool|No|Sì|
|Aggiornare l'utilizzo delle risorse dell'istanza gestitaUpdate managed instance resource usage|Sì |Sì|
|Controllare l'utilizzo e le proprietà dell'istanza gestitaCheck managed instance usage and properties|Sì|Sì|
|Eliminare l'istanza gestita dal poolDelete managed instance from the pool|Sì|Sì|
|Creare un database nell'istanza gestita inserita nel poolCreate a database in managed instance placed in the pool|Sì|Sì|
|Eliminare un database dall'istanza gestitaDelete a database from managed instance|Sì|Sì|

Comandi [di PowerShell](https://docs.microsoft.com/powershell/module/az.sql/) disponibili

|Cmdlet |Descrizione |
|:---|:---|
|[Nuovo-AzSqlInstancePoolNew-AzSqlInstancePool](/powershell/module/az.sql/new-azsqlinstancepool/) | Crea un pool di istanze del database SQL di Azure. |
|[Get-AzSqlInstancePoolGet-AzSqlInstancePool](/powershell/module/az.sql/get-azsqlinstancepool/) | Restituisce informazioni sul pool di istanze SQL di Azure. |
|[Set-AzSqlInstancePool](/powershell/module/az.sql/set-azsqlinstancepool/) | Imposta le proprietà per un pool di istanze del database SQL di Azure.Sets properties for an Azure SQL Database instance pool. |
|[Rimuovi-AzSqlInstancePool](/powershell/module/az.sql/remove-azsqlinstancepool/) | Rimuove un pool di istanze del database SQL di Azure. |
|[Get-AzSqlInstancePoolUsageGet-AzSqlInstancePoolUsage](/powershell/module/az.sql/get-azsqlinstancepoolusage/) | Restituisce informazioni sull'utilizzo del pool di istanze SQL di Azure. |


Per usare PowerShell, [installare la versione più recente di PowerShell Core](https://docs.microsoft.com/powershell/scripting/install/installing-powershell#powershell)e seguire le istruzioni per installare il modulo di Azure [PowerShell.](https://docs.microsoft.com/powershell/azure/install-az-ps)

Per le operazioni correlate alle istanze relative a istanze sia all'interno di pool che di istanze singole, utilizzare i [comandi dell'istanza gestita](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances)standard, ma la proprietà name del pool di *istanze* deve essere popolata quando si utilizzano questi comandi per un'istanza in un pool.

## <a name="how-to-deploy-managed-instances-into-pools"></a>Come distribuire istanze gestite in poolHow to deploy managed instances into pools

Il processo di distribuzione di un'istanza in un pool è costituito dai due passaggi seguenti:

1. Distribuzione unica del pool di istanze. Si tratta di un'operazione a esecuzione prolungata, in cui la durata è uguale alla distribuzione di una [singola istanza creata in una subnet vuota.](sql-database-managed-instance.md#managed-instance-management-operations)

2. Distribuzione di istanze ripetitiva in un pool di istanze. Il parametro del pool di istanze deve essere specificato in modo esplicito come parte di questa operazione. Si tratta di un'operazione relativamente veloce che richiede in genere fino a 5 minuti.

Nell'anteprima pubblica, entrambi i passaggi sono supportati solo tramite i modelli di PowerShell e Resource Manager.In public preview, both steps are only supported using PowerShell and Resource Manager templates. L'esperienza del portale di Azure non è attualmente disponibile.

Dopo aver distribuito un'istanza gestita in un pool, è *possibile* usare il portale di Azure per modificarne le proprietà nella pagina del piano tariffario.


## <a name="create-an-instance-pool"></a>Creare un pool di istanzeCreate an instance pool

Per creare un pool di istanze:

1. [Creare una rete virtuale con una subnet](#create-a-virtual-network-with-a-subnet).
2. [Creare un pool di istanze](#create-an-instance-pool).


### <a name="create-a-virtual-network-with-a-subnet"></a>Creare una rete virtuale con una subnet 

Per inserire più pool di istanze all'interno della stessa rete virtuale, vedere gli articoli seguenti:To place multiple instance pools inside the same virtual network, see the following articles:

- Determinare le dimensioni della [subnet della rete virtuale per un'istanza gestita del database SQL](sql-database-managed-instance-determine-size-vnet-subnet.md)di Azure.
- Creare una nuova rete virtuale e una nuova subnet usando il modello di portale di [Azure](sql-database-managed-instance-create-vnet-subnet.md) o seguire le istruzioni per la preparazione di una rete [virtuale esistente.](sql-database-managed-instance-configure-vnet-subnet.md)
 


### <a name="create-an-instance-pool"></a>Creare un pool di istanzeCreate an instance pool 

Dopo aver completato i passaggi precedenti, è possibile creare un pool di istanze.

Ai pool di istanze si applicano le restrizioni seguenti:The following restrictions apply to instance pools:

- Solo General Purpose e Gen5 sono disponibili in anteprima pubblica.
- Il nome del pool può contenere solo lettere minuscole, numeri e trattini e non può iniziare con un trattino.
- Se si vuole usare AHB (Azure Hybrid Benefit), viene applicato a livello di pool di istanze. È possibile impostare il tipo di licenza durante la creazione del pool o aggiornarlo in qualsiasi momento dopo la creazione.

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
  -VCore 80 `
  -Edition "GeneralPurpose" `
  -ComputeGeneration "Gen5" `
  -Location "westeurope"
```

> [!IMPORTANT]
> Poiché la distribuzione di un pool di istanze è un'operazione a esecuzione prolungata, è necessario attendere il completamento prima di eseguire uno dei passaggi seguenti in questo articolo.

## <a name="create-a-managed-instance-inside-the-pool"></a>Creare un'istanza gestita all'interno del poolCreate a managed instance inside the pool 

Dopo la corretta distribuzione del pool di istanze, è il momento di creare un'istanza al suo interno.

Per creare un'istanza gestita, eseguire il comando seguente:To create a managed instance, execute the following command:

```powershell
$instanceOne = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 2 -StorageSizeInGB 256
```

La distribuzione di un'istanza all'interno di un pool richiede un paio di minuti. Dopo aver creato la prima istanza, è possibile creare istanze aggiuntive:After the first instance has been created, additional instances can be created:

```powershell
$instanceTwo = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 4 -StorageSizeInGB 512
```

## <a name="create-a-database-inside-an-instance"></a>Creare un database all'interno di un'istanzaCreate a database inside an instance 

Per creare e gestire database in un'istanza gestita all'interno di un pool, usare i comandi a istanza singola.

Per creare un database all'interno di un'istanza gestita:To create a database inside a managed instance:

```powershell
$poolinstancedb = New-AzSqlInstanceDatabase -Name "mipooldb1" -InstanceName "poolmi-001" -ResourceGroupName "myResourceGroup"
```


## <a name="get-instance-pool-usage"></a>Ottenere l'utilizzo del pool di istanzeGet instance pool usage 
 
Per ottenere un elenco di istanze all'interno di un pool:

```powershell
$instancePool | Get-AzSqlInstance
```


Per ottenere l'utilizzo delle risorse del pool:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage
```


Per ottenere una panoramica dettagliata dell'utilizzo del pool e delle istanze al suo interno:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage –ExpandChildren
```

Per elencare i database in un'istanza:

```powershell
$databases = Get-AzSqlInstanceDatabase -InstanceName "pool-mi-001" -ResourceGroupName "resource-group-name"
```


> [!NOTE]
> Esiste un limite di 100 database per pool (non per istanza).


## <a name="scale-a-managed-instance-inside-a-pool"></a>Ridimensionare un'istanza gestita all'interno di un poolScale a managed instance inside a pool 


Dopo aver popolato un'istanza gestita con i database, è possibile raggiungere i limiti di istanza relativi all'archiviazione o alle prestazioni. In tal caso, se l'utilizzo del pool non è stato superato, è possibile ridimensionare l'istanza.
La scalabilità di un'istanza gestita all'interno di un pool è un'operazione che richiede un paio di minuti. Il prerequisito per il ridimensionamento è disponibile vCores e archiviazione a livello di pool di istanze.

Per aggiornare il numero di vCore e dimensioni di archiviazione:

```powershell
$instanceOne | Set-AzSqlInstance -VCore 8 -StorageSizeInGB 512 -InstancePoolName "mi-pool-name"
```


Per aggiornare solo le dimensioni di archiviazione:

```powershell
$instance | Set-AzSqlInstance -StorageSizeInGB 1024 -InstancePoolName "mi-pool-name"
```



## <a name="connect-to-a-managed-instance-inside-a-pool"></a>Connettersi a un'istanza gestita all'interno di un poolConnect to a managed instance inside a pool

Per connettersi a un'istanza gestita in un pool, sono necessari i due passaggi seguenti:To connect to a managed instance in a pool, the following two steps are required:

1. [Abilitare l'endpoint pubblico per l'istanza](#enable-the-public-endpoint-for-the-instance).
2. [Aggiungere una regola in ingresso al gruppo](#add-an-inbound-rule-to-the-network-security-group)di sicurezza di rete.

Al termine di entrambi i passaggi, è possibile connettersi all'istanza usando un indirizzo endpoint pubblico, una porta e le credenziali fornite durante la creazione dell'istanza. 

### <a name="enable-the-public-endpoint-for-the-instance"></a>Abilitare l'endpoint pubblico per l'istanzaEnable the public endpoint for the instance

L'abilitazione dell'endpoint pubblico per un'istanza può essere eseguita tramite il portale di Azure o usando il comando di PowerShell seguente:Enabling the public endpoint for an instance can be done through the Azure portal or using the following PowerShell command:


```powershell
$instanceOne | Set-AzSqlInstance -InstancePoolName "pool-mi-001" -PublicDataEndpointEnabled $true
```

Questo parametro può essere impostato anche durante la creazione dell'istanza.

### <a name="add-an-inbound-rule-to-the-network-security-group"></a>Aggiungere una regola in ingresso al gruppo di sicurezza di reteAdd an inbound rule to the network security group 

Questo passaggio può essere eseguito tramite il portale di Azure o tramite i comandi di PowerShell e può essere eseguito in qualsiasi momento dopo la preparazione della subnet per l'istanza gestita.

Per informazioni dettagliate, vedere Consentire il traffico di endpoint pubblici nel gruppo di sicurezza di [rete.](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group)


## <a name="move-an-existing-single-instance-inside-an-instance-pool"></a>Spostare una singola istanza esistente all'interno di un pool di istanzeMove an existing single instance inside an instance pool 
 
Lo spostamento di istanze all'esterno e all'esterno di un pool è una delle limitazioni dell'anteprima pubblica. Una soluzione alternativa che può essere utilizzata si basa sul ripristino temporizzato dei database da un'istanza esterna a un pool a un'istanza già presente in un pool. 

Entrambe le istanze devono trovarsi nella stessa sottoscrizione e nella stessa area. Il ripristino tra aree e intersottoscrizioni non è attualmente supportato.

Questo processo ha un periodo di inattività.

Per spostare i database esistenti:

1. Sospendere i carichi di lavoro nell'istanza gestita da cui si esegue la migrazione.
2. Generare script per creare database di sistema ed eseguirli nell'istanza che si si riperonda nel pool di istanze.
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

4. Puntare l'applicazione alla nuova istanza e riprenderne i carichi di lavoro.

Se sono presenti più database, ripetere il processo per ogni database.


## <a name="next-steps"></a>Passaggi successivi

- Per un elenco di confronto delle funzionalità, vedere [Confronto tra le funzionalità: database SQL di Azure e SQL Server](sql-database-features.md).
- Per altre informazioni sulla configurazione della rete virtuale, vedere l'articolo relativo alla [configurazione di una rete virtuale per l'istanza gestita](sql-database-managed-instance-connectivity-architecture.md).
- Per una guida introduttiva che crea un'istanza gestita e ripristina un database da un file di backup, vedere [creare un'istanza gestita.](sql-database-managed-instance-get-started.md)
- Per un'esercitazione sull'uso del servizio di migrazione del database di Azure per la migrazione, vedere Migrazione di [istanze gestite tramite DMS.](../dms/tutorial-sql-server-to-managed-instance.md)
- Per un monitoraggio avanzato delle prestazioni del database delle istanze gestite con la funzionalità di informazioni per la risoluzione dei problemi incorporata, vedere Monitorare il database SQL di Azure con Azure SQL Analytics.For advanced monitoring of managed instance database performance with built-in troubleshooting intelligence, see [Monitor Azure SQL Database using Azure SQL Analytics.](../azure-monitor/insights/azure-sql.md)
- Per informazioni sui prezzi, vedere Determinazione dei prezzi delle [istanze gestite dal database SQL.](https://azure.microsoft.com/pricing/details/sql-database/managed/)
