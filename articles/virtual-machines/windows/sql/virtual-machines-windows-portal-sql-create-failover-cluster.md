---
title: Istanza del cluster di failover di SQL Server - Macchine virtuali di Azure| Documentazione Microsoft
description: Questo articolo illustra come creare l'istanza del cluster di failover di SQL Server nelle macchine virtuali di Azure.
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: jhubbard
editor: monicar
tags: azure-service-management
ms.assetid: 9fc761b1-21ad-4d79-bebc-a2f094ec214d
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 09/26/2017
ms.author: mikeray
ms.openlocfilehash: 1bbfd7cc63d534d7f9c360ad4afd05bd4e225725
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-sql-server-failover-cluster-instance-on-azure-virtual-machines"></a>Configurare l'istanza del cluster di failover di SQL Server nelle macchine virtuali di Azure

Questo articolo illustra come creare l'istanza del cluster di failover di SQL Server nelle macchine virtuali di Azure con il modello Resource Manager. Questa soluzione usa la funzionalità [Spazi di archiviazione diretta \(S2D\) di Windows Server 2016 Datacenter Edition](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview) come SAN virtuale basata su software per la sincronizzazione delle risorse di archiviazione (dischi dati) tra i nodi (VM di Azure) in un cluster Windows. S2D è una novità di Windows Server 2016.

Il diagramma seguente illustra la soluzione completa nelle macchine virtuali di Azure:

![Gruppo di disponibilità](./media/virtual-machines-windows-portal-sql-create-failover-cluster/00-sql-fci-s2d-complete-solution.png)

Il diagramma precedente mostra quanto segue:

- Due macchine virtuali di Azure in un cluster di failover di Windows. Una macchina virtuale in un cluster di failover è detta anche *nodo del cluster* o *nodo*.
- Ogni macchina virtuale ha due o più dischi dati.
- S2D sincronizza i dati nel disco dati e presenta le risorse di archiviazione sincronizzate sotto forma di pool di archiviazione.
- Il pool di archiviazione presenta un volume condiviso cluster (CSV) per il cluster di failover.
- Il ruolo del cluster dell'istanza del cluster di failover di SQL Server usa il volume condiviso cluster per le unità dati.
- Un servizio di bilanciamento del carico di Azure contiene l'indirizzo IP per l'istanza del cluster di failover di SQL Server.
- Un set di disponibilità di Azure contiene tutte le risorse.

   >[!NOTE]
   >Tutte le risorse di Azure nel diagramma si trovano nello stesso gruppo di risorse.

Per informazioni dettagliate su S2D, vedere l'articolo relativo a [Spazi di archiviazione diretta \(S2D\) in Windows Server 2016 Datacenter Edition](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview).

S2D supporta due tipi di architettura: con convergenza e con iperconvergenza. L'architettura in questo documento è iperconvergente. In un'infrastruttura iperconvergente, l'archiviazione risiede negli stessi server che ospitano l'applicazione in cluster. In questa architettura, l'archiviazione risiede in ogni nodo dell'istanza del cluster di failover di SQL Server.

### <a name="example-azure-template"></a>Modello di Azure di esempio

È possibile creare l'intera soluzione in Azure da un modello. Un esempio di modello è disponibile tra i [modelli di avvio rapido di Azure](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad) in GitHub. Questo esempio non è progettato né testato per carichi di lavoro specifici. È possibile eseguire il modello per creare un'istanza del cluster di failover di SQL Server con risorse di archiviazione S2D connesse al dominio. È possibile valutare il modello e modificarlo in base alle proprie esigenze.

## <a name="before-you-begin"></a>Prima di iniziare

Prima di procedere, è necessario conoscere alcuni aspetti ed essere in possesso di alcuni elementi.

### <a name="what-to-know"></a>Conoscenze necessarie
È necessario avere una conoscenza operativa delle tecnologie seguenti:

