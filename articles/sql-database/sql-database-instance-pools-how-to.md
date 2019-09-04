---
title: Guida alle procedure per i pool di istanze del database SQL di Azure (anteprima) | Microsoft Docs
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
ms.openlocfilehash: 5862a54c92de7395ce42865ae32d453e926048d8
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70294269"
---
# <a name="azure-sql-database-instance-pools-preview-how-to-guide"></a>Guida alle procedure per pool di istanze del database SQL di Azure (anteprima)

Questo articolo fornisce informazioni dettagliate su come creare e gestire [pool di istanze](sql-database-instance-pools.md).

## <a name="instance-pool-operations"></a>Operazioni del pool di istanze

La tabella seguente illustra le operazioni disponibili correlate ai pool di istanze e la relativa disponibilità nel portale di Azure e in PowerShell.

|Comando|Portale di Azure|PowerShell|
|:---|:---|:---|
|Crea pool di istanze|No|Yes|
|Aggiornare il pool di istanze (numero limitato di proprietà)|No |Sì |
|Controllare l'utilizzo e le proprietà del pool di istanze|No|Sì |
|Elimina pool di istanze|No|Sì|
|Crea istanza gestita all'interno del pool di istanze|No|Yes|
|Aggiornare l'utilizzo delle risorse dell'istanza gestita|Sì |Yes|
|Controllare l'utilizzo e le proprietà delle istanze gestite|Yes|Sì|
|Elimina istanza gestita dal pool|Sì|Yes|
|Creare un database nell'istanza gestita posizionata nel pool|Sì|Yes|
|Eliminare un database da un'istanza gestita|Yes|Yes|

