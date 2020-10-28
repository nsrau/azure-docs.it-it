---
title: Creare un'istanza FCI con Spazi di archiviazione diretta
description: Usare Spazi di archiviazione diretta per creare un'istanza del cluster di failover (FCI) con SQL Server in macchine virtuali di Azure.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/18/2020
ms.author: mathoma
ms.openlocfilehash: 848f3cd2d5719d62e39f46c166d51e09ec89bd4c
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792515"
---
# <a name="create-an-fci-with-storage-spaces-direct-sql-server-on-azure-vms"></a>Creare un'istanza FCI con Spazi di archiviazione diretta (SQL Server in macchine virtuali di Azure)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Questo articolo illustra come creare un'istanza del cluster di failover usando [spazi di archiviazione diretta](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) con SQL Server in macchine virtuali (VM) di Azure. Spazi di archiviazione diretta funge da rete di archiviazione virtuale (rete VSAN) basata su software che sincronizza l'archiviazione (dischi dati) tra i nodi (VM di Azure) in un cluster Windows. 

Per altre informazioni, vedere Panoramica di [FCI con SQL Server nelle macchine virtuali di Azure](failover-cluster-instance-overview.md) e [procedure consigliate per cluster](hadr-cluster-best-practices.md). 


## <a name="overview"></a>Panoramica 

