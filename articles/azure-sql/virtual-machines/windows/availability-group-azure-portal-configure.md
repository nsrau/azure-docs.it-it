---
title: Configurare un gruppo di disponibilità (portale di Azure)
description: Usare il portale di Azure per creare il cluster di failover di Windows, il listener del gruppo di disponibilità e il servizio di bilanciamento del carico interno in una VM SQL Server in Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/20/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 8634efa1e8e5ab8a3b962b711ec8dfcdac4e6ced
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2020
ms.locfileid: "92164568"
---
# <a name="use-azure-portal-to-configure-an-availability-group-preview-for-sql-server-on-azure-vm"></a>Usare portale di Azure per configurare un gruppo di disponibilità (anteprima) per SQL Server nella macchina virtuale di Azure 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Questo articolo descrive come usare la [portale di Azure](https://portal.azure.com) per configurare un gruppo di disponibilità per SQL Server in macchine virtuali di Azure. 

Usare il portale di Azure per creare un nuovo cluster o caricare un cluster esistente, quindi creare il gruppo di disponibilità, il listener e il servizio di bilanciamento del carico interno. 

Questa funzionalità è attualmente in anteprima. 

Sebbene questo articolo usi il portale di Azure per configurare l'ambiente del gruppo di disponibilità, è anche possibile usare [PowerShell o l'interfaccia della](availability-group-az-commandline-configure.md)riga di comando di Azure, i [modelli di avvio rapido di Azure](availability-group-quickstart-template-configure.md)o [manualmente](availability-group-manually-configure-tutorial.md) . 


## <a name="prerequisites"></a>Prerequisiti

Per configurare un gruppo di disponibilità Always On usando il portale di Azure, è necessario che siano soddisfatti i prerequisiti seguenti: 

