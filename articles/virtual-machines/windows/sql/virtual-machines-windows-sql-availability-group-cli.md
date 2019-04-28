---
title: Usare VM di SQL Azure CLI per configurare gruppi di disponibilità AlwaysOn per SQL Server in una VM di Azure
description: 'Usare il comando di Azure per creare il Cluster di Failover di Windows, il listener del gruppo di disponibilità e bilanciamento del carico interno in una VM di SQL Server in Azure. '
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 1c5c5f4c8125f801edc89d47851871d8eb06a2f9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60591282"
---
# <a name="use-azure-sql-vm-cli-to-configure-always-on-availability-group-for-sql-server-on-an-azure-vm"></a>Usare VM di SQL Azure CLI per configurare gruppi di disponibilità AlwaysOn per SQL Server in una VM di Azure
Questo articolo descrive come usare [macchina virtuale di SQL Azure CLI](/cli/azure/sql/vm?view=azure-cli-latest/) per distribuire un Cluster di Failover di Windows (WSFC) e aggiungere le macchine virtuali di SQL Server al cluster, nonché per creare il bilanciamento del carico interno e il listener per un gruppo di disponibilità Always On.  La distribuzione effettiva del gruppo di disponibilità Always On viene comunque eseguita manualmente tramite SQL Server Management Studio (SSMS). 

