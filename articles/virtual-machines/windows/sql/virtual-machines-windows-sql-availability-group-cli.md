---
title: Configurare un gruppo di disponibilità (interfaccia della riga di comando di Azure)Configure an availability group (Azure CLI)
description: Usare l'interfaccia della riga di comando di Azure per creare il cluster di failover di Windows, il listener del gruppo di disponibilità e il servizio di bilanciamento del carico interno in una macchina virtuale di SQL Server in Azure.Use the Azure CLI to create the Windows failover cluster, the availability group listener, and the internal load balancer on a SQL Server VM in Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: a6600af353daf2bfa7b49196f48ba5b60e6c45fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022356"
---
# <a name="use-the-azure-cli-to-configure-an-always-on-availability-group-for-sql-server-on-an-azure-vm"></a>Usare l'interfaccia della riga di comando di Azure per configurare un gruppo di disponibilità AlwaysOn Always On per SQL Server in una macchina virtuale di AzureUse the Azure CLI to configure an Always On availability group for SQL Server on an Azure VM
Questo articolo descrive come usare [l'interfaccia della riga](/cli/azure/sql/vm?view=azure-cli-latest/) di comando di Azure per distribuire un cluster di failover di Windows, aggiungere macchine virtuali di SQL Server al cluster e creare il servizio di bilanciamento del carico interno e il listener per un gruppo di disponibilità AlwaysOn.This article describes how to use the Azure CLI to deploy a Windows failover cluster, add SQL Server VMs to the cluster, and create the internal load balancer and listener for an Always On availability group. La distribuzione del gruppo di disponibilità AlwaysOnAlways On availability group viene comunque eseguita manualmente tramite SQL Server Management Studio (SSMS). 

## <a name="prerequisites"></a>Prerequisiti
Per automatizzare la configurazione di un gruppo di disponibilità AlwaysOn tramite l'interfaccia della riga di comando di Azure, è necessario disporre dei prerequisiti seguenti:To automate the setup of an Always On availability group by using the Azure CLI, you must have the following prerequisites: 
- Una [sottoscrizione di Azure.](https://azure.microsoft.com/free/)
- Un gruppo di risorse con un controller di dominio. 
- Una o più macchine virtuali aggiunte al dominio [in Azure che eseguono SQL Server 2016 (o versione successiva) Enterprise Edition](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) nello stesso set di disponibilità o in zone di disponibilità *diverse* registrate con il provider di risorse della macchina [virtuale SQL.](virtual-machines-windows-sql-register-with-resource-provider.md)  
- [L'interfaccia della riga di comando](/cli/azure/install-azure-cli)di Azure . 
- Due indirizzi IP disponibili (non utilizzati da alcuna entità). Uno è per il servizio di bilanciamento del carico interno. L'altro è per il listener del gruppo di disponibilità all'interno della stessa subnet del gruppo di disponibilità. Se si usa un servizio di bilanciamento del carico esistente, è necessario un solo indirizzo IP disponibile per il listener del gruppo di disponibilità. 

## <a name="permissions"></a>Autorizzazioni
Sono necessarie le autorizzazioni dell'account seguente per configurare il gruppo di disponibilità AlwaysOn usando l'interfaccia della riga di comando di Azure:You need the following account permissions to configure the Always On availability group by using the Azure CLI: 

- Un account utente di dominio esistente che dispone dell'autorizzazione **Crea oggetto computer** nel dominio. Ad esempio, un account amministratore di dominio account@domain.comdispone in genere di autorizzazioni sufficienti (ad esempio: ). _Questo account deve anche fare parte del gruppo degli amministratori locali in ogni macchina virtuale per creare il cluster._
- Account utente di dominio che controlla il servizio SQL Server. 
 
## <a name="step-1-create-a-storage-account-as-a-cloud-witness"></a>Passaggio 1: Creare un account di archiviazione come cloud di controlloStep 1: Create a storage account as a cloud witness
Il cluster necessita di un account di archiviazione che funga da cloud di controllo. È possibile usare qualsiasi account di archiviazione esistente oppure creare un nuovo account di archiviazione. Se si vuole usare un account di archiviazione esistente, passare alla sezione successiva. 

Il frammento di codice seguente crea l'account di archiviazione:The following code snippet creates the storage account: 
```azurecli-interactive
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region ex:eastus> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

>[!TIP]
> L'errore potrebbe `az sql: 'vm' is not in the 'az sql' command group` essere visualizzato se si usa una versione obsoleta dell'interfaccia della riga di comando di Azure.You might see the error if you're using an obsoleted version of the Azure CLI. Scaricare la [versione più recente dell'interfaccia della riga di](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) comando di Azure per superare questo errore.

## <a name="step-2-define-windows-failover-cluster-metadata"></a>Passaggio 2: Definire i metadati del cluster di failover di WindowsStep 2: Define Windows failover cluster metadata
Il gruppo di comandi del gruppo di [macchine virtuali sql](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest) dell'interfaccia della riga di comando di Azure gestisce i metadati del servizio WsFC (Windows Server Failover Cluster) che ospita il gruppo di disponibilità. I metadati del cluster includono il dominio di Active Directory, gli account del cluster, gli account di archiviazione da usare come cloud di controllo e la versione di SQL Server.Cluster metadata includes the Active Directory domain, cluster accounts, storage accounts to be used as the cloud witness, and SQL Server version. Usare [az sql vm group create](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest#az-sql-vm-group-create) per definire i metadati per WSFC in modo che quando viene aggiunta la prima macchina virtuale di SQL Server, il cluster venga creato come definito. 

Il frammento di codice seguente definisce i metadati per il cluster:The following code snippet defines the metadata for the cluster:
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

## <a name="step-3-add-sql-server-vms-to-the-cluster"></a>Passaggio 3: Aggiungere macchine virtuali di SQL Server al clusterStep 3: Add SQL Server VMs to the cluster
L'aggiunta della prima macchina virtuale di SQL Server al cluster crea il cluster. Il comando [az sql vm add-to-group](https://docs.microsoft.com/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-add-to-group) crea il cluster con il nome indicato in precedenza, installa il ruolo del cluster nelle macchine virtuali di SQL Server e le aggiunge al cluster. Gli utilizzi `az sql vm add-to-group` successivi del comando aggiungono altre macchine virtuali di SQL Server al cluster appena creato. 

Il frammento di codice seguente crea il cluster e vi aggiunge la prima macchina virtuale di SQL Server:The following code snippet creates the cluster and adds the first SQL Server VM to it: 

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
Usare questo comando per aggiungere altre macchine virtuali di SQL Server al cluster. Modificare solo `-n` il parametro per il nome della macchina virtuale di SQL Server.Modify only the parameter for the SQL Server VM name. 

## <a name="step-4-create-the-availability-group"></a>Passaggio 4: Creare il gruppo di disponibilitàStep 4: Create the availability group
Creare manualmente il gruppo di disponibilità come di consueto, utilizzando [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)o [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

>[!IMPORTANT]
> *Non* creare un listener in questo momento perché questa operazione viene eseguita tramite l'interfaccia della riga di comando di Azure nelle sezioni seguenti.  

## <a name="step-5-create-the-internal-load-balancer"></a>Passaggio 5: Creare il servizio di bilanciamento del carico internoStep 5: Create the internal load balancer

Il listener del gruppo di disponibilità AlwaysOnAlways On availability group listener richiede un'istanza interna di Azure Load Balancer.The Always On availability group listener requires an internal instance of Azure Load Balancer. Il servizio di bilanciamento del carico interno fornisce un indirizzo IP "mobile" per il listener del gruppo di disponibilità che consente un failover e una riconnessione più rapidi. Se le macchine virtuali di SQL Server in un gruppo di disponibilità fanno parte dello stesso set di disponibilità, è possibile usare un servizio di bilanciamento del carico di base. In caso contrario, è necessario usare un servizio di bilanciamento del carico Standard.Otherwise, you need to use a Standard load balancer.  

> [!NOTE]
> Il servizio di bilanciamento del carico interno deve trovarsi nella stessa rete virtuale delle istanze della macchina virtuale di SQL Server.The internal load balancer should be in the same virtual network as the SQL Server VM instances. 

Il frammento di codice seguente crea il servizio di bilanciamento del carico interno:The following code snippet creates the internal load balancer:

```azurecli-interactive
# Create the internal load balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

>[!IMPORTANT]
> La risorsa IP pubblica per ogni macchina virtuale di SQL Serversql Server deve avere uno SKU Standard per essere compatibile con il servizio di bilanciamento del carico Standard.The public IP resource for each SQL Server VM should have a Standard SKU to be compatible with the Standard load balancer. Per determinare lo SKU della risorsa IP pubblica della macchina virtuale, passare a **Gruppo di risorse**, selezionare la risorsa Indirizzo IP **pubblico** per la macchina virtuale di SQL Server desiderata e individuare il valore in **SKU** nel riquadro **Panoramica.**  

## <a name="step-6-create-the-availability-group-listener"></a>Passaggio 6: Creare il listener del gruppo di disponibilitàStep 6: Create the availability group listener
Dopo aver creato manualmente il gruppo di disponibilità, è possibile creare il listener utilizzando [az sql vm ag-listener](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-latest#az-sql-vm-group-ag-listener-create). 

*L'ID risorsa subnet* `/subnets/<subnetname>` è il valore di aggiunto all'ID risorsa della risorsa di rete virtuale. Per identificare l'ID risorsa subnet:
   1. Passare al gruppo di risorse nel portale di [Azure.](https://portal.azure.com) 
   1. Selezionare la risorsa di rete virtuale. 
   1. Selezionare Proprietà nel riquadro **Impostazioni.Select** **Properties** in the Settings pane. 
   1. Identificare l'ID risorsa per `/subnets/<subnetname>` la rete virtuale e aggiungerlo alla fine per creare l'ID risorsa subnet. Ad esempio:
      - L'ID della risorsa di rete virtuale è:Your virtual network resource ID is:`/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
      - Il nome della subnet è:Your subnet name is:`default`
      - Pertanto, l'ID della risorsa subnet è:Therefore, your subnet resource ID is:`/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


Il frammento di codice seguente crea il listener del gruppo di disponibilità:The following code snippet creates the availability group listener:

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

## <a name="modify-the-number-of-replicas-in-an-availability-group"></a>Modificare il numero di repliche in un gruppo di disponibilitàModify the number of replicas in an availability group
È presente un ulteriore livello di complessità quando si distribuisce un gruppo di disponibilità alle macchine virtuali di SQL Server ospitate in Azure.There's an added layer of complexity when you're deploying an availability group to SQL Server VMs hosted in Azure. Il provider di risorse e il gruppo di macchine virtuali ora gestiscono le risorse. Di conseguenza, quando si aggiungono o rimuovono repliche nel gruppo di disponibilità, è disponibile un passaggio aggiuntivo per aggiornare i metadati del listener con informazioni sulle macchine virtuali di SQL Server.As such, when you're adding or removing replicas in the availability group, there's an additional step of updating the listener metadata with information about the SQL Server VMs. Quando si modifica il numero di repliche nel gruppo di disponibilità, è necessario usare anche il comando [az sql vm group ag-listener update](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-2018-03-01-hybrid#az-sql-vm-group-ag-listener-update) per aggiornare il listener con i metadati delle macchine virtuali di SQL Server. 


### <a name="add-a-replica"></a>Aggiungere una replica

Per aggiungere una nuova replica al gruppo di disponibilità:To add a new replica to the availability group:

1. Aggiungere la macchina virtuale di SQL Server al cluster:Add the SQL Server VM to the cluster:
   ```azurecli-interactive
   # Add the SQL Server VM to the cluster
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```
1. Usare SQL Server Management StudioSQL Server Management Studio per aggiungere l'istanza di SQL ServerSQL Server come replica all'interno del gruppo di disponibilità.
1. Aggiungere i metadati della macchina virtuale di SQL Server al listener:Add the SQL Server VM metadata to the listener:
   ```azurecli-interactive
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

### <a name="remove-a-replica"></a>Rimozione di una replica

Per rimuovere una replica dal gruppo di disponibilità:To remove a replica from the availability group:

1. Rimuovere la replica dal gruppo di disponibilità tramite SQL Server Management Studio.Remove the replica from the availability group by using SQL Server Management StudioSQL Server Management Studio. 
1. Rimuovere i metadati della macchina virtuale di SQL Server dal listener:Remove the SQL Server VM metadata from the listener:
   ```azurecli-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs that remain>
   ```
1. Rimuovere la macchina virtuale di SQL Server dal cluster:Remove the SQL Server VM from the cluster:
   ```azurecli-interactive
   # Remove the SQL VM from the cluster
   # example: az sql vm remove-from-group --name SQLVM3 --resource-group SQLVM-RG

   az sql vm remove-from-group --name <SQL VM name> --resource-group <RG name> 
   ```

## <a name="remove-the-availability-group-listener"></a>Rimuovere il listener del gruppo di disponibilitàRemove the availability group listener
Se in seguito è necessario rimuovere il listener del gruppo di disponibilità configurato con l'interfaccia della riga di comando di Azure, è necessario esaminare il provider di risorse della macchina virtuale SQL. Poiché il listener viene registrato tramite il provider di risorse della macchina virtuale SQL, è sufficiente eliminarlo tramite SQL Server Management StudioSQL Server Management Studio.Because the listener is registered through the SQL VM resource provider, just deleting it via SQL Server Management StudioSQL Server Management Studio is insufficient. 

The best method is to delete it through the SQL VM resource provider by using the following code snippet in the Azure CLI. In questo modo vengono rimossi i metadati del listener del gruppo di disponibilità dal provider di risorse della macchina virtuale SQL. Elimina inoltre fisicamente il listener dal gruppo di disponibilità. 

```azurecli-interactive
# Remove the availability group listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti: 

* [Panoramica delle macchine virtuali di SQL ServerOverview of SQL Server VMs](virtual-machines-windows-sql-server-iaas-overview.md)
* [Domande frequenti sulle macchine virtuali di SQL ServerFAQ for SQL Server VMs](virtual-machines-windows-sql-server-iaas-faq.md)
* [Note sulla versione per le macchine virtuali di SQL Server](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [Come cambiare il modello di licenza per una macchina virtuale SQL Server in Azure](virtual-machines-windows-sql-ahb.md)
* [Panoramica dei gruppi di disponibilità AlwaysOnAlways On availability groups &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [Configurazione di un'istanza del server per i gruppi di disponibilità AlwaysOnAlways On availability groups &#40;SQL&#41;Server](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [Amministrazione di un gruppo di disponibilità &#40;SQL ServerSQL Server sql Serversql Server&#41;](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Monitoraggio dei gruppi di disponibilità &#40;SQL ServerSQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Panoramica delle istruzioni Transact-SQLTransact-SQL per i gruppi di disponibilità AlwaysOnAlways On availability groups &#40;SQL ServerSQL Server&#41;](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Panoramica dei cmdlet di PowerShell per i gruppi di disponibilità AlwaysOnAlways On Availability groups &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  
