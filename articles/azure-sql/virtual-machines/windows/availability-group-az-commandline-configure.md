---
title: Configurare un gruppo di disponibilità (PowerShell & AZ CLI)
description: Usare PowerShell o l'interfaccia della riga di comando di Azure per creare il cluster di failover Windows, il listener del gruppo di disponibilità e il servizio di bilanciamento del carico interno in una macchina virtuale SQL Server in Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/20/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019, devx-track-azurecli
ms.openlocfilehash: a85c1326501a362371d3bc961f5c5ae448e8d22e
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790084"
---
# <a name="use-powershell-or-az-cli-to-configure-an-availability-group-for-sql-server-on-azure-vm"></a>Usare PowerShell o AZ CLI per configurare un gruppo di disponibilità per SQL Server in una macchina virtuale di Azure 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Questo articolo descrive come usare [PowerShell](/powershell/scripting/install/installing-powershell) o l'interfaccia della riga di comando di [Azure](/cli/azure/sql/vm) per distribuire un cluster di failover Windows, aggiungere SQL Server macchine virtuali al cluster e creare il servizio di bilanciamento del carico interno e il listener per un gruppo di disponibilità always on. 

La distribuzione del gruppo di disponibilità viene ancora eseguita manualmente tramite SQL Server Management Studio (SSMS) o Transact-SQL (T-SQL). 

Sebbene questo articolo usi PowerShell e AZ CLI per configurare l'ambiente del gruppo di disponibilità, è anche possibile farlo dalla [portale di Azure](availability-group-azure-portal-configure.md), usando i modelli di [avvio rapido di Azure](availability-group-quickstart-template-configure.md)o [manualmente](availability-group-manually-configure-tutorial.md) . 

## <a name="prerequisites"></a>Prerequisiti

Per configurare un gruppo di disponibilità di Always On, è necessario che siano soddisfatti i prerequisiti seguenti: 

