---
title: Istanza del cluster di failover di SQL Server - Macchine virtuali di Azure| Documentazione Microsoft
description: Questo articolo illustra come creare un'istanza del cluster di failover di SQL Server in macchine virtuali di Azure.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 9fc761b1-21ad-4d79-bebc-a2f094ec214d
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/11/2018
ms.author: mikeray
ms.openlocfilehash: 1a69741ba3ced91b6b0d1fc4bcd4aea887452151
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792187"
---
# <a name="configure-a-sql-server-failover-cluster-instance-on-azure-virtual-machines"></a>Configurare un'istanza del cluster di failover di SQL Server in macchine virtuali di Azure

Questo articolo illustra come creare un'istanza del cluster di failover di SQL Server in macchine virtuali di Azure nel modello di Azure Resource Manager. Questa soluzione USA [Windows Server 2016 Datacenter edition spazi di archiviazione diretta](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview) come una San virtuale basata su software che sincronizza l'archiviazione (dischi dati) tra i nodi (VM di Azure) in un cluster Windows. Spazi di archiviazione diretta era una novità di Windows Server 2016.

Il diagramma seguente illustra la soluzione completa nelle macchine virtuali di Azure:

![Soluzione completa](./media/virtual-machines-windows-portal-sql-create-failover-cluster/00-sql-fci-s2d-complete-solution.png)

Questo diagramma mostra:

- Due macchine virtuali di Azure in un cluster di failover di Windows Server. Quando una macchina virtuale si trova in un cluster di failover, viene anche chiamato nodo del *cluster* o *nodo*.
- Ogni macchina virtuale ha due o più dischi dati.
- Spazi di archiviazione diretta sincronizza i dati nei dischi dati e presenta l'archiviazione sincronizzata come pool di archiviazione.
- Il pool di archiviazione presenta un Volume condiviso cluster (CSV) al cluster di failover.
- Il ruolo del cluster dell'istanza del cluster di failover di SQL Server usa il volume condiviso cluster per le unità dati.
- Un servizio di bilanciamento del carico di Azure contiene l'indirizzo IP per l'istanza del cluster di failover di SQL Server.
- Un set di disponibilità di Azure contiene tutte le risorse.

>[!NOTE]
>Tutte le risorse di Azure nel diagramma si trovano nello stesso gruppo di risorse.

Per informazioni dettagliate sui Spazi di archiviazione diretta, vedere [spazi di archiviazione diretta di Windows Server 2016 Datacenter Edition](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview).

Spazi di archiviazione diretta supporta due tipi di architettura: convergente e iperconvergente. L'architettura in questo documento è iperconvergente. In un'infrastruttura iperconvergente, l'archiviazione risiede negli stessi server che ospitano l'applicazione in cluster. In questa architettura, l'archiviazione risiede in ogni nodo dell'istanza del cluster di failover di SQL Server.

## <a name="licensing-and-pricing"></a>Licenze e prezzi

Nelle macchine virtuali di Azure è possibile concedere in licenza SQL Server usando immagini di VM con pagamento in base al consumo (PAYG) o bring your own License (BYOL). Il tipo di immagine scelto influiscono sulla modalità di addebito.

Con la gestione delle licenze con pagamento in base al consumo, un'istanza del cluster di failover di SQL Server in macchine virtuali di Azure comporta l'addebito per tutti i nodi dell'istanza FCI, inclusi i nodi passivi. Per ulteriori informazioni, consultare le [tariffe delle Macchine virtuali SQL Server Enterprise](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/).

Se si dispone di Enterprise Agreement con Software Assurance, è possibile usare un nodo FCI passivo libero per ogni nodo attivo. Per sfruttare i vantaggi offerti da Azure, usare le immagini della macchina virtuale BYOL e usare la stessa licenza sia per i nodi attivi che per quelli passivi dell'istanza FCI. Per altre informazioni, consultare il [Contratto Enterprise](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx).

Per confrontare le licenze con pagamento in base al consumo e BYOL per SQL Server in macchine virtuali di Azure, vedere [Introduzione alle VM di SQL](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms).