- [Tecnologie cluster di Windows](http://technet.microsoft.com/library/hh831579.aspx)
-  [Istanze del cluster di failover di SQL Server](http://msdn.microsoft.com/library/ms189134.aspx)

È anche necessario avere una conoscenza generale delle tecnologie seguenti:

- [Soluzione iperconvergente che usa Spazi di archiviazione diretta in Windows Server 2016](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct)
- [Gruppi di risorse di Azure](../../../azure-resource-manager/resource-group-portal.md)

### <a name="what-to-have"></a>Elementi necessari

Prima di seguire le istruzioni di questo articolo, è necessario avere gli elementi seguenti:

- Una sottoscrizione di Microsoft Azure.
- Un dominio Windows in macchine virtuali di Azure.
- Un account con l'autorizzazione necessaria per creare oggetti nella macchina virtuale di Azure.
- Una rete virtuale di Azure e una subnet con uno spazio indirizzi IP sufficiente per i componenti seguenti:
   - Entrambe le macchine virtuali.
   - Indirizzo IP del cluster di failover.
   - Indirizzo IP per ogni istanza del cluster di failover.
- DNS configurato nella rete di Azure, che punta ai controller di dominio.

Dopo aver soddisfatto questi prerequisiti, è possibile procedere con la creazione del cluster di failover. Il primo passaggio consiste nel creare le macchine virtuali.

## <a name="step-1-create-virtual-machines"></a>Passaggio 1: Creare le macchine virtuali

1. Accedere al [portale di Azure](http://portal.azure.com) con la propria sottoscrizione.

1. [Creare un set di disponibilità di Azure](../tutorial-availability-sets.md).

   Un set di disponibilità raggruppa le macchine virtuali in più domini di errore e domini di aggiornamento. Il set di disponibilità garantisce che l'applicazione non sia influenzata da singoli punti di errore, come il commutatore di rete o l'unità di alimentazione di un rack di server.

   Se non è stato creato il gruppo di risorse per le macchine virtuali, crearlo al momento della creazione di un set di disponibilità di Azure. Se si usa il portale di Azure per creare il set di disponibilità, seguire questa procedura:

   - Nel portale di Azure fare clic su **+** per aprire Azure Marketplace. Cercare **Set di disponibilità**.
   - Fare clic su **Set di disponibilità**.
   - Fare clic su **Crea**.
   - Nel pannello **Crea set di disponibilità** impostare i valori seguenti.
      - **Nome**: nome del set di disponibilità.
      - **Sottoscrizione**: sottoscrizione di Azure.
      - **Gruppo di risorse**: se si vuole usare un gruppo esistente, fare clic su **Usa esistente** e selezionare il gruppo nell'elenco a discesa. In caso contrario, scegliere **Crea nuovo** e digitare un nome per il gruppo.
      - **Località**: impostare la località in cui si intende creare le macchine virtuali.
      - **Domini di errore**: usare il valore predefinito (3).
      - **Domini di aggiornamento**: usare il valore predefinito (5).
   - Fare clic su **Crea** per creare il set di disponibilità.

1. Creare le macchine virtuali nel set di disponibilità.

   Effettuare il provisioning di due macchine virtuali di SQL Server nel set di disponibilità di Azure. Per istruzioni, vedere [Effettuare il provisioning di una macchina virtuale di SQL Server nel portale di Azure](virtual-machines-windows-portal-sql-server-provision.md).

   Inserire entrambe le macchine virtuali:

   - Nello stesso gruppo di risorse di Azure in cui si trova il set di disponibilità.
   - Nella stessa rete del controller di dominio.
   - In una subnet con spazio indirizzi IP sufficiente per entrambe le macchine virtuali e tutte le istanze del cluster di failover che si potrebbero usare nel cluster.
   - Nel set di disponibilità di Azure.   

      >[!IMPORTANT]
      >Non è possibile impostare o modificare il set di disponibilità dopo che è stata creata una macchina virtuale.

   Scegliere un'immagine da Azure Marketplace. È possibile usare un'immagine del Marketplace che include Windows Server e SQL Server o solo Windows Server. Per informazioni dettagliate, vedere [Panoramica di SQL Server in macchine virtuali di Azure](../../virtual-machines-windows-sql-server-iaas-overview.md).

   Le immagini ufficiali di SQL Server nella raccolta di Azure includono un'istanza installata di SQL Server, nonché il software di installazione di SQL Server e la chiave necessaria.

   Scegliere l'immagine appropriata in base alla modalità di pagamento della licenza di SQL Server che si preferisce.

   - **Licenza con pagamento in base all'uso**. Il costo al minuto di queste immagini include la licenza di SQL Server:
      - **SQL Server 2016 Enterprise in Windows Server Datacenter 2016**
      - **SQL Server 2016 Standard in Windows Server Datacenter 2016**
      - **SQL Server 2016 Developer in Windows Server Datacenter 2016**

   - **BYOL (Bring Your Own License)**

      - **{BYOL} SQL Server 2016 Enterprise in Windows Server Datacenter 2016**
      - **{BYOL} SQL Server 2016 Standard in Windows Server Datacenter 2016**

   >[!IMPORTANT]
   >Dopo aver creato la macchina virtuale, rimuovere l'istanza autonoma preinstallata di SQL Server. I supporti dell'istanza preinstallata di SQL Server verranno usati per creare SQL Server FCI dopo la configurazione del cluster di failover e S2D.

   In alternativa, è possibile usare immagini di Azure Marketplace con il solo sistema operativo. Scegliere un'immagine di **Windows Server 2016 Datacenter** e installare l'istanza di SQL Server FCI dopo la configurazione del cluster di failover e S2D. Un'immagine di questo tipo non contiene i supporti di installazione di SQL Server. Posizionare i supporti di installazione in un percorso in cui è possibile eseguire l'installazione di SQL Server per ogni server.

1. Dopo aver creato le macchine virtuali di Azure, connettersi a ognuna con RDP.

   Quando ci si connette per la prima volta a una macchina virtuale con RDP, il computer chiede se si vuole rendere il PC individuabile sulla rete. Fare clic su **Sì**.

1. Se si usa un'immagine di macchina virtuale basata su SQL Server, rimuovere l'istanza di SQL Server.

   - In **Programmi e funzionalità** fare clic con il pulsante destro del mouse su **Microsoft SQL Server 2016 (64 bit)** e scegliere **Disinstalla/Cambia**.
   - Fare clic su **Rimuovi**.
   - Selezionare l'istanza predefinita.
   - Rimuovere tutte le funzionalità in **Servizi motore di database**. Non rimuovere **Funzionalità condivise**. Vedere l'immagine seguente:

      ![Rimuovere le funzionalità](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   - Fare clic su **Avanti** e quindi su **Rimuovi**.

1. <a name="ports"></a>Aprire le porte del firewall.

   In ogni macchina virtuale aprire le porte seguenti in Windows Firewall.

   | Scopo | Porta TCP | Note
   | ------ | ------ | ------
   | SQL Server | 1433 | Porta normale per le istanze predefinite di SQL Server. Se è stata usata un'immagine della raccolta, questa porta è automaticamente aperta.
   | Probe di integrità | 59999 | Qualsiasi porta TCP aperta. In un passaggio successivo, configurare il [probe di integrità](#probe) del servizio di bilanciamento del carico e il cluster per l'uso di questa porta.  

1. Aggiungere risorse di archiviazione alla macchina virtuale. Per informazioni dettagliate, vedere l'articolo relativo all'[aggiunta dell'archiviazione](../../../storage/common/storage-premium-storage.md).

   Entrambe le macchine virtuali necessitano di almeno due dischi dati.

   Collegare dischi non formattati, ossia senza formattazione NTFS.
      >[!NOTE]
      >Se si collegano dischi con formattazione NTFS, è possibile abilitare S2D solo senza controllo dell'idoneità del disco.  

   Collegare almeno due dischi SSD Archiviazione Premium a ogni VM. È consigliabile usare almeno dischi P30 (da 1 TB).

   Impostare la memorizzazione nella cache dell'host su **Sola lettura**.

   La capacità di archiviazione usata negli ambienti di produzione dipende dal carico di lavoro. I valori descritti in questo articolo sono forniti a scopo dimostrativo e di test.

1. [Aggiungere le macchine virtuali al dominio preesistente](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

Dopo aver creato e configurato le macchine virtuali, è possibile configurare il cluster di failover.

## <a name="step-2-configure-the-windows-failover-cluster-with-s2d"></a>Passaggio 2: Configurare il cluster di failover di Windows con S2D

Il passaggio successivo consiste nel configurare il cluster di failover con S2D. Questo passaggio include i passaggi secondari seguenti:

1. Aggiungere la funzionalità Clustering di failover di Windows
1. Convalidare il cluster
1. Creare il cluster di failover
1. Creare il cloud di controllo
1. Aggiungere le risorse di archiviazione

### <a name="add-windows-failover-clustering-feature"></a>Aggiungere la funzionalità Clustering di failover di Windows

1. Per iniziare, connettersi alla prima macchina virtuale con RDP usando un account di dominio che è membro del gruppo degli amministratori locali e ha le autorizzazioni necessarie per creare oggetti in Active Directory. Usare questo account per il resto della configurazione.

1. [Aggiungere la funzionalità Clustering di failover a ogni macchina virtuale](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

   Per installare la funzionalità Clustering di failover dall'interfaccia utente, eseguire questa procedura in entrambe le macchine virtuali.
   - In **Server Manager** fare clic su **Gestione** e quindi su **Aggiungi ruoli e funzionalità**.
   - Nell'**Aggiunta guidata ruoli e funzionalità** fare clic su **Avanti** fino a visualizzare **Selezione funzionalità**.
   - In **Selezione funzionalità** selezionare **Clustering di failover**. Includere tutte le funzionalità necessarie e gli strumenti di gestione. Fare clic su **Aggiungi funzionalità**.
   - Fare clic su **Avanti** e quindi su **Fine** per installare le funzionalità.

   Per installare la funzionalità Clustering di failover con PowerShell, eseguire lo script seguente da una sessione di PowerShell in modalità amministratore in una delle macchine virtuali.

   ```PowerShell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

Per riferimento, i passaggi successivi seguono le istruzioni riportate nel passaggio 3 dell'articolo [Soluzione iperconvergente che usa Spazi di archiviazione diretta in Windows Server 2016](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct).

### <a name="validate-the-cluster"></a>Convalidare il cluster

Questa guida fa riferimento alle istruzioni riportate nella sezione relativa alla [convalida del cluster](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-31-run-cluster-validation).

Convalidare il cluster nell'interfaccia utente o con PowerShell.

Per convalidare il cluster con l'interfaccia utente, eseguire questa procedura da una delle macchine virtuali.

1. In **Server Manager** fare clic su **Strumenti** e quindi su **Gestione cluster di failover**.
1. In **Gestione cluster di failover** fare clic su **Azione** e quindi su **Convalida configurazione**.
1. Fare clic su **Avanti**.
1. In **Selezione di server o di un cluster** digitare il nome di entrambe le macchine virtuali.
1. In **Opzioni di testing** scegliere **Esegui solo test selezionati**. Fare clic su **Avanti**.
1. In **Selezione dei test** includere tutti i test tranne **Archiviazione**. Vedere l'immagine seguente:

   ![Test di convalida](./media/virtual-machines-windows-portal-sql-create-failover-cluster/10-validate-cluster-test.png)

1. Fare clic su **Avanti**.
1. In **Conferma** fare clic su **Avanti**.

La **Convalida guidata configurazione** eseguirà i test di convalida.

Per convalidare il cluster con PowerShell, eseguire lo script seguente da una sessione di PowerShell in modalità amministratore in una delle macchine virtuali.

   ```PowerShell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

Dopo aver convalidato il cluster, creare il cluster di failover.

### <a name="create-the-failover-cluster"></a>Creare il cluster di failover

Questa guida fa riferimento alle istruzioni riportate in [Creare il cluster di failover](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-32-create-a-cluster).

Per creare il cluster di failover è necessario:
- I nomi delle macchine virtuali che diventeranno i nodi del cluster.
- un nome per il cluster di failover
- un indirizzo IP per il cluster di failover. È possibile usare un indirizzo IP non usato nella stessa rete virtuale di Azure e nella stessa subnet dei nodi del cluster.

Lo script di PowerShell seguente crea un cluster di failover. Aggiornare lo script con i nomi dei nodi (ossia i nomi delle macchine virtuali) e un indirizzo IP disponibile della rete virtuale di Azure:

```PowerShell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

### <a name="create-a-cloud-witness"></a>Creare un cloud di controllo

Il cloud di controllo è un nuovo tipo di quorum di controllo del cluster archiviato in un BLOB del servizio di archiviazione di Azure. Viene così eliminata l'esigenza di una VM separata che ospiti una condivisione di controllo.

1. [Creare un cloud di controllo per il cluster di failover](http://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Creare un contenitore BLOB.

1. Salvare le chiavi di accesso e l'URL del contenitore.

1. Configurare il quorum di controllo del cluster di failover. Vedere la sezione relativa alla [configurazione del quorum di controllo nell'interfaccia utente](http://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness).

### <a name="add-storage"></a>Aggiungere le risorse di archiviazione

I dischi per S2D devono essere vuoti e senza partizioni o altri dati. Per pulire i dischi seguire la procedura illustrata in [questa guida](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-34-clean-disks).

1. [Abilitare Spazi di archiviazione diretta \(S2D\)](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct).

   Il comando di PowerShell seguente abilita Spazi di archiviazione diretta.  

   ```PowerShell
   Enable-ClusterS2D
   ```

   In **Gestione cluster di failover** verrà ora visualizzato il pool di archiviazione.

1. [Creare un volume](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes).

   Una delle funzionalità di S2D è la creazione automatica di un pool di archiviazione al momento dell'abilitazione. È ora possibile creare un volume. Il cmdlet di PowerShell `New-Volume` automatizza il processo di creazione di volumi includendo la formattazione, l'aggiunta al cluster e la creazione di un volume condiviso cluster. L'esempio seguente illustra la creazione di un volume condiviso cluster di 800 gigabyte (GB).

   ```PowerShell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   Al termine dell'esecuzione del comando, un volume di 800 GB viene montato come risorsa cluster. Il volume si trova in `C:\ClusterStorage\Volume1\`.

   Il diagramma seguente illustra un volume condiviso cluster con S2D:

   ![Volume condiviso cluster](./media/virtual-machines-windows-portal-sql-create-failover-cluster/15-cluster-shared-volume.png)

## <a name="step-3-test-failover-cluster-failover"></a>Passaggio 3: Testare il failover del cluster di failover

In Gestione cluster di failover verificare che sia possibile spostare la risorsa di archiviazione nell'altro nodo del cluster. Se è possibile connettersi al cluster di failover con **Gestione cluster di failover** e spostare la risorsa di archiviazione da un nodo all'altro, si può configurare l'istanza FCI.

## <a name="step-4-create-sql-server-fci"></a>Passaggio 4: Creare l'istanza del cluster di failover di SQL Server

Dopo aver configurato il cluster di failover e tutti i componenti del cluster inclusa l'archiviazione, è possibile SQL Server FCI.

1. Connettersi alla prima macchina virtuale con RDP.

1. In **Gestione cluster di failover** verificare che tutte le risorse principali del cluster si trovino nella prima macchina virtuale. Se necessario, spostare tutte le risorse in questa macchina virtuale.

1. Individuare i supporti di installazione. Se la macchina virtuale usa una delle immagini di Azure Marketplace, i supporti si trovano in `C:\SQLServer_<version number>_Full`. Fare clic su **Configura**.

1. In **Centro installazione SQL Server** fare clic su **Installazione**.

1. Fare clic su **Installazione di un nuovo cluster di failover di SQL Server**. Seguire le istruzioni della procedura guidata per installare l'istanza del cluster di failover di SQL Server.

   Le directory di dati dell'istanza del cluster di failover devono trovarsi nelle risorse di archiviazione del cluster. Con S2D non si tratterà di un disco condiviso, ma di un punto di montaggio per un volume in ogni server. S2D sincronizza il volume tra i due nodi. Il volume viene presentato al cluster come volume condiviso cluster. Usare il punto di montaggio del volume condiviso cluster per le directory di dati.

   ![Directory di dati](./media/virtual-machines-windows-portal-sql-create-failover-cluster/20-data-dicrectories.png)

1. Al termine della procedura guidata, verrà installata un'istanza del cluster di failover di SQL Server nel primo nodo.

1. Al termine dell'installazione dell'istanza del cluster di failover nel primo nodo, connettersi al secondo nodo con RDP.

1. Aprire il **Centro installazione SQL Server**. Fare clic su **Installazione**.

1. Fare clic su **Aggiunta di un nodo a un cluster di failover di SQL Server**. Seguire le istruzioni della procedura guidata per installare SQL Server e aggiungere questo server all'istanza del cluster di failover.

   >[!NOTE]
   >Se è stata usata un'immagine della raccolta di Azure Marketplace con SQL Server, gli strumenti di SQL Server sono stati inclusi con l'immagine. In caso contrario, installare gli strumenti di SQL Server separatamente. Vedere [Scaricare SQL Server Management Studio (SSMS)](http://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-5-create-azure-load-balancer"></a>Passaggio 5: Creare un servizio di bilanciamento del carico di Azure

Nelle macchine virtuali di Azure, per contenere un indirizzo IP che deve trovarsi in un nodo del cluster alla volta viene usato nei cluster un servizio di bilanciamento del carico. In questa soluzione, il servizio di bilanciamento del carico contiene l'indirizzo IP per l'istanza del cluster di failover di SQL Server.

[Creare e configurare un servizio di bilanciamento del carico di Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Creare il servizio di bilanciamento del carico nel portale di Azure

Per creare il servizio di bilanciamento del carico:

1. Nel portale di Azure passare al gruppo di risorse con le macchine virtuali.

1. Fare clic su **+ Aggiungi**. Cercare **Servizio di bilanciamento del carico** nel Marketplace. Fare clic su **Servizio di bilanciamento del carico**.

1. Fare clic su **Crea**.

1. Configurare il servizio di bilanciamento del carico con le impostazioni seguenti.

   - **Nome**: un nome che identifichi il servizio di bilanciamento del carico.
   - **Tipo**: il servizio di bilanciamento del carico può essere pubblico o privato. Un servizio di bilanciamento del carico privato è accessibile dalla stessa rete virtuale. La maggior parte delle applicazioni Azure può usare un servizio di bilanciamento del carico privato. Se l'applicazione deve accedere a SQL Server direttamente su Internet, usare un servizio di bilanciamento del carico pubblico.
   - **Rete virtuale**: la stessa rete delle macchine virtuali.
   - **Subnet**: la stessa subnet delle macchine virtuali.
   - **Indirizzo IP privato**: lo stesso indirizzo IP assegnato alla risorsa di rete cluster dell'istanza del cluster di failover di SQL Server.
   - **Sottoscrizione**: sottoscrizione di Azure.
   - **Gruppo di risorse**: usare lo stesso gruppo di risorse delle macchine virtuali.
   - **Località**: usare la stessa località di Azure delle macchine virtuali.
   Vedere l'immagine seguente:

   ![Creare il servizio di bilanciamento del carico](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)

### <a name="configure-the-load-balancer-backend-pool"></a>Configurare il pool back-end del servizio di bilanciamento del carico

1. Tornare al gruppo di risorse di Azure con le macchine virtuali e individuare il nuovo servizio di bilanciamento del carico. Potrebbe essere necessario aggiornare la visualizzazione del gruppo di risorse. Fare clic sul servizio di bilanciamento del carico.

1. Nel pannello del servizio di bilanciamento del carico fare clic su **Pool back-end**.

1. Fare clic su **+ Aggiungi** per aggiungere un pool back-end.

1. Digitare un nome per il pool back-end.

1. Fare clic su **Aggiungi una macchina virtuale**.

1. Nel pannello **Scegliere le macchine virtuali** fare clic su **Scegliere un set di disponibilità**.

1. Scegliere il set di disponibilità in cui sono state inserite le macchine virtuali di SQL Server.

1. Nel pannello **Scegliere le macchine virtuali** fare clic su **Scegliere le macchine virtuali**.

   Il portale di Azure si presenterà come l'immagine seguente:

   ![Creare il back-end del servizio di bilanciamento di carico](./media/virtual-machines-windows-portal-sql-create-failover-cluster/33-load-balancer-back-end.png)

1. Fare clic su **Seleziona** nel pannello **Scegliere le macchine virtuali**.

1. Fare clic su **OK** due volte.

### <a name="configure-a-load-balancer-health-probe"></a>Configurare un probe di integrità per il servizio di bilanciamento del carico

1. Nel pannello del servizio di bilanciamento del carico fare clic su **Probe integrità**.

1. Fare clic su **+ Aggiungi**.

1. Nel pannello **Aggiungi probe integrità** <a name="probe"></a>impostare i parametri del probe di integrità.

   - **Nome**: nome del probe di integrità.
   - **Protocollo**: TCP.
   - **Porta**: impostare una porta TCP disponibile. È necessaria una porta del firewall aperta. Usare la [stessa porta](#ports) impostata per il probe di integrità nel firewall.
   - **Intervallo**: 5 secondi.
   - **Soglia di non integrità**: 2 errori consecutivi.

1. Fare clic su OK.

### <a name="set-load-balancing-rules"></a>Impostare le regole di bilanciamento del carico

1. Nel pannello del servizio di bilanciamento del carico fare clic su **Regole di bilanciamento del carico**.

1. Fare clic su **+ Aggiungi**.

1. Impostare i parametri delle regole di bilanciamento del carico.

   - **Nome**: nome delle regole di bilanciamento del carico.
   - **Indirizzo IP front-end**: usare l'indirizzo IP per la risorsa di rete cluster dell'istanza del cluster di failover di SQL Server.
   - **Porta**: impostare la porta TCP dell'istanza del cluster di failover di SQL Server. La porta predefinita dell'istanza è 1433.
   - **Porta back-end**: per questo valore viene usata la stessa porta inserita come valore in **Porta** quando si abilita **IP mobile (Direct Server Return)**.
   - **Pool back-end**: usare il nome del pool back-end configurato in precedenza.
   - **Probe integrità**: usare il probe di integrità configurato in precedenza.
   - **Salvataggio permanente sessione**: Nessuno.
   - **Timeout di inattività (minuti)**: 4.
   - **IP mobile (Direct Server Return)**: Abilitato.

1. Fare clic su **OK**.

## <a name="step-6-configure-cluster-for-probe"></a>Passaggio 6: Configurare il cluster per il probe

Impostare il parametro della porta probe del cluster in PowerShell.

Per impostare tale parametro, aggiornare le variabili nello script seguente con i valori dell'ambiente in uso. Rimuovere le parentesi acute `<>` dallo script. 

   ```PowerShell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

Nello script precedente impostare i valori per l'ambiente in uso. L'elenco seguente offre una descrizione dei valori.

   - `<Cluster Network Name>`: nome del cluster di failover di Windows Server per la rete. In **Gestione cluster di failover** > **Reti** fare clic con il pulsante destro del mouse sulla rete e scegliere **Proprietà**. Il valore corretto è riportato in **Nome** nella scheda **Generale**. 

   - `<SQL Server FCI IP Address Resource Name>`: nome della risorsa indirizzo IP dell'istanza del cluster di failover di SQL Server. In **Gestione cluster di failover** > **Ruoli** fare clic con il pulsante destro del mouse sulla risorsa indirizzo IP sotto **Nome server** nel ruolo dell'istanza del cluster di failover di SQL Server e quindi scegliere **Proprietà**. Il valore corretto è riportato in **Nome** nella scheda **Generale**. 

   - `<ILBIP>`: indirizzo IP ILB. Questo indirizzo è configurato nel portale di Azure come indirizzo ILB front-end ed è anche l'indirizzo IP dell'istanza del cluster di failover di SQL Server. È riportato in **Gestione cluster di failover** nella stessa pagina delle proprietà in cui si trova `<SQL Server FCI IP Address Resource Name>`.  

   - `<nnnnn>`: porta probe configurata nel probe di integrità del servizio di bilanciamento del carico. È valida qualsiasi porta TCP inutilizzata. 

>[!IMPORTANT]
>La subnet mask per il parametro del cluster deve essere l'indirizzo di broadcast IP TCP: `255.255.255.255`.

Dopo aver configurato il probe del cluster, è possibile visualizzare tutti i parametri del cluster in PowerShell. Eseguire lo script seguente:

   ```PowerShell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-7-test-fci-failover"></a>Passaggio 7: Eseguire il failover dell'istanza del cluster di failover

Testare il failover dell'istanza del cluster di failover per convalidare le funzionalità del cluster. Seguire anche questa procedura:

1. Connettersi a un nodo del cluster dell'istanza del cluster di failover di SQL Server con RDP.

1. Aprire **Gestione cluster di failover**. Fare clic su **Ruoli**. Rilevare quale nodo è proprietario del ruolo dell'istanza del cluster di failover di SQL Server.

1. Fare clic con il pulsante destro del mouse sul ruolo dell'istanza del cluster di failover di SQL Server.

1. Scegliere **Sposta** e quindi fare clic su **Miglior nodo possibile**.

In **Gestione cluster di failover** viene visualizzato il ruolo e le relative risorse passano alla modalità offline. Le risorse vengono quindi spostate e portate online nell'altro nodo.

### <a name="test-connectivity"></a>Testare la connettività

Per testare la connettività, accedere a un'altra macchina virtuale nella stessa rete virtuale. Aprire **SQL Server Management Studio** e connettersi al nome dell'istanza del cluster di failover di SQL Server.

>[!NOTE]
>Se necessario, è possibile [scaricare SQL Server Management Studio](http://msdn.microsoft.com/library/mt238290.aspx).

## <a name="limitations"></a>Limitazioni
Nelle macchine virtuali di Azure, Microsoft Distributed Transaction Coordinator (DTC) non è supportato nelle istanze del cluster di failover perché la porta RPC non è supportata dal servizio di bilanciamento del carico.

## <a name="see-also"></a>Vedere anche

[Configurare S2D con Desktop remoto (Azure)](http://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-storage-spaces-direct-deployment)

[Soluzione iperconvergente che usa Spazi di archiviazione diretta](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct).

[Panoramica di Spazi di archiviazione diretta](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)

[Supporto di SQL Server per S2D](https://blogs.technet.microsoft.com/dataplatforminsider/2016/09/27/sql-server-2016-now-supports-windows-server-2016-storage-spaces-direct/)