- Una [sottoscrizione di Azure](https://azure.microsoft.com/free/).
- Un gruppo di risorse con un controller di dominio. 
- Una o più macchine virtuali appartenenti a un dominio [in Azure che eseguono SQL Server 2016 (o versione successiva) Enterprise Edition](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) nello *stesso* set di disponibilità o in *diverse* zone di disponibilità [registrate con il provider di risorse VM SQL in modalità di gestibilità completa](sql-vm-resource-provider-register.md) e usano lo stesso account di dominio per il servizio di SQL Server in ogni macchina virtuale.
- Due indirizzi IP disponibili, non usati da alcuna entità. Uno è per il bilanciamento del carico interno, l'altro è per il listener del gruppo di disponibilità all'interno della stessa subnet del gruppo di disponibilità. Se si usa un servizio di bilanciamento del carico esistente, è necessario un solo indirizzo IP disponibile per il listener del gruppo di disponibilità. 

## <a name="permissions"></a>Autorizzazioni

Per configurare il gruppo di disponibilità portale di Azure, è necessario disporre delle autorizzazioni di account seguenti: 

- Un account utente di dominio esistente con l'autorizzazione **Create Computer Object** (Crea oggetti computer) nel dominio. Un account amministratore di dominio ha in genere autorizzazioni sufficienti, ad esempio: account@domain.com. _Questo account deve anche fare parte del gruppo degli amministratori locali in ogni macchina virtuale per creare il cluster._
- L'account utente di dominio che controlla SQL Server. Deve essere lo stesso account per ogni macchina virtuale SQL Server che si intende aggiungere al gruppo di disponibilità. 

## <a name="configure-cluster"></a>Configurare il cluster

Configurare il cluster usando il portale di Azure. È possibile creare un nuovo cluster oppure, se si dispone già di un cluster esistente, è possibile caricarlo nel provider di risorse VM SQL per la gestibilità del portale.


### <a name="create-a-new-cluster"></a>Creare un nuovo cluster

Se si dispone già di un cluster, ignorare questa sezione e passare a caricare il [cluster esistente](#onboard-existing-cluster) . 

Se non si dispone già di un cluster esistente, crearlo utilizzando la portale di Azure con i seguenti passaggi:

1. Accedere al [portale di Azure](https://portal.azure.com). 
1. Passare alla risorsa [macchine virtuali SQL](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) . 
1. Selezionare **disponibilità elevata** in **Impostazioni**. 
1. Selezionare **+ nuovo cluster di failover di Windows Server** per aprire la pagina **Configura cluster di failover Windows** .  

   :::image type="content" source="media/availability-group-az-portal-configure/create-new-cluster.png" alt-text="Creare un nuovo cluster selezionando + nuovo cluster nel portale":::

1. Assegnare un nome al cluster e specificare un account di archiviazione da usare come server di controllo del cloud. Usare un account di archiviazione esistente o selezionare **Crea nuovo** per creare un nuovo account di archiviazione. I nomi degli account di archiviazione devono essere compresi tra 3 e 24 caratteri e usare solo numeri e lettere minuscole.

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-cluster-1.png" alt-text="Creare un nuovo cluster selezionando + nuovo cluster nel portale":::

1. Espandere **credenziali del cluster di failover di Windows Server** per fornire le [credenziali](https://docs.microsoft.com/rest/api/sqlvm/sqlvirtualmachinegroups/createorupdate#wsfcdomainprofile) per l'account del servizio SQL Server, nonché per l'operatore del cluster e gli account bootstrap se sono diversi dall'account utilizzato per il servizio SQL Server. 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-cluster-2.png" alt-text="Creare un nuovo cluster selezionando + nuovo cluster nel portale"
    ```
    


### <a name="onboard-existing-cluster"></a>Onboarding del cluster esistente

Se si dispone già di un cluster configurato nell'ambiente SQL Server VM, è possibile caricarlo dal portale di Azure.

A tale scopo, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com). 
1. Passare alla risorsa [macchine virtuali SQL](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) . 
1. Selezionare **disponibilità elevata** in **Impostazioni**. 
1. Selezionare **onboarding cluster di failover di Windows Server esistente** per aprire la pagina **onboarding Windows Server failover cluster** . 

   :::image type="content" source="media/availability-group-az-portal-configure/onboard-existing-cluster.png" alt-text="Creare un nuovo cluster selezionando + nuovo cluster nel portale":::

1. Esaminare le impostazioni per il cluster. 
1. Selezionare **applica** per caricare il cluster e quindi selezionare **Sì** al prompt per continuare.




## <a name="create-availability-group"></a>Creare un gruppo di disponibilità

Dopo la creazione o l'onboarding del cluster, creare il gruppo di disponibilità usando il portale di Azure. A tale scopo, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com). 
1. Passare alla risorsa [macchine virtuali SQL](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) . 
1. Selezionare **disponibilità elevata** in **Impostazioni**. 
1. Selezionare **+ nuovo always on gruppo di disponibilità** per aprire la pagina **Crea gruppo di disponibilità** .

   :::image type="content" source="media/availability-group-az-portal-configure/create-new-availability-group.png" alt-text="Creare un nuovo cluster selezionando + nuovo cluster nel portale":::

1. Immettere un nome per il gruppo di disponibilità. 
1. Selezionare **Configura listener** per aprire la pagina **Configura listener gruppo di disponibilità** . 

   :::image type="content" source="media/availability-group-az-portal-configure/create-availability-group.png" alt-text="Creare un nuovo cluster selezionando + nuovo cluster nel portale":::

1. Compilare i valori e usare un servizio di bilanciamento del carico esistente oppure selezionare **Crea nuovo** per creare un nuovo servizio di bilanciamento del carico.  Selezionare **applica** per salvare le impostazioni e creare il listener e il servizio di bilanciamento del carico. 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-listener.png" alt-text="Creare un nuovo cluster selezionando + nuovo cluster nel portale":::

1. Scegliere **+ Seleziona replica** per aprire la pagina **Configura repliche del gruppo di disponibilità** .
1. Selezionare le macchine virtuali che si desidera aggiungere al gruppo di disponibilità e scegliere le impostazioni del gruppo di disponibilità più adatte alle proprie esigenze aziendali. Selezionare **applica** per salvare le impostazioni. 

   :::image type="content" source="media/availability-group-az-portal-configure/add-replicas.png" alt-text="Creare un nuovo cluster selezionando + nuovo cluster nel portale":::

1. Verificare le impostazioni del gruppo di disponibilità e quindi selezionare **applica** per creare il gruppo di disponibilità. 

È possibile controllare lo stato della distribuzione nel **log attività** , accessibile dall'icona a campana nella barra di spostamento superiore. 

  > [!NOTE]
  > L' **integrità della sincronizzazione** nella pagina di **disponibilità elevata** del portale di Azure viene visualizzata come **non integro** fino a quando non si aggiungono database al gruppo di disponibilità. 


## <a name="add-database-to-availability-group"></a>Aggiungi database a gruppo di disponibilità

Aggiungere i database al gruppo di disponibilità al termine della distribuzione. I passaggi seguenti usano SQL Server Management Studio (SSMS), ma è anche possibile usare [Transact-SQL o PowerShell](/sql/database-engine/availability-groups/windows/availability-group-add-a-database) . 

Per aggiungere database al gruppo di disponibilità tramite SQL Server Management Studio, attenersi alla procedura seguente:

1. Connettersi a una delle macchine virtuali SQL Server usando il metodo preferito, ad esempio Connessione Desktop remoto (RDP). 
1. Aprire SQL Server Management Studio (SSMS).
1. Connettersi all'istanza di SQL Server. 
1. Espandere **Always on disponibilità elevata** in **Esplora oggetti**.
1. Espandere **gruppi di disponibilità**, fare clic con il pulsante destro del mouse sul gruppo di disponibilità e scegliere **Aggiungi database..**..

   :::image type="content" source="media/availability-group-az-portal-configure/add-database.png" alt-text="Creare un nuovo cluster selezionando + nuovo cluster nel portale":::

1. Seguire le istruzioni per selezionare i database che si desidera aggiungere al gruppo di disponibilità. 
1. Selezionare **OK** per salvare le impostazioni e aggiungere il database al gruppo di disponibilità. 
1. Una volta aggiunto il database, aggiornare **Esplora oggetti** per verificare lo stato del database come `synchronized` . 

Una volta aggiunti i database, è possibile controllare lo stato del gruppo di disponibilità nel portale di Azure: 

:::image type="content" source="media/availability-group-az-portal-configure/healthy-availability-group.png" alt-text="Creare un nuovo cluster selezionando + nuovo cluster nel portale":::

## <a name="add-more-vms"></a>Aggiungere altre macchine virtuali

Per aggiungere altre macchine virtuali SQL Server al cluster, seguire questa procedura: 

1. Accedere al [portale di Azure](https://portal.azure.com). 
1. Passare alla risorsa [macchine virtuali SQL](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) . 
1. Selezionare **disponibilità elevata** in **Impostazioni**. 
1. Selezionare **configura Windows Server failover cluster** per aprire la pagina **Configura cluster di failover di Windows Server** . 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-existing-cluster.png" alt-text="Creare un nuovo cluster selezionando + nuovo cluster nel portale":::

1. Espandere **credenziali del cluster di failover di Windows Server** e immettere gli account usati per gli account servizio SQL Server, operatore cluster e bootstrap del cluster. 
1. Selezionare le macchine virtuali SQL Server che si desidera aggiungere al cluster. 
1. Selezionare **Applica**. 

È possibile controllare lo stato della distribuzione nel **log attività** , accessibile dall'icona a campana nella barra di spostamento superiore. 


## <a name="modify-availability-group"></a>Modificare il gruppo di disponibilità 


È possibile **aggiungere altre repliche** al gruppo di disponibilità, **configurare il listener**o **eliminare il listener** dalla pagina di **disponibilità elevata** nel portale di Azure selezionando i puntini di sospensione (...) accanto al gruppo di disponibilità: 

:::image type="content" source="media/availability-group-az-portal-configure/configure-listener.png" alt-text="Creare un nuovo cluster selezionando + nuovo cluster nel portale":::

## <a name="remove-cluster"></a>Rimuovi cluster

Rimuovere tutte le macchine virtuali SQL Server dal cluster per eliminarle, quindi rimuovere i metadati del cluster dal provider di risorse della macchina virtuale SQL. A tale scopo, è possibile usare la versione più recente dell'interfaccia della riga di comando di [Azure](/cli/azure/install-azure-cli) o PowerShell. 

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Rimuovere innanzitutto tutte le macchine virtuali SQL Server dal cluster. I nodi vengono rimossi fisicamente dal cluster ed eliminati definitivamente dal cluster:  

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

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si verificano problemi, è possibile controllare la cronologia della distribuzione ed esaminare gli errori comuni e le relative risoluzioni. 

### <a name="check-deployment-history"></a>Controllare la cronologia di distribuzione

Le modifiche al cluster e al gruppo di disponibilità tramite il portale vengono eseguite tramite le distribuzioni. La cronologia della distribuzione può fornire maggiori dettagli se si verificano problemi durante la creazione o l'onboarding del cluster o la creazione del gruppo di disponibilità.

Per visualizzare i log per la distribuzione e verificare la cronologia di distribuzione, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare al gruppo di risorse.
1. Fare clic su **Distribuzioni** in **Impostazioni**.
1. Selezionare la distribuzione di interesse per ulteriori informazioni sulla distribuzione. 


   :::image type="content" source="media/availability-group-az-portal-configure/failed-deployment.png" alt-text="Creare un nuovo cluster selezionando + nuovo cluster nel portale" :::

### <a name="common-errors"></a>Errori comuni

Esaminare i seguenti errori comuni e le relative soluzioni. 

#### <a name="the-account-which-is-used-to-start-up-sql-service-is-not-a-domain-account"></a>L'account utilizzato per avviare il servizio SQL non è un account di dominio

Ciò indica che il provider di risorse non è riuscito ad accedere al servizio SQL Server con le credenziali fornite. Alcune risoluzioni comuni:
- Verificare che il controller di dominio sia in esecuzione.
- Verificare che le credenziali fornite nel portale corrispondano a quelle del servizio SQL Server. 


## <a name="next-steps"></a>Passaggi successivi


Per ulteriori informazioni sui gruppi di disponibilità, vedere:

- [Panoramica sui gruppi di disponibilità](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)
* [Amministrazione di un gruppo di disponibilità](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Monitoraggio dei gruppi di disponibilità &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Istruzioni Transact-SQL del gruppo di disponibilità ](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Comandi di PowerShell per gruppi di disponibilità](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  

Per ulteriori informazioni sulle macchine virtuali SQL Server, vedere: 

* [Panoramica delle VM di SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Note sulla versione delle VM di SQL Server](../../database/doc-changes-updates-release-notes.md)
* [Domande frequenti sulle VM di SQL Server](frequently-asked-questions-faq.md)