[Comandi di PowerShell](https://docs.microsoft.com/powershell/module/az.sql/) disponibili

|Cmdlet |DESCRIZIONE |
|:---|:---|
|[New-AzSqlInstancePool](/powershell/module/az.sql/new-azsqlinstancepool/) | Crea un pool di istanze del database SQL di Azure. |
|[Get-AzSqlInstancePool](/powershell/module/az.sql/get-azsqlinstancepool/) | Restituisce informazioni sul pool di istanze di SQL Azure. |
|[Set-AzSqlInstancePool](/powershell/module/az.sql/set-azsqlinstancepool/) | Imposta le proprietà per un pool di istanze del database SQL di Azure. |
|[Remove-AzSqlInstancePool](/powershell/module/az.sql/remove-azsqlinstancepool/) | Rimuove un pool di istanze del database SQL di Azure. |
|[Get-AzSqlInstancePoolUsage](/powershell/module/az.sql/get-azsqlinstancepoolusage/) | Restituisce informazioni sull'utilizzo del pool di istanze SQL di Azure. |


Per usare PowerShell, [installare la versione più recente di PowerShell Core](https://docs.microsoft.com/powershell/scripting/install/installing-powershell#powershell-core)e seguire le istruzioni per [installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Per le operazioni correlate alle istanze sia all'interno di pool che a istanze singole, utilizzare i [comandi di istanza gestita](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances)standard, ma la proprietà *nome pool* di istanze deve essere popolata quando si utilizzano questi comandi per un'istanza in un pool.

## <a name="how-to-deploy-managed-instances-into-pools"></a>Come distribuire istanze gestite in pool

Il processo di distribuzione di un'istanza di in un pool è costituito dai due passaggi seguenti:

1. Distribuzione pool di istanze unidirezionale. Si tratta di un'operazione a esecuzione prolungata, in cui la durata coincide con la distribuzione di una [singola istanza creata in una subnet vuota](sql-database-managed-instance.md#managed-instance-management-operations).

2. Distribuzione di istanze ripetitive in un pool di istanze. Il parametro del pool di istanze deve essere specificato in modo esplicito come parte di questa operazione. Si tratta di un'operazione relativamente veloce che richiede in genere fino a 5 minuti.

Nella versione di anteprima pubblica, entrambi i passaggi sono supportati solo con i modelli PowerShell e Gestione risorse. L'esperienza portale di Azure non è attualmente disponibile.

Dopo la distribuzione di un'istanza gestita in un pool, è *possibile* utilizzare il portale di Azure per modificarne le proprietà nella pagina piano tariffario.


## <a name="create-an-instance-pool"></a>Creare un pool di istanze

Per creare un pool di istanze:

1. [Creare una rete virtuale con una subnet](#create-a-virtual-network-with-a-subnet).
2. [Creare un pool di istanze](#create-an-instance-pool).


### <a name="create-a-virtual-network-with-a-subnet"></a>Creare una rete virtuale con una subnet 

Per inserire più pool di istanze all'interno della stessa rete virtuale, vedere gli articoli seguenti:

- [Determinare le dimensioni della subnet VNet per un'istanza gestita di database SQL di Azure](sql-database-managed-instance-determine-size-vnet-subnet.md).
- Creare una nuova rete virtuale e una nuova subnet usando il [modello di portale di Azure](sql-database-managed-instance-create-vnet-subnet.md) o seguire le istruzioni per la [preparazione di una rete virtuale esistente](sql-database-managed-instance-configure-vnet-subnet.md).
 


### <a name="create-an-instance-pool"></a>Creare un pool di istanze 

Dopo aver completato i passaggi precedenti, è possibile creare un pool di istanze.

Ai pool di istanze si applicano le restrizioni seguenti:

- Solo per utilizzo generico e quinta generazione sono disponibili in anteprima pubblica.
- Il nome del pool può contenere solo lettere minuscole, numeri e trattini e non può iniziare con un trattino.
- Per ottenere l'ID subnet, usare `Get-AzVirtualNetworkSubnetConfig -Name "miPoolSubnet" -VirtualNetwork $virtualNetwork`.
- Se si desidera utilizzare vantaggio Azure Hybrid (Vantaggio Azure Hybrid), viene applicato a livello di pool di istanze. È possibile impostare il tipo di licenza durante la creazione del pool o aggiornarlo in qualsiasi momento dopo la creazione.

> [!IMPORTANT]
> La distribuzione di un pool di istanze è un'operazione a esecuzione prolungata che richiede circa 4,5 ore.

Per creare un pool di istanze:

```powershell
$instancePool = New-AzSqlInstancePool `
  -ResourceGroupName "myResourceGroup" `
  -Name "mi-pool-name" `
  -SubnetId "/subscriptions/subscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/miPoolVirtualNetwork/subnets/miPoolSubnet" `
  -LicenseType "LicenseIncluded" `
  -VCore 80 `
  -Edition "GeneralPurpose" `
  -ComputeGeneration "Gen5" `
  -Location "westeurope"
```

> [!IMPORTANT]
> Poiché la distribuzione di un pool di istanze è un'operazione a esecuzione prolungata, è necessario attenderne il completamento prima di eseguire uno dei passaggi seguenti in questo articolo.

## <a name="create-a-managed-instance-inside-the-pool"></a>Creare un'istanza gestita all'interno del pool 

Al termine della distribuzione del pool di istanze, è possibile creare un'istanza al suo interno.

Per creare un'istanza gestita, eseguire il comando seguente:

```powershell
$instanceOne = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 2 -StorageSizeInGB 256
```

La distribuzione di un'istanza di all'interno di un pool richiede un paio di minuti. Una volta creata la prima istanza, è possibile creare istanze aggiuntive:

```powershell
$instanceTwo = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 4 -StorageSizeInGB 512
```

## <a name="create-a-database-inside-an-instance"></a>Creazione di un database all'interno di un'istanza 

Per creare e gestire database in un'istanza gestita all'interno di un pool, usare i comandi a istanza singola.

Per creare un database all'interno di un'istanza gestita:

```powershell
$poolinstancedb = New-AzSqlInstanceDatabase -Name "mipooldb1" -InstanceName "poolmi-001" -ResourceGroupName "myResourceGroup"
```


## <a name="get-instance-pool-usage"></a>Ottenere l'utilizzo del pool di istanze 
 
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
> È previsto un limite di 100 database per pool (non per istanza).


## <a name="scale-a-managed-instance-inside-a-pool"></a>Ridimensionare un'istanza gestita all'interno di un pool 


Dopo aver popolato un'istanza gestita con i database, è possibile che vengano raggiunti i limiti dell'istanza relativi all'archiviazione o alle prestazioni In tal caso, se l'utilizzo del pool non è stato superato, è possibile ridimensionare l'istanza.
Il ridimensionamento di un'istanza gestita all'interno di un pool è un'operazione che richiede un paio di minuti. Il prerequisito per la scalabilità è vcore e archiviazione disponibili a livello di pool di istanze.

Per aggiornare il numero di Vcore e le dimensioni di archiviazione:

```powershell
$instanceOne | Set-AzSqlInstance -VCore 8 -StorageSizeInGB 512 -InstancePoolName "mi-pool-name"
```


Per aggiornare solo le dimensioni di archiviazione:

```powershell
$instance | Set-AzSqlInstance -StorageSizeInGB 1024 -InstancePoolName "mi-pool-name"
```



## <a name="connect-to-a-managed-instance-inside-a-pool"></a>Connettersi a un'istanza gestita all'interno di un pool

Per connettersi a un'istanza gestita in un pool, è necessario eseguire i due passaggi seguenti:

1. [Abilitare l'endpoint pubblico per l'istanza](#enable-the-public-endpoint-for-the-instance).
2. [Aggiungere una regola in ingresso al gruppo di sicurezza di rete (NSG)](#add-an-inbound-rule-to-the-network-security-group).

Una volta completati entrambi i passaggi, è possibile connettersi all'istanza di utilizzando un indirizzo endpoint pubblico, una porta e le credenziali fornite durante la creazione dell'istanza. 

### <a name="enable-the-public-endpoint-for-the-instance"></a>Abilitare l'endpoint pubblico per l'istanza

L'abilitazione dell'endpoint pubblico per un'istanza può essere eseguita tramite il portale di Azure o tramite il comando PowerShell seguente:


```powershell
$instanceOne | Set-AzSqlInstance -InstancePoolName "pool-mi-001" -PublicDataEndpointEnabled $true
```

Questo parametro può essere impostato anche durante la creazione dell'istanza.

### <a name="add-an-inbound-rule-to-the-network-security-group"></a>Aggiungere una regola in ingresso al gruppo di sicurezza di rete 

Questo passaggio può essere eseguito tramite il portale di Azure o tramite i comandi di PowerShell e può essere eseguito in qualsiasi momento dopo la preparazione della subnet per l'istanza gestita.

Per informazioni dettagliate, vedere [consentire il traffico dell'endpoint pubblico nel gruppo di sicurezza di rete](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group).


## <a name="move-an-existing-single-instance-inside-an-instance-pool"></a>Spostare un'istanza singola esistente all'interno di un pool di istanze 
 
Lo stato di trasferimento di istanze all'interno e all'esterno di un pool è uno dei limiti di anteprima pubblica. Una soluzione alternativa utilizzabile si basa sul ripristino temporizzato dei database da un'istanza all'esterno di un pool a un'istanza che si trova già in un pool. 

Entrambe le istanze devono trovarsi nella stessa sottoscrizione e nella stessa area. Il ripristino tra più aree e tra sottoscrizioni non è attualmente supportato.

Questo processo ha un periodo di inattività.

Per spostare i database esistenti:

1. Sospendere i carichi di lavoro nell'istanza gestita da cui si esegue la migrazione.
2. Generare script per creare database di sistema ed eseguirli nell'istanza all'interno del pool di istanze.
3. Eseguire un ripristino temporizzato di ogni database da una singola istanza all'istanza nel pool.

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

- Per un elenco di confronto delle funzionalità, vedere [Confronto tra le funzionalità: database SQL di Azure e SQL Server](sql-database-features.md).
- Per altre informazioni sulla configurazione della rete virtuale, vedere l'articolo relativo alla [configurazione di una rete virtuale per l'istanza gestita](sql-database-managed-instance-connectivity-architecture.md).
- Per una guida di avvio rapido sulla creazione di un'istanza gestita e il ripristino di un database da un file di backup, vedere [Creare un'istanza gestita](sql-database-managed-instance-get-started.md).
- Per un'esercitazione sull'uso di Servizio Migrazione del database di Azure per la migrazione, vedere l'articolo relativo alla [migrazione all'istanza gestita con Servizio Migrazione del database](../dms/tutorial-sql-server-to-managed-instance.md).
- Per il monitoraggio avanzato delle prestazioni del database dell'istanza gestita con l'Intelligence per la risoluzione dei problemi incorporata, vedere [monitorare il database SQL di Azure con analisi SQL di Azure](../azure-monitor/insights/azure-sql.md).
- Per informazioni sui prezzi, vedere [Prezzi dell'istanza gestita di database SQL](https://azure.microsoft.com/pricing/details/sql-database/managed/).