[Spazi di archiviazione diretta (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) supporta due tipi di architettura: convergente e iperconvergente. Un'infrastruttura iperconvergente posiziona l'archiviazione sugli stessi server che ospitano l'applicazione in cluster, in modo che l'archiviazione si trovi in ogni nodo dell'istanza del cluster di failover SQL Server. 

Il diagramma seguente illustra la soluzione completa, che usa Spazi di archiviazione diretta iperconvergenti con SQL Server in macchine virtuali di Azure: 

![Diagramma della soluzione completa, usando Spazi di archiviazione diretta iperconvergenti](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/00-sql-fci-s2d-complete-solution.png)

Il diagramma precedente mostra le risorse seguenti nello stesso gruppo di risorse:

- Due macchine virtuali in un cluster di failover di Windows Server. Una macchina virtuale in un cluster di failover è detta anche *nodo del cluster* o *nodo* .
- Ogni macchina virtuale ha due o più dischi dati.
- Spazi di archiviazione diretta sincronizza i dati nei dischi dati e presenta le risorse di archiviazione sincronizzate sotto forma di pool di archiviazione.
- Il pool di archiviazione presenta un volume condiviso cluster per il cluster di failover.
- Il ruolo del cluster dell'istanza del cluster di failover di SQL Server usa il volume condiviso cluster per le unità dati.
- Un servizio di bilanciamento del carico di Azure contiene l'indirizzo IP per l'istanza del cluster di failover di SQL Server.
- Un set di disponibilità di Azure contiene tutte le risorse.

   > [!NOTE]
   > È possibile creare questa intera soluzione in Azure da un modello. Un esempio di modello è disponibile nella pagina dei [modelli di avvio rapido di Azure](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad) in GitHub. Questo esempio non è progettato né testato per carichi di lavoro specifici. È possibile eseguire il modello per creare un'istanza del cluster di failover di SQL Server con risorse di archiviazione di Spazi di archiviazione diretta connesse al dominio. È possibile valutare il modello e modificarlo in base alle proprie esigenze.


## <a name="prerequisites"></a>Prerequisiti

Prima di completare le istruzioni riportate in questo articolo, è necessario avere già:

- Una sottoscrizione di Azure. Inizia subito [gratuitamente](https://azure.microsoft.com/free/). 
- [Due o più macchine virtuali di Windows Azure predisposte](failover-cluster-instance-prepare-vm.md) in un [set di disponibilità](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set).
- Un account con autorizzazioni per creare oggetti sia nelle macchine virtuali di Azure che in Active Directory.
- La versione più recente di [PowerShell](/powershell/azure/install-az-ps). 


## <a name="add-the-windows-cluster-feature"></a>Aggiungere la funzionalità cluster Windows

1. Connettersi alla prima macchina virtuale utilizzando Remote Desktop Protocol (RDP) con un account di dominio che è membro degli amministratori locali e che dispone dell'autorizzazione per la creazione di oggetti in Active Directory. Usare questo account per il resto della configurazione.

1. Aggiungere il clustering di failover a ogni macchina virtuale.

   Per installare il clustering di failover dall'interfaccia utente, eseguire le operazioni seguenti in entrambe le macchine virtuali:

   1. In **Server Manager** selezionare **Gestione** e quindi **Aggiungi ruoli e funzionalità** .
   1. Nell' **Aggiunta guidata ruoli e funzionalità** selezionare **Avanti** fino a quando non si ottengono le **funzionalità selezionate** .
   1. In **Seleziona funzionalità** selezionare **Clustering di failover** . Includere tutte le funzionalità necessarie e gli strumenti di gestione. 
   1. Selezionare **Aggiungi funzionalità** .
   1. Selezionare **Avanti** e quindi selezionare **Fine** per installare le funzionalità.

   Per installare il clustering di failover tramite PowerShell, eseguire lo script seguente da una sessione di PowerShell amministratore in una delle macchine virtuali:

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

Per ulteriori informazioni sui passaggi successivi, vedere le istruzioni riportate nella sezione "Step 3: Configure Spazi di archiviazione diretta" della [soluzione iperconvergente con spazi di archiviazione diretta in Windows Server 2016](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-3-configure-storage-spaces-direct).


## <a name="validate-the-cluster"></a>Convalidare il cluster

Convalidare il cluster nell'interfaccia utente o usando PowerShell.

Per convalidare il cluster usando l'interfaccia utente, eseguire le operazioni seguenti in una delle macchine virtuali:

1. In **Server Manager** selezionare **Strumenti** e quindi selezionare **Gestione cluster di failover** .
1. In **Gestione cluster di failover** selezionare **Azione** e quindi selezionare **Convalida configurazione** .
1. Selezionare **Avanti** .
1. In **Selezione di server o di un cluster** immettere i nomi di entrambe le macchine virtuali.
1. In **Opzioni di testing** selezionare **Esegui solo test selezionati** . 
1. Selezionare **Avanti** .
1. In **Selezione dei test** selezionare tutti i test tranne **Archiviazione** , come illustrato di seguito:

   ![Selezionare i test di convalida del cluster](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/10-validate-cluster-test.png)

1. Selezionare **Avanti** .
1. In **Conferma** selezionare **Avanti** .

    La convalida guidata **configurazione** esegue i test di convalida.

Per convalidare il cluster usando PowerShell, eseguire lo script seguente da una sessione di PowerShell in modalità amministratore in una delle macchine virtuali:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

Dopo aver convalidato il cluster, creare il cluster di failover.


## <a name="create-failover-cluster"></a>Crea cluster di failover

Per creare il cluster di failover è necessario:

- I nomi delle macchine virtuali che diventeranno i nodi del cluster.
- Nome del cluster di failover.
- un indirizzo IP per il cluster di failover. È possibile usare un indirizzo IP non usato nella stessa rete virtuale di Azure e nella stessa subnet dei nodi del cluster.


# <a name="windows-server-2012---2016"></a>[Windows Server 2012 - 2016](#tab/windows2012)

Lo script di PowerShell seguente crea un cluster di failover per Windows Server 2012 tramite Windows Server 2016. Aggiornare lo script con i nomi dei nodi (ossia i nomi delle macchine virtuali) e un indirizzo IP disponibile della rete virtuale di Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

# <a name="windows-server-2019"></a>[Windows Server 2019](#tab/windows2019)

Lo script di PowerShell seguente crea un cluster di failover per Windows Server 2019.  Aggiornare lo script con i nomi dei nodi (ossia i nomi delle macchine virtuali) e un indirizzo IP disponibile della rete virtuale di Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```

Per altre informazioni, vedere [Cluster di failover: oggetto di rete cluster](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97).

---


## <a name="configure-quorum"></a>Configurare il quorum

Configurare la soluzione quorum più adatta alle proprie esigenze aziendali. È possibile configurare un [disco](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)di controllo, un [cloud](/windows-server/failover-clustering/deploy-cloud-witness)di controllo o una [condivisione file](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)di controllo. Per altre informazioni, vedere [quorum con SQL Server VM](hadr-cluster-best-practices.md#quorum). 

## <a name="add-storage"></a>Aggiungere le risorse di archiviazione

I dischi per Spazi di archiviazione diretta devono essere vuoti. Non possono contenere partizioni o altri dati. Per pulire i dischi, seguire le istruzioni riportate in [Deploy spazi di archiviazione diretta](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-31-clean-drives).

1. [Abilitare spazi di archiviazione diretta](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-35-enable-storage-spaces-direct).

   Lo script di PowerShell seguente abilita Spazi di archiviazione diretta:  

   ```powershell
   Enable-ClusterS2D
   ```

   In **Gestione cluster di failover** verrà ora visualizzato il pool di archiviazione.

1. [Creare un volume](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-36-create-volumes).

   Spazi di archiviazione diretta crea automaticamente un pool di archiviazione al momento dell'abilitazione. È ora possibile creare un volume. Il cmdlet di PowerShell `New-Volume` automatizza il processo di creazione del volume. Questo processo include la formattazione, l'aggiunta del volume al cluster e la creazione di un volume CSV. Questo esempio Mostra come creare un file CSV da 800 Gigabyte (GB):

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   Dopo aver eseguito il comando precedente, viene montato un volume di 800 GB come risorsa cluster. Il volume si trova in `C:\ClusterStorage\Volume1\`.

   Questo screenshot mostra un volume CSV con Spazi di archiviazione diretta:

   ![Screenshot di un Volume condiviso cluster con Spazi di archiviazione diretta](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/15-cluster-shared-volume.png)



## <a name="test-cluster-failover"></a>Test del failover del cluster

Testare il failover del cluster. In **Gestione cluster di failover** fare clic con il pulsante destro del mouse sul cluster, scegliere **altre azioni**  >  **Sposta Core Cluster Resource**  >  **selezionare nodo** , quindi selezionare l'altro nodo del cluster. Spostare le risorse principali del cluster in ogni nodo del cluster, quindi spostarle di nuovo nel nodo primario. Se lo spostamento del cluster in ogni nodo avviene in modo corretto, è possibile installare SQL Server.  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="Testare il failover del cluster spostando le risorse principali negli altri nodi":::

## <a name="create-sql-server-fci"></a>Creare l'istanza del cluster di failover di SQL Server

Dopo aver configurato il cluster di failover e tutti i componenti del cluster, inclusa la risorsa di archiviazione, è possibile creare l'istanza del cluster di failover di SQL Server.

1. Connettersi alla prima macchina virtuale con RDP.

1. In **Gestione cluster di failover** assicurarsi che tutte le risorse del cluster di base si trovino nella prima macchina virtuale. Se necessario, spostare tutte le risorse in tale macchina virtuale.

1. Individuare i supporti di installazione. Se la macchina virtuale usa una delle immagini di Azure Marketplace, i supporti si trovano in `C:\SQLServer_<version number>_Full`. Selezionare **Imposta** .

1. In **Centro installazione SQL Server** selezionare **Installazione** .

1. Selezionare **Installazione di un nuovo cluster di failover di SQL Server** . Seguire le istruzioni della procedura guidata per installare l'istanza del cluster di failover di SQL Server.

   Le directory di dati dell'istanza del cluster di failover devono trovarsi nelle risorse di archiviazione del cluster. Con Spazi di archiviazione diretta, non si tratta di un disco condiviso, ma di un punto di montaggio su un volume in ogni server. Spazi di archiviazione diretta sincronizza il volume tra i due nodi. Il volume viene presentato al cluster come un volume CSV. Usare il punto di montaggio del volume condiviso cluster per le directory di dati.

   ![Directory dati](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/20-data-dicrectories.png)

1. Dopo aver completato le istruzioni della procedura guidata, il programma di installazione installa una SQL Server FCI nel primo nodo.

1. Al termine dell'installazione dell'istanza del cluster di failover nel primo nodo, connettersi al secondo nodo usando RDP.

1. Aprire il **Centro installazione SQL Server** . Selezionare **Installazione** .

1. Selezionare **Aggiungi nodo a cluster di failover di SQL Server** . Seguire le istruzioni della procedura guidata per installare SQL Server e aggiungere il server all'istanza del cluster di failover.

   >[!NOTE]
   >Se è stata usata un'immagine della raccolta di Azure Marketplace che contiene SQL Server, gli strumenti di SQL Server sono stati inclusi con l'immagine. In caso contrario, installare gli strumenti di SQL Server separatamente. Per altre informazioni, vedere [Scaricare SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).
   >


## <a name="register-with-the-sql-vm-rp"></a>Eseguire la registrazione con la macchina virtuale SQL RP

Per gestire la macchina virtuale SQL Server dal portale, registrarla con il provider di risorse VM SQL (RP) in [modalità di gestione leggera](sql-vm-resource-provider-register.md#lightweight-management-mode), attualmente l'unica modalità supportata con FCI e SQL Server in macchine virtuali di Azure. 


Registrare una macchina virtuale SQL Server in modalità Lightweight con PowerShell:  

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
         
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType PAYG -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>Configurare la connettività 

Per instradare il traffico in modo appropriato al nodo primario corrente, configurare l'opzione di connettività adatta per l'ambiente. È possibile creare un servizio di [bilanciamento del carico di Azure](failover-cluster-instance-vnn-azure-load-balancer-configure.md) o, se si usa SQL Server 2019 Cu2 (o versione successiva) e Windows Server 2016 (o versione successiva), è invece possibile usare la funzionalità nome di [rete distribuita](failover-cluster-instance-distributed-network-name-dnn-configure.md) . 

## <a name="limitations"></a>Limitazioni

- Le macchine virtuali di Azure supportano Microsoft Distributed Transaction Coordinator (MSDTC) in Windows Server 2019 con archiviazione su CSVs e un servizio di [bilanciamento del carico standard](../../../load-balancer/load-balancer-overview.md).
- I dischi che sono stati collegati come dischi formattati NTFS possono essere usati con Spazi di archiviazione diretta solo se l'opzione di idoneità del disco è deselezionata o cancellata quando è in corso l'aggiunta dell'archiviazione al cluster. 
- È supportata solo la registrazione con il provider di risorse VM SQL in [modalità di gestione semplice](sql-vm-resource-provider-register.md#management-modes) .

## <a name="next-steps"></a>Passaggi successivi

Se non è già stato fatto, configurare la connettività all'istanza del cluster di failover con un [nome di rete virtuale e un](failover-cluster-instance-vnn-azure-load-balancer-configure.md) servizio di bilanciamento del carico di Azure o un nome di [rete distribuita (DNN)](failover-cluster-instance-distributed-network-name-dnn-configure.md). 

Se Spazi di archiviazione diretta non è la soluzione di archiviazione FCI appropriata, provare a creare l'istanza FCI usando i [dischi condivisi di Azure](failover-cluster-instance-azure-shared-disks-manually-configure.md) o le [condivisioni file Premium](failover-cluster-instance-premium-file-share-manually-configure.md) . 

Per altre informazioni, vedere Panoramica di [FCI con SQL Server nelle macchine virtuali di Azure](failover-cluster-instance-overview.md) e [procedure consigliate](hadr-cluster-best-practices.md)per la configurazione del cluster. 

Per altre informazioni, vedere: 
- [Tecnologie cluster di Windows](/windows-server/failover-clustering/failover-clustering-overview)   
- [Istanze del cluster di failover di SQL Server](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)