## <a name="prerequisites"></a>Prerequisiti
Per automatizzare l'installazione di un gruppo disponibilità Always On tramite CLI della macchina virtuale di Azure SQL, è necessario disporre già i prerequisiti seguenti: 
- Una [sottoscrizione di Azure](https://azure.microsoft.com/free/).
- Un gruppo di risorse con un controller di dominio. 
- Uno o più aggiunti al dominio [macchine virtuali in Azure in esecuzione SQL Server 2016 (o superiore) Enterprise edition](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) nel *stesso set di disponibilità o le zone di disponibilità diverso* che sono stati [registrato con il provider di risorse di VM di SQL](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider).  
- [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). 
- (Non usato da qualsiasi entità) disponibili due indirizzi IP, uno per il bilanciamento del carico interno e uno per il listener del gruppo di disponibilità all'interno della stessa subnet del gruppo di disponibilità. Se viene usato un bilanciamento del carico esistente, è necessario un solo indirizzo IP disponibile per il listener del gruppo di disponibilità. 

## <a name="permissions"></a>Autorizzazioni
Le autorizzazioni di account seguenti sono necessari per configurare il gruppo disponibilità Always On usando VM di SQL Azure CLI. 

- Un dominio account utente che dispone dell'autorizzazione 'Crea Computer oggetto' nel dominio.  Ad esempio, un account amministratore di dominio ha in genere autorizzazioni sufficienti (ad esempio: account@domain.com). _Questo account deve anche fare parte del gruppo degli amministratori locali in ogni macchina virtuale per creare il cluster._
- L'account utente di dominio che controlla il servizio SQL Server. 
 
## <a name="step-1---create-storage-account-as-a-cloud-witness"></a>Passaggio 1: creare account di archiviazione come un cloud di controllo
Il cluster è necessario un account di archiviazione da usare come il cloud di controllo. È possibile usare qualsiasi account di archiviazione esistente oppure è possibile creare un nuovo account di archiviazione. Se si desidera usare un account di archiviazione esistente, passare direttamente alla sezione successiva. 

Il frammento di codice seguente crea l'account di archiviazione: 
```azurecli
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region ex:eastus> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

   >[!TIP]
   > Viene inoltre visualizzato l'errore `az sql: 'vm' is not in the 'az sql' command group` se si usa una versione obsoleta del comando di Azure. Scaricare il [versione più recente di Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) per risolvere questo errore.

## <a name="step-2---define-windows-failover-cluster-metadata"></a>Passaggio 2: definire i metadati del Cluster di Failover Windows
La macchina virtuale di SQL Azure CLI [gruppo di macchine virtuali sql az](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest) comando gruppo gestisce i metadati del servizio Windows Failover Cluster (WSFC) che ospita il gruppo di disponibilità. I metadati del cluster sono inclusi il dominio di Active Directory, account del cluster, gli account di archiviazione da usare come il cloud di controllo e la versione di SQL Server. Uso [creare il gruppo di macchine virtuali di az sql](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest#az-sql-vm-group-create) per definire i metadati per il cluster WSFC in modo che quando viene aggiunta la prima VM SQL Server, viene creato il cluster, come definito. 

Il frammento di codice seguente definisce i metadati per il cluster:
```azurecli
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

## <a name="step-3---add-sql-server-vms-to-cluster"></a>Passaggio 3 - aggiungere VM di SQL Server in cluster
Aggiungere la prima VM SQL Server al cluster crea il cluster. Il [az sql vm aggiunta a gruppo](https://docs.microsoft.com/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-add-to-group) comando crea il cluster con il nome specificato in precedenza, installa il ruolo del cluster in macchine virtuali di SQL Server e li aggiunge al cluster. Gli utilizzi successivi del `az sql vm add-to-group` comando consente di aggiungere altre macchine virtuali di SQL Server per il cluster appena creato. 

Il frammento di codice seguente crea il cluster e aggiungervi la prima VM SQL Server: 

```azurecli
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
Usare questo comando per aggiungere eventuali altre macchine virtuali SQL Server al cluster, solo la modifica di `-n` parametro per il nome di VM di SQL Server. 

## <a name="step-4---create-availability-group"></a>Passaggio 4: creare il gruppo di disponibilità
Creare manualmente il gruppo di disponibilità come di consueto, usando [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell), o [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

  >[!IMPORTANT]
  > Effettuare **non** creare un listener in questo momento perché questa operazione viene eseguita tramite la CLI di Azure nelle sezioni seguenti.  

## <a name="step-5---create-internal-load-balancer"></a>Passaggio 5: creare il bilanciamento del carico interno

Always On listener gruppo di disponibilità (AG) richiede un interno Azure (bilanciamento del carico). L'ILB fornisce un indirizzo IP mobile per il listener del gruppo di disponibilità che consente il failover e la riconnessione più veloci. Se le VM di SQL Server in un gruppo di disponibilità fanno parte dello stesso set di disponibilità, è possibile usare un servizio Load Balancer Basic. In caso contrario, è necessario usare un Load Balancer Standard.  **L'ILB deve trovarsi nella stessa rete virtuale delle istanze di VM di SQL Server.** 

Il frammento di codice seguente consente di creare il bilanciamento del carico interno:

```azurecli
# Create the Internal Load Balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

  >[!IMPORTANT]
  > La risorsa IP pubblico per ogni VM di SQL Server deve avere uno SKU standard per essere compatibile con il Load Balancer Standard. Per determinare lo SKU della risorsa IP pubblico della VM, passare a **Gruppo di risorse**, selezionare la risorsa **Indirizzo IP pubblico** per la VM di SQL Server desiderata e individuare il valore sotto **SKU**  nel pannello **Panoramica**.  

## <a name="step-6---create-availability-group-listener"></a>Passaggio 6: creare listener del gruppo di disponibilità
Dopo aver creato manualmente il gruppo di disponibilità, è possibile creare il listener usando [az sql vm listener gruppo di disponibilità-](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-latest#az-sql-vm-group-ag-listener-create). 


- Il **ID risorsa della subnet** JE hodnotou `/subnets/<subnetname>` aggiunto all'ID risorsa della risorsa della rete virtuale. Per identificare l'ID risorsa della subnet, eseguire le operazioni seguenti:
   1. Passare al gruppo di risorse nel [portale di Azure](https://portal.azure.com). 
   1. Selezionare la risorsa rete virtuale. 
   1. Selezionare **delle proprietà** nel **impostazioni** riquadro. 
   1. Identificare l'ID risorsa per la rete virtuale e aggiungere `/subnets/<subnetname>`alla fine del codice per creare l'ID di risorsa subnet. Ad esempio: 
        - L'ID della risorsa della rete virtuale è: `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
        - Il nome della subnet è `default`.
        - Pertanto, l'ID risorsa della subnet è: `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


Il frammento di codice seguente verrà creato il listener del gruppo di disponibilità:

```azurecli
# Create the AG listener
# example: az sql vm group ag-listener create -n AGListener -g SQLVM-RG `
#  --ag-name SQLAG --group-name Cluster --ip-address 10.0.0.27 `
#  --load-balancer sqlilb --probe-port 59999  `
#  --subnet /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#  --sqlvms sqlvm1 sqlvm2

az sql vm group ag-listener create -n <listener name> -g <resource group name> `
  --ag-name <availability group name> --group-name <cluster name> --ip-address <ag listener IP address> `
  --load-balancer <lbname> --probe-port <Load Balancer probe port, default 59999>  `
  --subnet <subnet resource id> `
  --sqlvms <names of SQL VM's hosting AG replicas ex: sqlvm1 sqlvm2>
```

## <a name="modify-number-of-replicas-in-availability-group"></a>Modifica numero di repliche nel gruppo di disponibilità
È presente un ulteriore livello di complessità durante la distribuzione di un gruppo di disponibilità per macchine virtuali di SQL Server ospitato in Azure, come le risorse vengono ora gestite dal provider di risorse e dal `virtual machine group`. Di conseguenza, durante l'aggiunta o rimozione di repliche per il gruppo di disponibilità, è presente un ulteriore passaggio dell'aggiornamento dei metadati di listener con informazioni sulle macchine virtuali di SQL Server. Pertanto, quando si modifica il numero di repliche del gruppo di disponibilità, è necessario usare anche il [aggiornamento del listener del gruppo di disponibilità del gruppo della macchina virtuale sql az](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-2018-03-01-hybrid#az-sql-vm-group-ag-listener-update) comando per aggiornare il listener con i metadati delle macchine virtuali di SQL Server. 


### <a name="add-a-replica"></a>Aggiungere una replica

Per aggiungere una nuova replica al gruppo di disponibilità, eseguire le operazioni seguenti:

1. Aggiungere la macchina virtuale di SQL Server al cluster:
   ```azurecli
   # Add SQL Server VM to the Cluster
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```
1. Usare SQL Server Management Studio (SSMS) per aggiungere l'istanza di SQL Server come replica all'interno del gruppo di disponibilità.
1. Aggiungere i metadati di macchina virtuale SQL Server per il listener:
   ```azurecli
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

### <a name="remove-a-replica"></a>Rimozione di una replica

Per rimuovere una replica dal gruppo di disponibilità, eseguire le operazioni seguenti:

1. Rimuovere la replica dal gruppo di disponibilità tramite SQL Server Management Studio (SSMS). 
1. Rimuovere i metadati di macchina virtuale SQL Server dal listener:
   ```azurecli
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs that remain>
   ```
1. Rimuovere la macchina virtuale di SQL Server dal cluster:
   ```azurecli
   # Remove SQL VM from cluster
   # example: az sql vm remove-from-group --name SQLVM3 --resource-group SQLVM-RG

   az sql vm remove-from-group --name <SQL VM name> --resource-group <RG name> 
   ```

## <a name="remove-availability-group-listener"></a>Rimuovere il listener del gruppo di disponibilità
Se in un secondo momento è necessario rimuovere il listener del gruppo di disponibilità configurati con CLI di Azure, è necessario passare attraverso il provider di risorse di VM di SQL. Poiché il listener è stato registrato tramite il provider di risorse di macchine virtuali SQL, eliminarlo semplicemente tramite SQL Server Management Studio non è sufficiente. In realtà eliminarlo tramite il provider di risorse di VM di SQL tramite la CLI di Azure. Questa operazione rimuove i metadati del listener AG dal provider di risorse di macchine virtuali SQL ed elimina fisicamente il listener dal gruppo di disponibilità. 

Il frammento di codice seguente elimina il listener del gruppo di disponibilità SQL sia dal provider di risorse SQL che dal gruppo di disponibilità: 

```azurecli
# Remove the AG listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti: 

* [Cos'è SQL Server in Macchine virtuali di Azure?](virtual-machines-windows-sql-server-iaas-overview.md)
* [Domande frequenti su SQL Server in esecuzione in macchine virtuali Windows in Azure](virtual-machines-windows-sql-server-iaas-faq.md)
* [Note sulla versione di SQL Server in una macchina virtuale Azure](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [Come cambiare il modello di licenza per una macchina virtuale SQL Server in Azure](virtual-machines-windows-sql-ahb.md)
* [Panoramica di gruppi di disponibilità Always On &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [Configurazione di un'istanza del Server per i gruppi di disponibilità Always On &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [Amministrazione di un gruppo di disponibilità &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Monitoraggio dei gruppi di disponibilità &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Panoramica delle istruzioni Transact-SQL per i gruppi di disponibilità Always On &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Panoramica dei cmdlet di PowerShell per gruppi di disponibilità Always On &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  