- Una [sottoscrizione di Azure](https://azure.microsoft.com/free/).
- Un gruppo di risorse con un controller di dominio. 
- Una o più macchine virtuali appartenenti a un dominio [in Azure che eseguono SQL Server 2016 (o versione successiva) Enterprise Edition](./create-sql-vm-portal.md) nello *stesso* set di disponibilità o in zone di disponibilità *diverse* [registrate con il provider di risorse della macchina virtuale SQL](sql-vm-resource-provider-register.md).  
- La versione più recente di [PowerShell](/powershell/scripting/install/installing-powershell) o dell'interfaccia della riga di comando di [Azure](/cli/azure/install-azure-cli). 
- Due indirizzi IP disponibili, non usati da alcuna entità. Uno è per il bilanciamento del carico interno, l'altro è per il listener del gruppo di disponibilità all'interno della stessa subnet del gruppo di disponibilità. Se si usa un servizio di bilanciamento del carico esistente, è necessario un solo indirizzo IP disponibile per il listener del gruppo di disponibilità. 

## <a name="permissions"></a>Autorizzazioni

Per configurare il gruppo di disponibilità AlwaysOn usando l'interfaccia della riga di comando di Azure, sono necessarie le autorizzazioni seguenti per l'account: 

- Un account utente di dominio esistente con l'autorizzazione **Create Computer Object** (Crea oggetti computer) nel dominio. Un account amministratore di dominio ha in genere autorizzazioni sufficienti, ad esempio: account@domain.com. _Questo account deve anche fare parte del gruppo degli amministratori locali in ogni macchina virtuale per creare il cluster._
- L'account utente di dominio che controlla SQL Server. 
 
## <a name="create-a-storage-account"></a>Creare un account di archiviazione 

Per il cluster è necessario una account di archiviazione che funga da cloud di controllo. È possibile usare un account di archiviazione esistente o crearne uno nuovo. Per usare un account di archiviazione esistente, passare direttamente alla sezione successiva. 

Il frammento di codice seguente crea un account di archiviazione: 

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

>[!TIP]
> È possibile che venga visualizzato l'errore `az sql: 'vm' is not in the 'az sql' command group` se si usa una versione obsoleta dell'interfaccia della riga di comando di Azure. Scaricare la [versione più recente dell'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli-windows) per risolvere il problema.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Create the storage account
# example: New-AzStorageAccount -ResourceGroupName SQLVM-RG -Name cloudwitness `
#    -SkuName Standard_LRS -Location West US -Kind StorageV2 `
#    -AccessTier Hot -EnableHttpsTrafficOnly

New-AzStorageAccount -ResourceGroupName <resource group name> -Name <name> `
    -SkuName Standard_LRS -Location <region> -Kind StorageV2 `
    -AccessTier Hot -EnableHttpsTrafficOnly
```

---

## <a name="define-cluster-metadata"></a>Definire i metadati del cluster

Il gruppo di comandi dell'interfaccia della riga di comando di Azure [az sql vm group](/cli/azure/sql/vm/group) gestisce i metadati del servizio WSFC (Windows Server Failover Cluster, Cluster di failover di Windows Server) che ospita il gruppo di disponibilità. I metadati del cluster includono il dominio Active Directory, gli account del cluster, gli account di archiviazione da usare come cloud di controllo e la versione di SQL Server. Usare [az sql vm group create](/cli/azure/sql/vm/group#az-sql-vm-group-create) per definire i metadati per WSFC in modo che, quando viene aggiunta la prima VM di SQL Server, il cluster venga creato come definito. 

Il frammento di codice seguente definisce i metadati per il cluster:

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
# Define the cluster metadata
# example: az sql vm group create -n Cluster -l 'West US' -g SQLVM-RG `
#  --image-offer SQL2017-WS2016 --image-sku Enterprise --domain-fqdn domain.com `
#  --operator-acc vmadmin@domain.com --bootstrap-acc vmadmin@domain.com --service-acc sqlservice@domain.com `
#  --sa-key '4Z4/i1Dn8/bpbseyWX' `
#  --storage-account 'https://cloudwitness.blob.core.windows.net/'

az sql vm group create -n <cluster name> -l <region ex:eastus> -g <resource group name> `
  --image-offer <SQL2016-WS2016 or SQL2017-WS2016> --image-sku Enterprise --domain-fqdn <FQDN ex: domain.com> `
  --operator-acc <domain account ex: testop@domain.com> --bootstrap-acc <domain account ex:bootacc@domain.com> `
  --service-acc <service account ex: testservice@domain.com> `
  --sa-key '<PublicKey>' `
  --storage-account '<ex:https://cloudwitness.blob.core.windows.net/>'
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Define the cluster metadata
# example: $group = New-AzSqlVMGroup -Name Cluster -Location West US ' 
#  -ResourceGroupName SQLVM-RG -Offer SQL2017-WS2016
#  -Sku Enterprise -DomainFqdn domain.com -ClusterOperatorAccount vmadmin@domain.com
#  -ClusterBootstrapAccount vmadmin@domain.com  -SqlServiceAccount sqlservice@domain.com 
#  -StorageAccountUrl '<ex:https://cloudwitness.blob.core.windows.net/>' `
#  -StorageAccountPrimaryKey '4Z4/i1Dn8/bpbseyWX'

$group = New-AzSqlVMGroup -Name <name> -Location <regio> 
  -ResourceGroupName <resource group name> -Offer <SQL201?-WS201?> 
  -Sku Enterprise -DomainFqdn <FQDN> -ClusterOperatorAccount <domain account> 
  -ClusterBootstrapAccount <domain account>  -SqlServiceAccount <service account> 
  -StorageAccountUrl '<ex:StorageAccountUrl>' `
  -StorageAccountPrimaryKey '<PublicKey>'
```

---

## <a name="add-vms-to-the-cluster"></a>Aggiungere VM al cluster

L'aggiunta della prima VM di SQL Server al cluster crea il cluster. Il comando [az sql vm add-to-group](/cli/azure/sql/vm#az-sql-vm-add-to-group) crea il cluster con il nome specificato in precedenza, installa il ruolo del cluster nelle VM di SQL Server e lo aggiunge al cluster. Gli utilizzi successivi del comando `az sql vm add-to-group` aggiungono altre VM di SQL Server al cluster appena creato. 

Il frammento di codice seguente crea il cluster e vi aggiunge la prima VM di SQL Server: 

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
# Add SQL Server VMs to cluster
# example: az sql vm add-to-group -n SQLVM1 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!
# example: az sql vm add-to-group -n SQLVM2 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

az sql vm add-to-group -n <VM1 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
az sql vm add-to-group -n <VM2 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
```
Usare questo comando per aggiungere altre VM di SQL Server al cluster. Modificare solo il parametro `-n` per il nome della VM di SQL Server. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Add SQL Server VMs to cluster
# example: $sqlvm1 = Get-AzSqlVM -Name SQLVM1 -ResourceGroupName SQLVM-RG
# $sqlvm2 = Get-AzSqlVM -Name SQLVM2  -ResourceGroupName SQLVM-RG

# $sqlvmconfig1 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm1 `
#  -SqlVMGroup $group -ClusterOperatorAccountPassword Str0ngAzur3P@ssword! `
#  -SqlServiceAccountPassword Str0ngAzur3P@ssword! `
#  -ClusterBootstrapAccountPassword Str0ngAzur3P@ssword!

# $sqlvmconfig2 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm2 `
#  -SqlVMGroup $group -ClusterOperatorAccountPassword Str0ngAzur3P@ssword! `
#  -SqlServiceAccountPassword Str0ngAzur3P@ssword! `
#  - ClusterBootstrapAccountPassword Str0ngAzur3P@ssword!

$sqlvm1 = Get-AzSqlVM -Name <VM1 Name> -ResourceGroupName <Resource Group Name>
$sqlvm2 = Get-AzSqlVM -Name <VM2 Name> -ResourceGroupName <Resource Group Name>

$sqlvmconfig1 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm1 `
   -SqlVMGroup $group -ClusterOperatorAccountPassword <operator account password> `
   -SqlServiceAccountPassword <service account password> `
   -ClusterBootstrapAccountPassword <bootstrap account password>

Update-AzSqlVM -ResourceId $sqlvm1.ResourceId -SqlVM $sqlvmconfig1

$sqlvmconfig2 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm2 `
   -SqlVMGroup $group -ClusterOperatorAccountPassword <operator account password> `
   -SqlServiceAccountPassword <service account password> `
   -ClusterBootstrapAccountPassword <bootstrap account password>

Update-AzSqlVM -ResourceId $sqlvm2.ResourceId -SqlVM $sqlvmconfig2
```

---


## <a name="validate-cluster"></a>Convalida cluster 

Affinché un cluster di failover sia supportato da Microsoft, deve superare la convalida del cluster. Connettersi alla macchina virtuale usando il metodo preferito, ad esempio Remote Desktop Protocol (RDP) e verificare che il cluster superi la convalida prima di procedere. In caso contrario, il cluster rimane in uno stato non supportato. 

È possibile convalidare il cluster usando Gestione cluster di failover (FCM) o il comando di PowerShell seguente:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

## <a name="create-availability-group"></a>Creare un gruppo di disponibilità

Creare manualmente il gruppo di disponibilità come di consueto, usando [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell) o [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

>[!IMPORTANT]
> *Non* creare un listener in questo momento perché questa operazione viene eseguita tramite l'interfaccia della riga di comando di Azure nelle sezioni seguenti.  

## <a name="create-internal-load-balancer"></a>Creare un servizio di bilanciamento del carico interno

[!INCLUDE [sql-ag-use-dnn-listener](../../includes/sql-ag-use-dnn-listener.md)]

Il listener del gruppo di disponibilità AlwaysOn richiede un'istanza interna di Azure Load Balancer. Il bilanciamento del carico interno specifica un indirizzo IP "mobile" per il listener del gruppo di disponibilità che consente la riconnessione e il failover più rapidi. Se le VM di SQL Server in un gruppo di disponibilità fanno parte dello stesso set di disponibilità, è possibile usare Load Balancer Basic. In caso contrario, è necessario usare Load Balancer Standard.  

> [!NOTE]
> Il bilanciamento del carico interno deve trovarsi nella stessa rete virtuale delle istanze della VM di SQL Server. 

Il frammento di codice seguente crea un bilanciamento del carico interno:

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
# Create the internal load balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Create the internal load balancer
# example: New-AzLoadBalancer -name sqlILB -ResourceGroupName SQLVM-RG  `
#  -Sku Standard -Location West US

New-AzLoadBalancer -name sqlILB -ResourceGroupName <resource group name> `
   -Sku Standard -Location <region>
```

---

>[!IMPORTANT]
> La risorsa IP pubblico per ogni VM di SQL Server deve avere uno SKU Standard per essere compatibile con Load Balancer Standard. Per determinare lo SKU della risorsa IP pubblico della VM, andare a **Gruppo di risorse** , selezionare la risorsa **Indirizzo IP pubblico** per la VM di SQL Server e individuare il valore in **SKU**  nel riquadro **Panoramica** .  

## <a name="create-listener"></a>Creare un listener

Dopo aver creato manualmente il gruppo di disponibilità, è possibile creare il listener usando [az sql vm ag-listener](/cli/azure/sql/vm/group/ag-listener#az-sql-vm-group-ag-listener-create). 

L' *ID di risorsa della subnet* è il valore di `/subnets/<subnetname>` accodato all'ID risorsa della risorsa di rete virtuale. Per identificare l'ID risorsa della subnet:
   1. Passare al gruppo di risorse nel [portale di Azure](https://portal.azure.com). 
   1. Selezionare la risorsa di rete virtuale. 
   1. Selezionare **Proprietà** nel riquadro **Impostazioni** . 
   1. Identificare l'ID risorsa per la rete virtuale e accodarvi `/subnets/<subnetname>` per creare l'ID della risorsa della subnet. Ad esempio:
      - L'ID della risorsa di rete virtuale è: `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
      - Il nome della subnet è: `default`
      - Pertanto l'ID della risorsa della subnet è: `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


Il frammento di codice seguente crea il listener del gruppo di disponibilità:

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
# Create the availability group listener
# example: az sql vm group ag-listener create -n AGListener -g SQLVM-RG `
#  --ag-name SQLAG --group-name Cluster --ip-address 10.0.0.27 `
#  --load-balancer sqlilb --probe-port 59999  `
#  --subnet /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#  --sqlvms sqlvm1 sqlvm2

az sql vm group ag-listener create -n <listener name> -g <resource group name> `
  --ag-name <availability group name> --group-name <cluster name> --ip-address <ag listener IP address> `
  --load-balancer <lbname> --probe-port <Load Balancer probe port, default 59999>  `
  --subnet <subnet resource id> `
  --sqlvms <names of SQL VM's hosting AG replicas, ex: sqlvm1 sqlvm2>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive

# example: New-AzAvailabilityGroupListener -Name AGListener -ResourceGroupName SQLVM-RG `
#    -AvailabilityGroupName SQLAG  -GroupName Cluster `
#    -IpAddress 10.0.0.27 -LoadBalancerResourceId sqlilb `
#    -ProbePort 59999 `
#    -SubnetId /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#    -SqlVirtualMachineId sqlvm1 sqlvm2


New-AzAvailabilityGroupListener -Name <listener name> -ResourceGroupName <resource group name> `
   -AvailabilityGroupName <availability group name> -GroupName <cluster name> `
   -IpAddress <ag listener IP address> -LoadBalancerResourceId <lbid> `
   -ProbePort <Load Balancer probe port, default 59999> `
   -SubnetId <subnet resource id> `
   -SqlVirtualMachineId <names of SQL VM's hosting AG replicas>
```

---

## <a name="modify-number-of-replicas"></a>Modificare il numero di repliche 
Quando si distribuisce un gruppo di disponibilità in VM di SQL Server ospitate in Azure, è presente un livello di complessità in più. Il provider di risorse e il gruppo di macchine virtuali ora gestiscono le risorse. Di conseguenza, quando si aggiungono o si rimuovono le repliche nel gruppo di disponibilità, vi è un altro passaggio che consiste nell'aggiornamento dei metadati del listener con le informazioni sulle VM di SQL Server. Quando si modifica il numero di repliche nel gruppo di disponibilità, è necessario usare anche il comando [az sql vm group ag-listener update](/cli/azure/sql/vm/group/ag-listener#az-sql-vm-group-ag-listener-update) per aggiornare il listener con i metadati delle VM di SQL Server. 


### <a name="add-a-replica"></a>Aggiungere una replica

Per aggiungere una nuova replica al gruppo di disponibilità:

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

1. Aggiungere la macchina virtuale SQL Server al gruppo cluster:
   ```azurecli-interactive

   # Add the SQL Server VM to the cluster group
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```

1. Usare SQL Server Management Studio per aggiungere l'istanza di SQL Server come replica all'interno del gruppo di disponibilità.
1. Aggiungere i metadati della VM di SQL Server al listener:

   ```azurecli-interactive
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Aggiungere la macchina virtuale SQL Server al gruppo cluster:

   ```powershell-interactive
   # Add the SQL Server VM to the cluster group
   # example: $sqlvm3 = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG 
   # $group = Get-AzSqlVMGroup -ResourceGroupName SQLVM-RG -Name Cluster
   # $sqlvmconfig3 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm3 -SqlVMGroup $group `
   #  -ClusterOperatorAccountPassword Str0ngAzur3P@ssword! `
   #  -SqlServiceAccountPassword Str0ngAzur3P@ssword! `
   #  -ClusterBootstrapAccountPassword Str0ngAzur3P@ssword!

   $sqlvm3 = Get-AzSqlVM -Name <VM1 Name> -ResourceGroupName <Resource Group Name>
   $group = Get-AzSqlVMGroup  -ResourceGroupName <resource group name> -Name <name>
   $sqlvmconfig3 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm3 -SqlVMGroup $group `
   -ClusterOperatorAccountPassword <operator account password> `
   -SqlServiceAccountPassword <service account password> `
   -ClusterBootstrapAccountPassword <bootstrap account password>

   Update-AzSqlVM -ResourceId $sqlvm3.ResourceId -SqlVM $sqlvmconfig3
   ```

1. Usare SQL Server Management Studio per aggiungere l'istanza di SQL Server come replica all'interno del gruppo di disponibilità.
1. Aggiungere i metadati della VM di SQL Server al listener:

   ```powershell-interactive
   # Update the listener metadata with the new VM
   # example: Update-AzAvailabilityGroupListener -Name AGListener -ResourceGroupName SQLVM-RG `
   #   -SqlVMGroupName Cluster -SqlVirtualMachineId SQLVM3

   Update-AzAvailabilityGroupListener -Name <Listener> -ResourceGroupName <Resource Group Name> `
      -SqlVMGroupName <cluster name> -SqlVirtualMachineId <SQL VMs, along with new SQL VM> 

   ```

---

### <a name="remove-a-replica"></a>Rimozione di una replica

Per rimuovere una replica dal gruppo di disponibilità:

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

1. Rimuovere la replica dal gruppo di disponibilità usando SQL Server Management Studio. 
1. Rimuovere i metadati della VM di SQL Server dal listener:
   ```azurecli-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs that remain>
   ```
1. Rimuovere la VM di SQL Server dal cluster:
   ```azurecli-interactive
   # Remove the SQL VM from the cluster
   # example: az sql vm remove-from-group --name SQLVM3 --resource-group SQLVM-RG

   az sql vm remove-from-group --name <SQL VM name> --resource-group <RG name> 
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Rimuovere la replica dal gruppo di disponibilità usando SQL Server Management Studio. 
1. Rimuovere i metadati della VM di SQL Server dal listener:

   ```powershell-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: Update-AzAvailabilityGroupListener -Name AGListener -ResourceGroupName SQLVM-RG `
   #  -SqlVMGroupName Cluster -SqlVirtualMachineId SQLVM3

   Update-AzAvailabilityGroupListener -Name <Listener> -ResourceGroupName <Resource Group Name> `
      -SqlVMGroupName <cluster name> -SqlVirtualMachineId <SQL VMs that remain>

   ```
1. Rimuovere la VM di SQL Server dal cluster:

   ```powershell-interactive
   # Remove the SQL VM from the cluster
   # example: $sqlvm = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG
   #  $sqlvm. SqlVirtualMachineGroup = ""
   #  Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm

   $sqlvm = Get-AzSqlVM -Name <VM Name> -ResourceGroupName <Resource Group Name>
      $sqlvm. SqlVirtualMachineGroup = ""
    
    Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm
   ```

---

## <a name="remove-listener"></a>Rimuovere un listener
Se in un secondo momento è necessario rimuovere il listener del gruppo di disponibilità configurato con l'interfaccia della riga di comando di Azure, è necessario passare dal provider di risorse di VM di SQL. Poiché il listener è stato registrato con il provider di risorse di VM di SQL, eliminarlo semplicemente tramite SQL Server Management Studio non è sufficiente. 

Il metodo migliore consiste nell'eliminarlo con il provider di risorse delle VM di SQL, usando il frammento di codice seguente nell'interfaccia della riga di comando di Azure. Questa operazione rimuove i metadati del listener del gruppo di disponibilità dal provider di risorse di macchine virtuali di SQL ed elimina fisicamente il listener dal gruppo di disponibilità. 

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
# Remove the availability group listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Remove the availability group listener
# example: Remove-AzAvailabilityGroupListener -Name AGListener `
#   -ResourceGroupName SQLVM-RG -SqlVMGroupName Cluster

Remove-AzAvailabilityGroupListener -Name <Listener> `
   -ResourceGroupName <Resource Group Name> -SqlVMGroupName <cluster name>
```

---

## <a name="remove-cluster"></a>Rimuovi cluster

Rimuovere tutti i nodi dal cluster per eliminarli definitivamente, quindi rimuovere i metadati del cluster dal provider di risorse della macchina virtuale SQL. A tale scopo, è possibile usare l'interfaccia della riga di comando di Azure o PowerShell. 


# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Rimuovere innanzitutto tutte le macchine virtuali SQL Server dal cluster: 

```azurecli-interactive
# Remove the VM from the cluster metadata
# example: az sql vm remove-from-group --name SQLVM2 --resource-group SQLVM-RG

az sql vm remove-from-group --name <VM1 name>  --resource-group <resource group name>
az sql vm remove-from-group --name <VM2 name>  --resource-group <resource group name>
```

Se queste sono le uniche macchine virtuali nel cluster, il cluster verrà eliminato definitivamente. Se nel cluster sono presenti altre macchine virtuali separate dalle macchine virtuali SQL Server che sono state rimosse, le altre macchine virtuali non verranno rimosse e il cluster non verrà eliminato definitivamente. 

Rimuovere quindi i metadati del cluster dal provider di risorse VM SQL: 

```azurecli-interactive
# Remove the cluster from the SQL VM RP metadata
# example: az sql vm group delete --name Cluster --resource-group SQLVM-RG

az sql vm group delete --name <cluster name> Cluster --resource-group <resource group name>
```



# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Rimuovere innanzitutto tutte le macchine virtuali SQL Server dal cluster. I nodi vengono rimossi fisicamente dal cluster ed eliminati definitivamente dal cluster: 

```powershell-interactive
# Remove the SQL VM from the cluster
# example: $sqlvm = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG
#  $sqlvm. SqlVirtualMachineGroup = ""
#  Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm

$sqlvm = Get-AzSqlVM -Name <VM Name> -ResourceGroupName <Resource Group Name>
   $sqlvm. SqlVirtualMachineGroup = ""
   
   Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm
```

Se queste sono le uniche macchine virtuali nel cluster, il cluster verrà eliminato definitivamente. Se nel cluster sono presenti altre macchine virtuali separate dalle macchine virtuali SQL Server che sono state rimosse, le altre macchine virtuali non verranno rimosse e il cluster non verrà eliminato definitivamente. 

Rimuovere quindi i metadati del cluster dal provider di risorse VM SQL: 

```powershell-interactive
# Remove the cluster metadata
# example: Remove-AzSqlVMGroup -ResourceGroupName "SQLVM-RG" -Name "Cluster"

Remove-AzSqlVMGroup -ResourceGroupName "<resource group name>" -Name "<cluster name> "
```

---

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti: 

* [Panoramica delle VM di SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Domande frequenti sulle VM di SQL Server](frequently-asked-questions-faq.md)
* [Note sulla versione delle VM di SQL Server](../../database/doc-changes-updates-release-notes.md)
* [Come cambiare il modello di licenza per una macchina virtuale SQL Server in Azure](licensing-model-azure-hybrid-benefit-ahb-change.md)
* [Panoramica dei gruppi di disponibilità AlwaysOn &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [Configurazione di un'istanza del server per i gruppi di disponibilità AlwaysOn &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [Amministrazione di un gruppo di disponibilità &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Monitoraggio dei gruppi di disponibilità &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Panoramica delle istruzioni Transact-SQL per i gruppi di disponibilità AlwaysOn &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Panoramica dei cmdlet di PowerShell per i gruppi di disponibilità AlwaysOn &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)