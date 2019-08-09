---
title: Usare l'interfaccia della riga di comando di Azure per configurare un gruppo di disponibilità Always On per SQL Server in una macchina virtuale di Azure
description: Usare l'interfaccia della riga di comando di Azure per creare il cluster di failover di Windows, il listener del gruppo di disponibilità e il servizio di bilanciamento del carico interno in una macchina virtuale SQL Server in Azure.
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
ms.openlocfilehash: 5bfbf995b67ac49cf169565046daa2887a57e476
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68846159"
---
# <a name="use-the-azure-cli-to-configure-an-always-on-availability-group-for-sql-server-on-an-azure-vm"></a>Usare l'interfaccia della riga di comando di Azure per configurare un gruppo di disponibilità Always On per SQL Server in una macchina virtuale di Azure
Questo articolo descrive come usare l'interfaccia della riga di comando di [Azure](/cli/azure/sql/vm?view=azure-cli-latest/) per distribuire un cluster di failover Windows, aggiungere SQL Server macchine virtuali al cluster e creare il servizio di bilanciamento del carico interno e il listener per un gruppo di disponibilità always on. La distribuzione del gruppo di disponibilità Always On viene ancora eseguita manualmente tramite SQL Server Management Studio (SSMS). 

## <a name="prerequisites"></a>Prerequisiti
Per automatizzare l'installazione di un gruppo di disponibilità Always On usando l'interfaccia della riga di comando di Azure, è necessario che siano soddisfatti i prerequisiti seguenti: 
- Una [sottoscrizione di Azure](https://azure.microsoft.com/free/).
- Un gruppo di risorse con un controller di dominio. 
- Una o più macchine virtuali appartenenti a un dominio [in Azure che eseguono SQL Server 2016 (o versione successiva) Enterprise Edition](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) nello *stesso set di disponibilità o in zone di disponibilità diverse* [registrate con il provider di risorse della macchina virtuale SQL](virtual-machines-windows-sql-register-with-resource-provider.md).  
- [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). 
- Due indirizzi IP disponibili, non usati da alcuna entità. Uno per il servizio di bilanciamento del carico interno. L'altro riguarda il listener del gruppo di disponibilità all'interno della stessa subnet del gruppo di disponibilità. Se si usa un servizio di bilanciamento del carico esistente, è necessario un solo indirizzo IP disponibile per il listener del gruppo di disponibilità. 

## <a name="permissions"></a>Autorizzazioni
Per configurare il gruppo di disponibilità Always On usando l'interfaccia della riga di comando di Azure, sono necessarie le autorizzazioni seguenti per l'account: 

- Account utente di dominio esistente che dispone dell'autorizzazione **Crea oggetto computer** nel dominio. Un account amministratore di dominio, ad esempio, ha in genere un'autorizzazione sufficiente account@domain.com(ad esempio:). _Questo account deve anche fare parte del gruppo degli amministratori locali in ogni macchina virtuale per creare il cluster._
- Account utente di dominio che controlla il servizio SQL Server. 
 
## <a name="step-1-create-a-storage-account-as-a-cloud-witness"></a>Passaggio 1: Creare un account di archiviazione come server di controllo del cloud
Per il cluster è necessario un account di archiviazione che funga da server di controllo del cloud. È possibile usare qualsiasi account di archiviazione esistente oppure è possibile creare un nuovo account di archiviazione. Se si vuole usare un account di archiviazione esistente, passare alla sezione successiva. 

Il frammento di codice seguente crea l'account di archiviazione: 
```azurecli-interactive
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region ex:eastus> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

>[!TIP]
> Potrebbe essere visualizzato l'errore `az sql: 'vm' is not in the 'az sql' command group` se si usa una versione obsoleta dell'interfaccia della riga di comando di Azure. Scaricare la [versione più recente dell'interfaccia della riga di](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) comando di Azure per superare l'errore.

## <a name="step-2-define-windows-failover-cluster-metadata"></a>Passaggio 2: Definire i metadati del cluster di failover di Windows
L'interfaccia della riga di comando di Azure [AZ SQL VM Group](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest) Command Group gestisce i metadati del servizio WSFC (Windows Server failover cluster) che ospita il gruppo di disponibilità. I metadati del cluster includono il dominio Active Directory, gli account del cluster, gli account di archiviazione da usare come server di controllo del cloud e la versione SQL Server. Usare il comando [AZ SQL VM Group create](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest#az-sql-vm-group-create) per definire i metadati per WSFC in modo che, quando viene aggiunta la prima SQL Server VM, il cluster venga creato come definito. 

Il frammento di codice seguente definisce i metadati per il cluster:
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

## <a name="step-3-add-sql-server-vms-to-the-cluster"></a>Passaggio 3: Aggiungere macchine virtuali SQL Server al cluster
L'aggiunta della prima macchina virtuale SQL Server al cluster crea il cluster. Il comando [AZ SQL VM Add-to-Group](https://docs.microsoft.com/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-add-to-group) crea il cluster con il nome specificato in precedenza, installa il ruolo del cluster nelle macchine virtuali SQL Server e li aggiunge al cluster. Gli utilizzi successivi del `az sql vm add-to-group` comando aggiungono altre macchine virtuali SQL Server al cluster appena creato. 

Il frammento di codice seguente crea il cluster e aggiunge la prima macchina virtuale SQL Server: 

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
Usare questo comando per aggiungere altre macchine virtuali SQL Server al cluster. Modificare solo il `-n` parametro per il nome della macchina virtuale SQL Server. 

## <a name="step-4-create-the-availability-group"></a>Passaggio 4: Creare il gruppo di disponibilità
Creare manualmente il gruppo di disponibilità come si farebbe normalmente usando [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)o [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

>[!IMPORTANT]
> *Non* creare un listener in questo momento perché questa operazione viene eseguita tramite l'interfaccia della riga di comando di Azure nelle sezioni riportate di seguito.  

## <a name="step-5-create-the-internal-load-balancer"></a>Passaggio 5: Creare il servizio di bilanciamento del carico interno

Il listener del gruppo di disponibilità Always On richiede un'istanza interna di Azure Load Balancer. Il servizio di bilanciamento del carico interno fornisce un indirizzo IP "mobile" per il listener del gruppo di disponibilità che consente un failover e una riconnessione più veloci. Se le macchine virtuali SQL Server in un gruppo di disponibilità fanno parte dello stesso set di disponibilità, è possibile usare un servizio di bilanciamento del carico di base. In caso contrario, è necessario usare un servizio di bilanciamento del carico standard.  

> [!NOTE]
> Il servizio di bilanciamento del carico interno deve trovarsi nella stessa rete virtuale delle istanze di VM SQL Server. 

Il frammento di codice seguente crea il servizio di bilanciamento del carico interno:

```azurecli-interactive
# Create the internal load balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

>[!IMPORTANT]
> La risorsa IP pubblico per ogni macchina virtuale SQL Server deve avere uno SKU standard per essere compatibile con il servizio di bilanciamento del carico standard. Per determinare lo SKU della risorsa IP pubblico della macchina virtuale, passare a **gruppo di risorse**, selezionare la risorsa **indirizzo IP pubblico** per la macchina virtuale SQL Server desiderata e individuare il valore in **SKU** nel riquadro **Overview (panoramica** ).  

## <a name="step-6-create-the-availability-group-listener"></a>Passaggio 6: Creare il listener del gruppo di disponibilità
Dopo aver creato manualmente il gruppo di disponibilità, è possibile creare il listener usando il comando [AZ SQL VM AG-listener](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-latest#az-sql-vm-group-ag-listener-create). 

L' *ID della risorsa della subnet* è il `/subnets/<subnetname>` valore aggiunto all'ID risorsa della risorsa di rete virtuale. Per identificare l'ID risorsa della subnet:
   1. Passare al gruppo di risorse nel [portale di Azure](https://portal.azure.com). 
   1. Selezionare la risorsa rete virtuale. 
   1. Selezionare **Proprietà** nel riquadro **Impostazioni** . 
   1. Identificare l'ID risorsa per la rete virtuale e aggiungerlo `/subnets/<subnetname>` alla fine per creare l'ID risorsa della subnet. Ad esempio:
      - L'ID risorsa della rete virtuale è:`/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
      - Il nome della subnet è:`default`
      - L'ID della risorsa della subnet è quindi:`/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


Il seguente frammento di codice crea il listener del gruppo di disponibilità:

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

## <a name="modify-the-number-of-replicas-in-an-availability-group"></a>Modificare il numero di repliche in un gruppo di disponibilità
Quando si distribuisce un gruppo di disponibilità per SQL Server macchine virtuali ospitate in Azure, si verifica un ulteriore livello di complessità. Il provider di risorse e il gruppo di macchine virtuali ora gestiscono le risorse. Di conseguenza, quando si aggiungono o si rimuovono le repliche nel gruppo di disponibilità, è disponibile un ulteriore passaggio dell'aggiornamento dei metadati del listener con le informazioni sulle macchine virtuali SQL Server. Quando si modifica il numero di repliche nel gruppo di disponibilità, è necessario usare anche il comando [AZ SQL VM Group AG-listener Update](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-2018-03-01-hybrid#az-sql-vm-group-ag-listener-update) per aggiornare il listener con i metadati delle macchine virtuali SQL Server. 


### <a name="add-a-replica"></a>Aggiungere una replica

Per aggiungere una nuova replica al gruppo di disponibilità:

1. Aggiungere la macchina virtuale SQL Server al cluster:
   ```azurecli-interactive
   # Add the SQL Server VM to the cluster
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```
1. Utilizzare SQL Server Management Studio per aggiungere l'istanza di SQL Server come replica all'interno del gruppo di disponibilità.
1. Aggiungere i metadati della macchina virtuale SQL Server al listener:
   ```azurecli-interactive
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

### <a name="remove-a-replica"></a>Rimozione di una replica

Per rimuovere una replica dal gruppo di disponibilità:

1. Rimuovere la replica dal gruppo di disponibilità utilizzando SQL Server Management Studio. 
1. Rimuovere i metadati della VM SQL Server dal listener:
   ```azurecli-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs that remain>
   ```
1. Rimuovere la macchina virtuale SQL Server dal cluster:
   ```azurecli-interactive
   # Remove the SQL VM from the cluster
   # example: az sql vm remove-from-group --name SQLVM3 --resource-group SQLVM-RG

   az sql vm remove-from-group --name <SQL VM name> --resource-group <RG name> 
   ```

## <a name="remove-the-availability-group-listener"></a>Rimuovere il listener del gruppo di disponibilità
Se in un secondo momento è necessario rimuovere il listener del gruppo di disponibilità configurato con l'interfaccia della riga di comando di Azure, è necessario usare il provider di risorse della macchina virtuale SQL. Poiché il listener viene registrato tramite il provider di risorse VM SQL, è sufficiente eliminarlo tramite SQL Server Management Studio non è sufficiente. 

Il metodo migliore consiste nell'eliminarlo tramite il provider di risorse VM SQL usando il frammento di codice seguente nell'interfaccia della riga di comando di Azure. In questo modo i metadati del listener del gruppo di disponibilità vengono rimossi dal provider di risorse della macchina virtuale SQL. Elimina anche fisicamente il listener dal gruppo di disponibilità. 

```azurecli-interactive
# Remove the availability group listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere i seguenti articoli: 

* [Panoramica delle macchine virtuali SQL Server](virtual-machines-windows-sql-server-iaas-overview.md)
* [Domande frequenti per le macchine virtuali SQL Server](virtual-machines-windows-sql-server-iaas-faq.md)
* [Note sulla versione per le macchine virtuali SQL Server](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [Come cambiare il modello di licenza per una macchina virtuale SQL Server in Azure](virtual-machines-windows-sql-ahb.md)
* [Panoramica di Gruppi di disponibilità Always On &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [Configurazione di un'istanza del server per &#40;gruppi di disponibilità always on SQL Server&#41;](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [Amministrazione di un gruppo &#40;di disponibilità SQL Server&#41;](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Monitoraggio dei gruppi &#40;di disponibilità SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Panoramica delle istruzioni Transact-SQL per Gruppi di disponibilità Always On &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Panoramica dei cmdlet di PowerShell per Gruppi di disponibilità Always On &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  