Per informazioni complete sulle licenze SQL Server, consultare [Prezzi](https://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="example-azure-template"></a>Modello di Azure di esempio

È possibile creare questa intera soluzione in Azure da un modello. Un esempio di modello è disponibile tra i [modelli di avvio rapido di Azure](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad) in GitHub. Questo esempio non è stato progettato o testato per un carico di lavoro specifico. È possibile eseguire il modello per creare una SQL Server FCI con Spazi di archiviazione diretta archiviazione connessa al dominio. È possibile valutare il modello e modificarlo per le proprie esigenze.

## <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare, è necessario tenere presente quanto segue.

### <a name="what-to-know"></a>Conoscenze necessarie
È necessario conoscere in modo operativo le tecnologie seguenti:

- [Tecnologie cluster di Windows](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server istanze del cluster di failover](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

Una cosa da tenere presente è che in un cluster di failover di macchine virtuali IaaS di Azure, è consigliabile usare una singola scheda di interfaccia di rete per server (nodo del cluster) e una singola subnet. La rete di Azure ha ridondanza fisica, che rende superflue le NIC e le subnet aggiuntive in un cluster guest di macchine virtuali IaaS di Azure. Il report di convalida del cluster informa che i nodi sono raggiungibili solo in una singola rete. È possibile ignorare questo avviso nei cluster di failover guest della macchina virtuale IaaS di Azure.

È inoltre necessario conoscere le tecnologie seguenti:

- [Soluzioni iperconvergenti che usano Spazi di archiviazione diretta in Windows Server 2016](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Gruppi di risorse di Azure](../../../azure-resource-manager/manage-resource-groups-portal.md)

> [!IMPORTANT]
> A questo punto, SQL Server istanze del cluster di failover in macchine virtuali di Azure sono supportate solo con la [modalità di gestione semplice](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes) dell' [estensione dell'agente IaaS SQL Server](virtual-machines-windows-sql-server-agent-extension.md). Per passare dalla modalità di estensione completa a quella Lightweight, eliminare la risorsa della **macchina virtuale SQL** per le macchine virtuali corrispondenti e quindi registrarla con il provider di risorse VM SQL in modalità lightweight. Quando si elimina la risorsa della **macchina virtuale SQL** utilizzando la portale di Azure, **deselezionare la casella di controllo accanto alla macchina virtuale corretta**. L'estensione completa supporta funzionalità quali il backup automatizzato, l'applicazione di patch e la gestione avanzata del portale. Queste funzionalità non funzioneranno per le macchine virtuali SQL dopo che l'agente è stato reinstallato in modalità di gestione leggera.

### <a name="what-to-have"></a>Elementi necessari

Prima di completare i passaggi descritti in questo articolo, è necessario avere già:

- Una sottoscrizione di Microsoft Azure.
- Un dominio Windows in macchine virtuali di Azure.
- Un account che dispone delle autorizzazioni per creare oggetti in macchine virtuali di Azure e in Active Directory.
- Una rete virtuale e una subnet di Azure con spazio di indirizzi IP sufficiente per i componenti seguenti:
   - Entrambe le macchine virtuali.
   - Indirizzo IP del cluster di failover.
   - Indirizzo IP per ogni istanza del cluster di failover.
- DNS configurato nella rete di Azure, che punta ai controller di dominio.

Con questi prerequisiti, è possibile iniziare a compilare il cluster di failover. Il primo passaggio consiste nel creare le macchine virtuali.

## <a name="step-1-create-the-virtual-machines"></a>Passaggio 1: creare le macchine virtuali

1. Accedere al [portale di Azure](https://portal.azure.com) con la sottoscrizione.

1. [Creare un set di disponibilità di Azure](../tutorial-availability-sets.md).

   Un set di disponibilità raggruppa le macchine virtuali in più domini di errore e domini di aggiornamento. Garantisce che l'applicazione non sia influenzata da singoli punti di errore, ad esempio il commutire di rete o l'unità di alimentazione di un rack di server.

   Se non è stato creato il gruppo di risorse per le macchine virtuali, eseguirlo quando si crea un set di disponibilità di Azure. Se si usa il portale di Azure per creare il set di disponibilità, seguire questa procedura:

   1. Nella portale di Azure selezionare **Crea una risorsa** per aprire Azure Marketplace. Cercare **Set di disponibilità**.
   1. Selezionare **set di disponibilità**.
   1. Selezionare **Create** (Crea).
   1. In **Crea set di disponibilità**specificare i valori seguenti:
      - **Nome**: nome del set di disponibilità.
      - **Sottoscrizione**: sottoscrizione di Azure.
      - **Gruppo di risorse**: se si vuole usare un gruppo esistente, fare clic su **Seleziona esistente** e quindi selezionare il gruppo nell'elenco. In caso contrario, selezionare **Crea nuovo** e immettere un nome per il gruppo.
      - **Località**: impostare la località in cui si intende creare le macchine virtuali.
      - **Domini di errore**: usare il valore predefinito (**3**).
      - **Domini di aggiornamento**: usare il valore predefinito (**5**).
   1. Selezionare **Crea** per creare il set di disponibilità.

1. Creare le macchine virtuali nel set di disponibilità.

   Effettuare il provisioning di due macchine virtuali di SQL Server nel set di disponibilità di Azure. Per istruzioni, vedere [Effettuare il provisioning di una macchina virtuale di SQL Server nel portale di Azure](virtual-machines-windows-portal-sql-server-provision.md).

   Inserire entrambe le macchine virtuali:

   - Nello stesso gruppo di risorse di Azure del set di disponibilità.
   - Nella stessa rete del controller di dominio.
   - In una subnet con spazio di indirizzi IP sufficiente per le macchine virtuali e per tutti failover che potrebbero essere usati nel cluster.
   - Nel set di disponibilità di Azure.

      >[!IMPORTANT]
      >Non è possibile impostare o modificare il set di disponibilità dopo aver creato una macchina virtuale.

   Scegliere un'immagine da Azure Marketplace. È possibile usare un'immagine di Azure Marketplace che include Windows Server e SQL Server oppure usare un'immagine che include solo Windows Server. Per informazioni dettagliate, vedere [Panoramica delle SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md).

   Le immagini SQL Server ufficiali nella raccolta di Azure includono un'istanza di SQL Server installata, il software di installazione di SQL Server e la chiave necessaria.

   Scegliere l'immagine corretta, in base al modo in cui si desidera pagare la licenza di SQL Server:

   - **Licenze con pagamento in base all'utilizzo**. il costo al secondo di queste immagini include la licenza di SQL Server:
      - **SQL Server 2016 Enterprise in Windows Server 2016 Datacenter**
      - **SQL Server 2016 standard in Windows Server 2016 Datacenter**
      - **SQL Server 2016 Developer su Windows Server 2016 Datacenter**

   - **BYOL (Bring Your Own License)**

      - **BYOL SQL Server 2016 Enterprise in Windows Server 2016 Datacenter**
      - **BYOL SQL Server 2016 standard in Windows Server 2016 Datacenter**

   >[!IMPORTANT]
   >Dopo aver creato la macchina virtuale, rimuovere l'istanza autonoma preinstallata di SQL Server. Usare il supporto di SQL Server preinstallato per creare l'istanza FCI di SQL Server dopo aver configurato il cluster di failover e Spazi di archiviazione diretta.

   In alternativa, è possibile usare immagini di Azure Marketplace che contengono solo il sistema operativo. Scegliere un'immagine di **Windows Server 2016 datacenter** e installare l'istanza FCI di SQL Server dopo aver configurato il cluster di failover e spazi di archiviazione diretta. Questa immagine non contiene SQL Server supporti di installazione. Posizionare il supporto di installazione di SQL Server in un percorso in cui sia possibile eseguirlo per ogni server.

1. Quando Azure crea le macchine virtuali, connettersi a ciascuna di esse tramite RDP.

   Quando si esegue la prima connessione a una macchina virtuale tramite RDP, viene chiesto se si desidera consentire al PC di essere individuabile sulla rete. Selezionare **Sì**.

1. Se si usa una delle immagini di macchina virtuale basate su SQL Server, rimuovere l'istanza di SQL Server.

   1. In **programmi e funzionalità**fare clic con il pulsante destro del mouse su **Microsoft SQL Server 2016 (64-bit)** e scegliere **Disinstalla/Cambia**.
   1. Selezionare **Rimuovi**.
   1. Selezionare l'istanza predefinita.
   1. Rimuovere tutte le funzionalità in **Servizi motore di database**. Non rimuovere le **funzionalità condivise**. Verrà visualizzata una schermata simile alla seguente:

      ![Selezione funzionalità](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   1. Selezionare **Avanti**, quindi selezionare **Rimuovi**.

1. <a name="ports"></a>Aprire le porte del firewall.

   In ogni macchina virtuale aprire queste porte nella Windows Firewall:

   | Finalità | Porta TCP | Note
   | ------ | ------ | ------
   | SQL Server | 1433 | Porta normale per le istanze predefinite di SQL Server. Se è stata usata un'immagine della raccolta, questa porta è automaticamente aperta.
   | Probe di integrità | 59999 | Qualsiasi porta TCP aperta. In un passaggio successivo, configurare il [probe di integrità](#probe) del servizio di bilanciamento del carico e il cluster per l'uso di questa porta.  

1. Aggiungere risorse di archiviazione alla macchina virtuale. Per informazioni dettagliate, vedere l'articolo relativo all'[aggiunta dell'archiviazione](../disks-types.md).

   Entrambe le macchine virtuali necessitano di almeno due dischi dati.

   Connetti dischi RAW, non dischi formattati con NTFS.
      >[!NOTE]
      >Se si alleghino dischi formattati NTFS, è possibile abilitare Spazi di archiviazione diretta solo senza il controllo di idoneità del disco.  

   Collegare almeno due unità SSD Premium a ogni macchina virtuale. Si consiglia almeno P30 (1 TB) dischi.

   Impostare la memorizzazione nella cache dell'host su **Sola lettura**.

   La capacità di archiviazione usata negli ambienti di produzione dipende dal carico di lavoro. I valori descritti in questo articolo sono forniti a scopo dimostrativo e di test.

1. [Aggiungere le macchine virtuali al dominio preesistente](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

Dopo aver creato e configurato le macchine virtuali, è possibile configurare il cluster di failover.

## <a name="step-2-configure-the-windows-server-failover-cluster-with-storage-spaces-direct"></a>Passaggio 2: configurare il cluster di failover di Windows Server con Spazi di archiviazione diretta

Il passaggio successivo consiste nel configurare il cluster di failover con Spazi di archiviazione diretta. In questo passaggio verranno completati i passaggi seguenti:

1. Aggiungere la funzionalità Windows Server failover clustering.
1. Convalidare il cluster.
1. Creare il cluster di failover di.
1. Creare il server di controllo del cloud.
1. Aggiungere spazio di archiviazione.

### <a name="add-windows-server-failover-clustering"></a>Aggiungi clustering di failover di Windows Server

1. Connettersi alla prima macchina virtuale con RDP utilizzando un account di dominio che sia membro degli amministratori locali e che disponga delle autorizzazioni per creare oggetti in Active Directory. Usare questo account per il resto della configurazione.

1. [Aggiungere il clustering di failover a ogni macchina virtuale](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

   Per installare il clustering di failover dall'interfaccia utente, eseguire questi passaggi in entrambe le macchine virtuali:
   1. In **Server Manager**selezionare **Gestisci**e quindi selezionare **Aggiungi ruoli e funzionalità**.
   1. Nell' **Aggiunta guidata ruoli e funzionalità**selezionare **Avanti** fino a quando non si ottengono le **funzionalità selezionate**.
   1. In **Selezione funzionalità**selezionare **clustering di failover**. Includere tutte le funzionalità necessarie e gli strumenti di gestione. Selezionare **Aggiungi funzionalità**.
   1. Selezionare **Avanti**, quindi fare clic su **fine** per installare le funzionalità.

   Per installare il clustering di failover tramite PowerShell, eseguire lo script seguente da una sessione di PowerShell amministratore in una delle macchine virtuali:

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

Per ulteriori informazioni di riferimento sui passaggi successivi, vedere le istruzioni nel passaggio 3 della [soluzione iperconvergente con spazi di archiviazione diretta in Windows Server 2016](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct).

### <a name="validate-the-cluster"></a>Convalidare il cluster

Convalidare il cluster nell'interfaccia utente o tramite PowerShell.

Per convalidare il cluster usando l'interfaccia utente, seguire questa procedura in una delle macchine virtuali:

1. In **Server Manager**selezionare **strumenti**, quindi **Gestione cluster di failover**.
1. In **Gestione cluster di failover**selezionare **azione**, quindi selezionare **Convalida configurazione**.
1. Selezionare **Avanti**.
1. In **Seleziona server o un cluster**immettere i nomi di entrambe le macchine virtuali.
1. In **Opzioni di test**selezionare **Esegui solo I test selezionati**. Selezionare **Avanti**.
1. In **selezione test**selezionare tutti i test eccetto **archiviazione**, come illustrato di seguito:

   ![Selezionare i test di convalida del cluster](./media/virtual-machines-windows-portal-sql-create-failover-cluster/10-validate-cluster-test.png)

1. Selezionare **Avanti**.
1. In **conferma**selezionare **Avanti**.

La convalida guidata configurazione esegue i test di convalida.

Per convalidare il cluster tramite PowerShell, eseguire lo script seguente da una sessione di PowerShell amministratore in una delle macchine virtuali:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

Dopo aver convalidato il cluster, creare il cluster di failover.

### <a name="create-the-failover-cluster"></a>Creare il cluster di failover

Per creare il cluster di failover è necessario:
- I nomi delle macchine virtuali che diventeranno i nodi del cluster.
- un nome per il cluster di failover
- un indirizzo IP per il cluster di failover. È possibile usare un indirizzo IP non usato nella stessa rete virtuale di Azure e nella stessa subnet dei nodi del cluster.

#### <a name="windows-server-2008-through-windows-server-2016"></a>Windows Server 2008 tramite Windows Server 2016

Lo script di PowerShell seguente crea un cluster di failover per Windows Server 2008 tramite Windows Server 2016. Aggiornare lo script con i nomi dei nodi (i nomi delle macchine virtuali) e un indirizzo IP disponibile dalla rete virtuale di Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

Lo script di PowerShell seguente crea un cluster di failover per Windows Server 2019. Per ulteriori informazioni, vedere [cluster di failover: oggetto di rete cluster](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97). Aggiornare lo script con i nomi dei nodi (i nomi delle macchine virtuali) e un indirizzo IP disponibile dalla rete virtuale di Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness"></a>Creare un cloud di controllo

Cloud Witness è un nuovo tipo di quorum di controllo del cluster archiviato in un BLOB di archiviazione di Azure. In questo modo si elimina la necessità di una macchina virtuale separata che ospita una condivisione del server di controllo.

1. [Creare un cloud di controllo per il cluster di failover](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Creare un contenitore BLOB.

1. Salvare le chiavi di accesso e l'URL del contenitore.

1. Configurare il quorum di controllo del cluster di failover. Vedere [configurare il quorum di controllo nell'interfaccia utente](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness).

### <a name="add-storage"></a>Aggiungi le risorse di archiviazione

I dischi per Spazi di archiviazione diretta devono essere vuoti. Non possono contenere partizioni o altri dati. Per pulire i dischi, attenersi [alla procedura descritta in questa guida](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct?redirectedfrom=MSDN#step-31-clean-drives).

1. [Abilitare spazi di archiviazione diretta](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct).

   Lo script di PowerShell seguente Abilita Spazi di archiviazione diretta:  

   ```powershell
   Enable-ClusterS2D
   ```

   In **Gestione cluster di failover** verrà ora visualizzato il pool di archiviazione.

1. [Creare un volume](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes).

   Spazi di archiviazione diretta crea automaticamente un pool di archiviazione quando lo si Abilita. A questo punto si è pronti per creare un volume. Il cmdlet di PowerShell `New-Volume` automatizza il processo di creazione del volume. Questo processo include la formattazione, l'aggiunta del volume al cluster e la creazione di un Volume condiviso cluster (CSV). Questo esempio crea un file CSV da 800 Gigabyte (GB):

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   Al termine di questo comando, viene montato un volume di 800 GB come risorsa cluster. Il volume si trova in `C:\ClusterStorage\Volume1\`.

   Questa schermata mostra un Volume condiviso cluster con Spazi di archiviazione diretta:

   ![Volume condiviso cluster](./media/virtual-machines-windows-portal-sql-create-failover-cluster/15-cluster-shared-volume.png)

## <a name="step-3-test-failover-cluster-failover"></a>Passaggio 3: Testare il failover del cluster di failover

In **Gestione cluster di failover**verificare che sia possibile spostare la risorsa di archiviazione nell'altro nodo del cluster. Se è possibile connettersi al cluster di failover usando **Gestione cluster di failover** e spostare lo spazio di archiviazione da un nodo all'altro, si è pronti per configurare l'istanza FCI.

## <a name="step-4-create-the-sql-server-fci"></a>Passaggio 4: creare il SQL Server FCI

Dopo aver configurato il cluster di failover e tutti i componenti del cluster, inclusa l'archiviazione, è possibile creare il SQL Server FCI.

1. Connettersi alla prima macchina virtuale tramite RDP.

1. In **Gestione cluster di failover**assicurarsi che tutte le risorse del cluster di base si trovino nella prima macchina virtuale. Se necessario, spostare tutte le risorse in tale macchina virtuale.

1. Individuare i supporti di installazione. Se la macchina virtuale usa una delle immagini di Azure Marketplace, i supporti si trovano in `C:\SQLServer_<version number>_Full`. Selezionare **Imposta**.

1. In **Centro installazione SQL Server**selezionare **installazione**.

1. Selezionare **nuovo SQL Server installazione del cluster di failover**. Seguire le istruzioni della procedura guidata per installare l'istanza del cluster di failover di SQL Server.

   Le directory di dati dell'istanza del cluster di failover devono trovarsi nelle risorse di archiviazione del cluster. Con Spazi di archiviazione diretta, non è un disco condiviso, ma un punto di montaggio a un volume in ogni server. Spazi di archiviazione diretta sincronizza il volume tra entrambi i nodi. Il volume viene presentato al cluster come Volume condiviso cluster. Usare il punto di montaggio del volume condiviso cluster per le directory di dati.

   ![Directory dati](./media/virtual-machines-windows-portal-sql-create-failover-cluster/20-data-dicrectories.png)

1. Dopo aver completato le istruzioni della procedura guidata, il programma di installazione installerà un SQL Server FCI nel primo nodo.

1. Dopo aver installato l'istanza FCI nel primo nodo, connettersi al secondo nodo tramite RDP.

1. Aprire il **Centro installazione SQL Server**. Selezionare **installazione**.

1. Selezionare **Aggiungi nodo a un cluster di failover di SQL Server**. Seguire le istruzioni della procedura guidata per installare SQL Server e aggiungere il server all'istanza del cluster di failover.

   >[!NOTE]
   >Se è stata usata un'immagine della raccolta di Azure Marketplace contenente SQL Server, gli strumenti di SQL Server sono stati inclusi nell'immagine. Se non si usa una di queste immagini, installare gli strumenti di SQL Server separatamente. Vedere [Scaricare SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-5-create-the-azure-load-balancer"></a>Passaggio 5: creare il servizio di bilanciamento del carico di Azure

Nelle macchine virtuali di Azure, per contenere un indirizzo IP che deve trovarsi in un nodo del cluster alla volta viene usato nei cluster un servizio di bilanciamento del carico. In questa soluzione, il servizio di bilanciamento del carico contiene l'indirizzo IP per l'istanza del cluster di failover di SQL Server.

Per altre informazioni, vedere [creare e configurare un](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)servizio di bilanciamento del carico di Azure.

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Creare il servizio di bilanciamento del carico nel portale di Azure

Per creare il servizio di bilanciamento del carico:

1. Nel portale di Azure passare al gruppo di risorse che contiene le macchine virtuali.

1. Selezionare **Aggiungi**. Cerca **Load Balancer**in Azure Marketplace. Selezionare **Load Balancer**.

1. Selezionare **Create** (Crea).

1. Configurare il servizio di bilanciamento del carico con le impostazioni seguenti.

   - **Sottoscrizione**: sottoscrizione di Azure.
   - **Gruppo di risorse**: il gruppo di risorse che contiene le macchine virtuali.
   - **Nome**: un nome che identifichi il servizio di bilanciamento del carico.
   - **Region (area**): il percorso di Azure che contiene le macchine virtuali.
   - **Tipo**: pubblico o privato. È possibile accedere a un servizio di bilanciamento del carico privato dall'interno della rete virtuale. La maggior parte delle applicazioni Azure può usare un servizio di bilanciamento del carico privato. Se l'applicazione deve accedere a SQL Server direttamente su Internet, usare un servizio di bilanciamento del carico pubblico.
   - **SKU**: standard.
   - **Rete virtuale**: la stessa rete delle macchine virtuali.
   - **Assegnazione di indirizzi IP**: statica. 
   - **Indirizzo IP privato**: l'indirizzo IP assegnato alla risorsa di rete cluster FCI SQL Server.

 La schermata seguente illustra l'interfaccia utente di creazione del servizio di **bilanciamento del carico** :

   ![Configurare il servizio di bilanciamento del carico](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)

### <a name="configure-the-load-balancer-backend-pool"></a>Configurare il pool back-end del servizio di bilanciamento del carico

1. Tornare al gruppo di risorse di Azure che contiene le macchine virtuali e individuare il nuovo servizio di bilanciamento del carico. Potrebbe essere necessario aggiornare la visualizzazione nel gruppo di risorse. Selezionare l'istanza di Load Balancer.

1. Selezionare **pool back-end**e quindi fare clic su **Aggiungi**.

1. Associare il pool back-end con il set di disponibilità contenente le macchine virtuali.

1. In **configurazioni IP di rete di destinazione**selezionare **macchina virtuale** e scegliere le macchine virtuali che parteciperanno come nodi del cluster. Assicurarsi di includere tutte le macchine virtuali che ospiteranno l'istanza del cluster di failover.

1. Selezionare **OK** per creare il pool back-end.

### <a name="configure-a-load-balancer-health-probe"></a>Configurare un probe di integrità per il servizio di bilanciamento del carico

1. Nel pannello del servizio di bilanciamento del carico selezionare **Probe di integrità**.

1. Selezionare **Aggiungi**.

1. Nel pannello **Aggiungi Probe integrità** <a name="probe"> </a>impostare i parametri del probe di integrità.

   - **Nome**: nome del probe di integrità.
   - **Protocollo**: TCP.
   - **Porta**: impostare sulla porta creata nel firewall per il probe di integrità in [questo passaggio](#ports). In questo articolo, l'esempio usa la porta TCP `59999`.
   - **Intervallo**: 5 secondi.
   - **Soglia di non integrità**: 2 errori consecutivi.

1. Selezionare **OK**.

### <a name="set-load-balancing-rules"></a>Impostare le regole di bilanciamento del carico

1. Nel pannello del servizio di bilanciamento del carico selezionare **regole di bilanciamento del carico**.

1. Selezionare **Aggiungi**.

1. Impostare i parametri della regola di bilanciamento del carico:

   - **Nome**: nome delle regole di bilanciamento del carico.
   - **Indirizzo IP**front-end: indirizzo IP per la risorsa di rete cluster SQL Server FCI.
   - **Port**: la porta TCP FCI SQL Server. La porta predefinita dell'istanza è 1433.
   - **Porta back-end**: usa la stessa porta del valore di **porta** quando si Abilita l' **indirizzo IP mobile (Direct Server Return)** .
   - **Pool back-end**: il nome del pool back-end configurato in precedenza.
   - **Probe di integrità**: il probe di integrità configurato in precedenza.
   - **Salvataggio permanente sessione**: Nessuno.
   - **Timeout di inattività (minuti)** : 4.
   - **IP mobile (Direct Server Return)** : abilitata.

1. Selezionare **OK**.

## <a name="step-6-configure-the-cluster-for-the-probe"></a>Passaggio 6: configurare il cluster per il probe

Impostare il parametro della porta probe del cluster in PowerShell.

Per impostare il parametro della porta probe del cluster, aggiornare le variabili nello script seguente con i valori dell'ambiente in uso. Rimuovere le parentesi acute (`<` e `>`) dallo script.

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

Nell'elenco seguente vengono descritti i valori che è necessario aggiornare:

   - `<Cluster Network Name>`: il nome del cluster di failover di Windows Server per la rete. In **Gestione cluster di failover** > **reti**, fare clic con il pulsante destro del mouse sulla rete e scegliere **proprietà**. Il valore corretto è riportato in **Nome** nella scheda **Generale**.

   - `<SQL Server FCI IP Address Resource Name>`: il nome della risorsa indirizzo IP dell'istanza del cluster di failover SQL Server. In **Gestione cluster di failover** **ruoli**di > , sotto il SQL Server ruolo FCI, in **nome server**, fare clic con il pulsante destro del mouse sulla risorsa indirizzo IP e scegliere **Proprietà**. Il valore corretto è riportato in **Nome** nella scheda **Generale**. 

   - `<ILBIP>`: indirizzo IP ILB. Questo indirizzo è configurato nel portale di Azure come indirizzo ILB front-end ed è anche l'indirizzo IP dell'istanza del cluster di failover di SQL Server. È riportato in **Gestione cluster di failover** nella stessa pagina delle proprietà in cui si trova `<SQL Server FCI IP Address Resource Name>`.  

   - `<nnnnn>`: la porta Probe configurata nel Probe di integrità del servizio di bilanciamento del carico. È valida qualsiasi porta TCP inutilizzata.

>[!IMPORTANT]
>La subnet mask per il parametro del cluster deve essere l'indirizzo di broadcast IP TCP: `255.255.255.255`.

Dopo aver impostato il probe del cluster, è possibile visualizzare tutti i parametri del cluster in PowerShell. Eseguire questo script:

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-7-test-fci-failover"></a>Passaggio 7: Eseguire il failover dell'istanza del cluster di failover

Testare il failover dell'istanza del cluster di failover per convalidare le funzionalità del cluster. Eseguire questa procedura:

1. Connettersi a uno dei nodi del cluster di SQL Server FCI utilizzando RDP.

1. Aprire **Gestione cluster di failover**. Selezionare **Ruoli**. Rilevare quale nodo è proprietario del ruolo dell'istanza del cluster di failover di SQL Server.

1. Fare clic con il pulsante destro del mouse sul ruolo dell'istanza del cluster di failover di SQL Server.

1. Selezionare **Sposta**, quindi selezionare il **nodo migliore possibile**.

**Gestione cluster di failover** Mostra il ruolo e le relative risorse passano offline. Le risorse vengono quindi spostate e portate online nell'altro nodo.

### <a name="test-connectivity"></a>Testare la connettività

Per testare la connettività, accedere a un'altra macchina virtuale nella stessa rete virtuale. Aprire **SQL Server Management Studio** e connettersi al nome dell'istanza del cluster di failover di SQL Server.

>[!NOTE]
>Se necessario, è possibile [scaricare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="limitations"></a>Limitazioni

Le macchine virtuali di Azure supportano Microsoft Distributed Transaction Coordinator (MSDTC) in Windows Server 2019 con archiviazione su volumi condivisi cluster (CSV) e un servizio di [bilanciamento del carico standard](../../../load-balancer/load-balancer-standard-overview.md).

In macchine virtuali di Azure, MSDTC non è supportato in Windows Server 2016 o versioni precedenti perché:

- Non è possibile configurare la risorsa MSDTC in cluster per usare l'archiviazione condivisa. In Windows Server 2016, se si crea una risorsa MSDTC, non verrà visualizzata alcuna archiviazione condivisa disponibile per l'uso, anche se è disponibile spazio di archiviazione. Questo problema è stato risolto per Windows Server 2019.
- Il servizio di bilanciamento del carico di base non gestisce le porte RPC.

## <a name="see-also"></a>Vedi anche

[Configurare Spazi di archiviazione diretta con desktop remoto (Azure)](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-storage-spaces-direct-deployment)

[Soluzione iperconvergente con Spazi di archiviazione diretta](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct)

[Panoramica di Spazi di archiviazione diretta](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)

[Supporto SQL Server per Spazi di archiviazione diretta](https://blogs.technet.microsoft.com/dataplatforminsider/2016/09/27/sql-server-2016-now-supports-windows-server-2016-storage-spaces-direct/)
