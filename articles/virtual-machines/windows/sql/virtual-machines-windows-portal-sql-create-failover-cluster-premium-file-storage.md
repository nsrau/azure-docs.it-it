---
title: SQL Server FCI con la condivisione file Premium-macchine virtuali di Azure
description: Questo articolo illustra come creare un'istanza del cluster di failover di SQL Server usando una condivisione file Premium in macchine virtuali di Azure.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 9fc761b1-21ad-4d79-bebc-a2f094ec214d
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/09/2019
ms.author: mathoma
ms.openlocfilehash: 39f04005776f3b451ad7c64c76f9aa5d8c4a7768
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330097"
---
# <a name="configure-sql-server-failover-cluster-instance-with-premium-file-share-on-azure-virtual-machines"></a>Configurare SQL Server istanza del cluster di failover con una condivisione file Premium in macchine virtuali di Azure

Questo articolo illustra come creare un'istanza del cluster di failover di SQL Server in macchine virtuali di Azure usando una [condivisione file Premium](../../../storage/files/storage-how-to-create-premium-fileshare.md). 

Le condivisioni file Premium sono condivisioni file con bassa latenza SSD, completamente supportate per l'uso con l'istanza del cluster di failover per SQL Server 2012 e versioni successive in Windows Server 2012 e versioni successive. Le condivisioni file Premium offrono maggiore flessibilità, consentendo di ridimensionare e ridimensionare la condivisione file senza tempi di inattività. 


## <a name="before-you-begin"></a>Prima di iniziare

Prima di procedere, è necessario conoscere alcuni aspetti ed essere in possesso di alcuni elementi.

È necessario avere una conoscenza operativa delle tecnologie seguenti:

- [Tecnologie cluster di Windows](/windows-server/failover-clustering/failover-clustering-overview)
- [Istanze del cluster di failover di SQL Server](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

Una differenza importante è che in un cluster di failover di macchine virtuali IaaS di Azure è consigliabile usare una singola scheda di interfaccia di rete per server (nodo del cluster) e una singola subnet. La rete di Azure ha una ridondanza fisica che rende superflue altre schede di rete e subnet in un cluster guest di macchine virtuali IaaS di Azure. Anche se il report di convalida del cluster emette un avviso che indica che i nodi sono raggiungibili solo in una singola rete, questo avviso può essere ignorato in cluster di failover delle macchine virtuali IaaS di Azure. 

Inoltre, è necessario avere una conoscenza generale delle tecnologie seguenti:

- [Condivisione file Premium di Azure](../../../storage/files/storage-how-to-create-premium-fileshare.md)
- [Gruppi di risorse di Azure](../../../azure-resource-manager/manage-resource-groups-portal.md)

> [!IMPORTANT]
> A questo punto, SQL Server istanze del cluster di failover in macchine virtuali di Azure sono supportate solo con la modalità di gestione [semplice](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider) dell' [estensione dell'agente IaaS SQL Server](virtual-machines-windows-sql-server-agent-extension.md). Disinstallare l'estensione completa dalle macchine virtuali che fanno parte del cluster di failover e quindi registrarle con il provider di risorse VM SQL in modalità `lightweight`. L'estensione completa supporta funzionalità quali il backup automatizzato, l'applicazione di patch e la gestione avanzata del portale. Queste funzionalità non funzioneranno per le macchine virtuali SQL dopo che l'agente è stato reinstallato in modalità di gestione leggera.

### <a name="workload-consideration"></a>Considerazione del carico di lavoro

Le condivisioni file Premium forniscono IOPS e tutta la capacità che soddisferà le esigenze di molti carichi di lavoro. Tuttavia, per i carichi di lavoro con utilizzo intensivo di i/o, prendere in considerazione [SQL Server FCI con spazi di archiviazione diretta](virtual-machines-windows-portal-sql-create-failover-cluster.md) basati su dischi Premium gestiti o dischi rigidi.  

Controllare l'attività IOPS dell'ambiente corrente e verificare che i file Premium forniscano gli IOPS necessari prima di avviare una distribuzione o una migrazione. Utilizzare i contatori dei dischi di performance monitor di Windows e monitorare le operazioni SQL Server di i/o totali (trasferimenti disco/sec) e la velocità effettiva (byte disco/sec) richieste per i file di dati, di log e di database temporaneo. Molti carichi di lavoro hanno picchi di i/o, quindi è consigliabile verificare durante i periodi di utilizzo intensivo e prendere nota del numero massimo di IOPS, oltre che di IOPS medio. Le condivisioni file Premium forniscono IOPS in base alle dimensioni della condivisione. I file Premium forniscono anche un'espansione gratuita in cui è possibile aumentare le operazioni di i/o per triplicare l'importo della linea di base per un massimo di un'ora. 

Per ulteriori informazioni sulle prestazioni di condivisione file Premium, vedere [livelli di prestazioni di condivisione file](https://docs.microsoft.com/en-us/azure/storage/files/storage-files-planning#file-share-performance-tiers). 

### <a name="licensing-and-pricing"></a>Licenze e prezzi

Nelle macchine virtuali di Azure è possibile concedere in licenza SQL Server usando immagini di VM con pagamento in base al consumo (PAYG) o bring your own License (BYOL). Il tipo di immagine selezionato influisce sul modo in cui vengono applicati i costi.

Con le licenze PAYG, un'istanza del cluster di failover (FCI) di SQL Server nelle macchine virtuali di Microsoft Azure comporta costi per tutti i nodi di FCI, inclusi i nodi passivi. Per ulteriori informazioni, consultare le [tariffe delle Macchine virtuali SQL Server Enterprise](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/). 

I clienti con un contratto Enterprise con Software Assurance hanno il diritto di utilizzare un nodo FCI passivo gratuito per ogni nodo attivo. Per sfruttare i vantaggi di questo vantaggio in Azure, usare le immagini di VM BYOL e quindi usare la stessa licenza nei nodi attivi e passivi dell'istanza FCI. Per altre informazioni, consultare il [Contratto Enterprise](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx).

Per confrontare le licenze PAYG e BYOL per SQL Server nelle macchine virtuali di Azure, consultare [Introduzione alle VM di SQL](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms).

Per informazioni complete sulle licenze SQL Server, consultare [Prezzi](https://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="limitations"></a>Limitazioni

- FILESTREAM non è supportato per un cluster di failover con una condivisione file Premium. Per usare FILESTREAM, distribuire il cluster usando [spazi di archiviazione diretta](virtual-machines-windows-portal-sql-create-failover-cluster.md). 

## <a name="prerequisites"></a>Prerequisiti 

Prima di seguire le istruzioni di questo articolo, è necessario avere gli elementi seguenti:

- Una sottoscrizione di Microsoft Azure.
- Un dominio Windows in macchine virtuali di Azure.
- Un account con l'autorizzazione necessaria per creare oggetti nella macchina virtuale di Azure.
- Una rete virtuale di Azure e una subnet con uno spazio indirizzi IP sufficiente per i componenti seguenti:
   - Entrambe le macchine virtuali.
   - Indirizzo IP del cluster di failover.
   - Indirizzo IP per ogni istanza del cluster di failover.
- DNS configurato nella rete di Azure, che punta ai controller di dominio.
- Una [condivisione file Premium](../../../storage/files/storage-how-to-create-premium-fileshare.md) basata sulla quota di archiviazione del database per i file di dati. 
- Una condivisione file per i backup diversa dalla condivisione file Premium utilizzata per i file di dati. Questa condivisione file può essere standard o Premium. 

Dopo aver soddisfatto questi prerequisiti, è possibile procedere con la creazione del cluster di failover. Il primo passaggio consiste nel creare le macchine virtuali.

## <a name="step-1-create-virtual-machines"></a>Passaggio 1: Creare le macchine virtuali

1. Accedere al [portale di Azure](https://portal.azure.com) con la propria sottoscrizione.

1. [Creare un set di disponibilità di Azure](../tutorial-availability-sets.md).

   Un set di disponibilità raggruppa le macchine virtuali in più domini di errore e domini di aggiornamento. Il set di disponibilità garantisce che l'applicazione non sia influenzata da singoli punti di errore, come il commutatore di rete o l'unità di alimentazione di un rack di server.

   Se non è stato creato il gruppo di risorse per le macchine virtuali, crearlo al momento della creazione di un set di disponibilità di Azure. Se si usa il portale di Azure per creare il set di disponibilità, seguire questa procedura:

   - Nel portale di Azure fare clic su **+** per aprire Azure Marketplace. Cercare **Set di disponibilità**.
   - Fare clic su **Set di disponibilità**.
   - Fare clic su **Create**(Crea).
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

   Scegliere un'immagine da Azure Marketplace. È possibile usare un'immagine del Marketplace che include Windows Server e SQL Server o solo Windows Server. Per informazioni dettagliate, vedere [Panoramica di SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md).

   Le immagini ufficiali di SQL Server nella raccolta di Azure includono un'istanza installata di SQL Server, nonché il software di installazione di SQL Server e la chiave necessaria.

   >[!IMPORTANT]
   > Dopo aver creato la macchina virtuale, rimuovere l'istanza autonoma preinstallata di SQL Server. Si utilizzeranno i supporti di SQL Server preinstallati per creare l'istanza FCI di SQL Server dopo aver configurato il cluster di failover e la condivisione file Premium come archiviazione. 

   In alternativa, è possibile usare immagini di Azure Marketplace con il solo sistema operativo. Scegliere un'immagine di **Windows Server 2016 datacenter** e installare l'istanza FCI di SQL Server dopo aver configurato il cluster di failover e la condivisione file Premium come archiviazione. Un'immagine di questo tipo non contiene i supporti di installazione di SQL Server. Posizionare i supporti di installazione in un percorso in cui è possibile eseguire l'installazione di SQL Server per ogni server. 

1. Dopo aver creato le macchine virtuali di Azure, connettersi a ognuna con RDP.

   Quando ci si connette per la prima volta a una macchina virtuale con RDP, il computer chiede se si vuole rendere il PC individuabile sulla rete. Fare clic su **Sì**.

1. Se si usa un'immagine di macchina virtuale basata su SQL Server, rimuovere l'istanza di SQL Server.

   - In **programmi e funzionalità**fare clic con il pulsante destro del mouse su **Microsoft SQL Server 201_ (64 bit)** e scegliere **Disinstalla/Cambia**.
   - Fare clic su **Rimuovi**.
   - Selezionare l'istanza predefinita.
   - Rimuovere tutte le funzionalità in **Servizi motore di database**. Non rimuovere **Funzionalità condivise**. Vedere l'immagine seguente:

      ![Rimuovere le funzionalità](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   - Fare clic su **Avanti** e quindi su **Rimuovi**.

1. <a name="ports"></a>Aprire le porte del firewall.

   In ogni macchina virtuale aprire le porte seguenti in Windows Firewall.

   | Finalità | Porta TCP | Note
   | ------ | ------ | ------
   | SQL Server | 1433 | Porta normale per le istanze predefinite di SQL Server. Se è stata usata un'immagine della raccolta, questa porta è automaticamente aperta.
   | Probe di integrità | 59999 | Qualsiasi porta TCP aperta. In un passaggio successivo, configurare il [probe di integrità](#probe) del servizio di bilanciamento del carico e il cluster per l'uso di questa porta.   
   | Condivisione file | 445 | Porta utilizzata dal servizio Condivisione file. 

1. [Aggiungere le macchine virtuali al dominio preesistente](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

Dopo aver creato e configurato le macchine virtuali, è possibile configurare la condivisione file Premium.

## <a name="step-2-mount-premium-file-share"></a>Passaggio 2: montare la condivisione file Premium

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'account di archiviazione.
1. Passare a **condivisioni file** in **servizio file** e selezionare la condivisione file Premium che si vuole usare per l'archiviazione SQL. 
1. Selezionare **Connetti** per visualizzare la stringa di connessione per la condivisione file. 
1. Selezionare la lettera di unità che si desidera utilizzare dall'elenco a discesa, quindi copiare entrambi i blocchi di codice in un blocco note.

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-storage/premium-file-storage-commands.png" alt-text="Copiare entrambi i comandi di PowerShell dal portale di connessione alla condivisione file":::

1. Eseguire la connessione RDP alla macchina virtuale SQL Server usando l'account che la SQL Server FCI utilizzerà per l'account del servizio. 
1. Avviare una console di comando di PowerShell amministrativa. 
1. Eseguire i comandi dal portale salvato in precedenza. 
1. Passare alla condivisione con Esplora file o la finestra di dialogo **Esegui** (tasto Windows + r) utilizzando il percorso di rete `\\storageaccountname.file.core.windows.net\filesharename`. Esempio: `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`

1. Creare almeno una cartella nella condivisione file appena connessa in cui inserire i file di dati SQL. 
1. Ripetere questi passaggi in ogni macchina virtuale SQL Server che parteciperà al cluster. 

  > [!IMPORTANT]
  > Provare a usare una condivisione file separata per i file di backup per salvare i dati di IOPS e la capacità di ridimensionamento della condivisione per i file di dati e di log. È possibile usare una condivisione file Premium o standard per i file di backup

## <a name="step-3-configure-failover-cluster-with-file-share"></a>Passaggio 3: configurare il cluster di failover con la condivisione file 

Il passaggio successivo consiste nel configurare il cluster di failover. Questo passaggio include i passaggi secondari seguenti:

1. Aggiungere la funzionalità Clustering di failover di Windows
1. Convalidare il cluster
1. Creare il cluster di failover
1. Creare il cloud di controllo


### <a name="add-windows-failover-clustering-feature"></a>Aggiungere la funzionalità Clustering di failover di Windows

1. Per iniziare, connettersi alla prima macchina virtuale con RDP usando un account di dominio che è membro del gruppo degli amministratori locali e ha le autorizzazioni necessarie per creare oggetti in Active Directory. Usare questo account per il resto della configurazione.

1. [Aggiungere la funzionalità Clustering di failover a ogni macchina virtuale](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

   Per installare la funzionalità Clustering di failover dall'interfaccia utente, eseguire questa procedura in entrambe le macchine virtuali.
   - In **Server Manager** fare clic su **Gestione** e quindi su **Aggiungi ruoli e funzionalità**.
   - Nell'**Aggiunta guidata ruoli e funzionalità** fare clic su **Avanti** fino a visualizzare **Selezione funzionalità**.
   - In **Selezione funzionalità** selezionare **Clustering di failover**. Includere tutte le funzionalità necessarie e gli strumenti di gestione. Fare clic su **Aggiungi funzionalità**.
   - Fare clic su **Avanti** e quindi su **Fine** per installare le funzionalità.

   Per installare la funzionalità Clustering di failover con PowerShell, eseguire lo script seguente da una sessione di PowerShell in modalità amministratore in una delle macchine virtuali.

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

### <a name="validate-the-cluster"></a>Convalidare il cluster

Questa guida fa riferimento alle istruzioni riportate nella sezione relativa alla [convalida del cluster](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-31-run-cluster-validation).

Convalidare il cluster nell'interfaccia utente o con PowerShell.

Per convalidare il cluster con l'interfaccia utente, eseguire questa procedura da una delle macchine virtuali.

1. In **Server Manager** fare clic su **Strumenti** e quindi su **Gestione cluster di failover**.
1. In **Gestione cluster di failover** fare clic su **Azione** e quindi su **Convalida configurazione**.
1. Fare clic su **Next** (Avanti).
1. In **Selezione di server o di un cluster** digitare il nome di entrambe le macchine virtuali.
1. In **Opzioni di testing** scegliere **Esegui solo test selezionati**. Fare clic su **Next** (Avanti).
1. In **selezione test**includere tutti i test ad eccezione di **archiviazione** e **spazi di archiviazione diretta**. Vedere l'immagine seguente:

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-storage/cluster-validation.png" alt-text="Test di convalida del cluster":::

1. Fare clic su **Next** (Avanti).
1. In **Conferma** fare clic su **Avanti**.

La **Convalida guidata configurazione** eseguirà i test di convalida.

Per convalidare il cluster con PowerShell, eseguire lo script seguente da una sessione di PowerShell in modalità amministratore in una delle macchine virtuali.

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

Dopo aver convalidato il cluster, creare il cluster di failover.

### <a name="create-the-failover-cluster"></a>Creare il cluster di failover


Per creare il cluster di failover è necessario:
- I nomi delle macchine virtuali che diventeranno i nodi del cluster.
- un nome per il cluster di failover
- un indirizzo IP per il cluster di failover. È possibile usare un indirizzo IP non usato nella stessa rete virtuale di Azure e nella stessa subnet dei nodi del cluster.

#### <a name="windows-server-2012-2016"></a>Windows Server 2012-2016

Il seguente PowerShell crea un cluster di failover per **Windows Server 2012-2016**. Aggiornare lo script con i nomi dei nodi (ossia i nomi delle macchine virtuali) e un indirizzo IP disponibile della rete virtuale di Azure:

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

Il seguente PowerShell crea un cluster di failover per Windows Server 2019.  Per ulteriori informazioni, esaminare il cluster di failover del Blog [: oggetto rete cluster](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97).  Aggiornare lo script con i nomi dei nodi (ossia i nomi delle macchine virtuali) e un indirizzo IP disponibile della rete virtuale di Azure:

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness"></a>Creare un cloud di controllo

Il cloud di controllo è un nuovo tipo di quorum di controllo del cluster archiviato in un BLOB del servizio di archiviazione di Azure. In questo modo si elimina la necessità di una macchina virtuale separata che ospita una condivisione di controllo.

1. [Creare un cloud di controllo per il cluster di failover](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Creare un contenitore BLOB.

1. Salvare le chiavi di accesso e l'URL del contenitore.

1. Configurare il quorum di controllo del cluster di failover. Vedere [Configurare il quorum di controllo nell'interfaccia utente](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness) nell'interfaccia utente.


## <a name="step-4-test-cluster-failover"></a>Passaggio 4: testare il failover del cluster

Test del failover del cluster. In Gestione cluster di failover fare clic con il pulsante destro del mouse sul cluster > **altre azioni** > **spostare la risorsa cluster principale** > **selezionare un nodo** e selezionare l'altro nodo del cluster. Spostare la risorsa cluster principale in ogni nodo del cluster e quindi spostarla di nuovo nel nodo primario. Se si è in grado di spostare correttamente il cluster in ogni nodo, è possibile procedere con l'installazione SQL Server.  

:::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-storage/test-cluster-failover.png" alt-text="Testare il failover del cluster spostando la risorsa principale negli altri nodi":::

## <a name="step-5-create-sql-server-fci"></a>Passaggio 5: creare SQL Server FCI

Dopo aver configurato il cluster di failover, è possibile creare il SQL Server FCI.

1. Connettersi alla prima macchina virtuale con RDP.

1. In **Gestione cluster di failover** verificare che tutte le risorse principali del cluster si trovino nella prima macchina virtuale. Se necessario, spostare tutte le risorse in questa macchina virtuale.

1. Individuare i supporti di installazione. Se la macchina virtuale usa una delle immagini di Azure Marketplace, i supporti si trovano in `C:\SQLServer_<version number>_Full`. Fare clic su **Configura**.

1. In **Centro installazione SQL Server** fare clic su **Installazione**.

1. Fare clic su **Installazione di un nuovo cluster di failover di SQL Server**. Seguire le istruzioni della procedura guidata per installare l'istanza del cluster di failover di SQL Server.

   Le directory dati FCI devono trovarsi nella condivisione file Premium. Digitare il percorso completo della condivisione, nel formato `\\storageaccountname.file.core.windows.net\filesharename\foldername`. Verrà visualizzato un avviso che informa che è stato specificato un file server come directory dei dati. Si tratta di un comportamento previsto. Verificare che lo stesso account con cui è stata resa permanente la condivisione file sia lo stesso account utilizzato dal servizio SQL Server per evitare possibili errori. 

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-storage/use-file-share-as-data-directories.png" alt-text="Usare la condivisione file come directory dati SQL":::

1. Al termine della procedura guidata, verrà installata un'istanza del cluster di failover di SQL Server nel primo nodo.

1. Al termine dell'installazione dell'istanza del cluster di failover nel primo nodo, connettersi al secondo nodo con RDP.

1. Aprire il **Centro installazione SQL Server**. Fare clic su **Installazione**.

1. Fare clic su **Aggiunta di un nodo a un cluster di failover di SQL Server**. Seguire le istruzioni della procedura guidata per installare SQL Server e aggiungere questo server all'istanza del cluster di failover.

   >[!NOTE]
   >Se è stata usata un'immagine della raccolta di Azure Marketplace con SQL Server, gli strumenti di SQL Server sono stati inclusi con l'immagine. In caso contrario, installare gli strumenti di SQL Server separatamente. Vedere [Scaricare SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-6-create-azure-load-balancer"></a>Passaggio 6: creare il servizio di bilanciamento del carico di Azure

Nelle macchine virtuali di Azure, per contenere un indirizzo IP che deve trovarsi in un nodo del cluster alla volta viene usato nei cluster un servizio di bilanciamento del carico. In questa soluzione, il servizio di bilanciamento del carico contiene l'indirizzo IP per l'istanza del cluster di failover di SQL Server.

[Creare e configurare un servizio di bilanciamento del carico di Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Creare il servizio di bilanciamento del carico nel portale di Azure

Per creare il servizio di bilanciamento del carico:

1. Nel portale di Azure passare al gruppo di risorse con le macchine virtuali.

1. Fare clic su **+ Aggiungi**. Cercare **Servizio di bilanciamento del carico** nel Marketplace. Fare clic su **Servizio di bilanciamento del carico**.

1. Fare clic su **Create**(Crea).

1. Configurare il servizio di bilanciamento del carico con le impostazioni seguenti.

   - **Sottoscrizione**: sottoscrizione di Azure.
   - **Gruppo di risorse**: usare lo stesso gruppo di risorse delle macchine virtuali.
   - **Nome**: un nome che identifichi il servizio di bilanciamento del carico.
   - **Area**: usare la stessa località di Azure delle macchine virtuali.
   - **Tipo**: il servizio di bilanciamento del carico può essere pubblico o privato. Un servizio di bilanciamento del carico privato è accessibile dalla stessa rete virtuale. La maggior parte delle applicazioni Azure può usare un servizio di bilanciamento del carico privato. Se l'applicazione deve accedere a SQL Server direttamente su Internet, usare un servizio di bilanciamento del carico pubblico.
   - **SKU**: lo SKU per il servizio di bilanciamento del carico deve essere standard. 
   - **Rete virtuale**: la stessa rete delle macchine virtuali.
   - **Assegnazione di indirizzi IP**: l'assegnazione di indirizzi IP deve essere statica. 
   - **Indirizzo IP privato**: lo stesso indirizzo IP assegnato alla risorsa di rete cluster dell'istanza del cluster di failover di SQL Server.
   Vedere l'immagine seguente:

   ![Creare il servizio di bilanciamento del carico](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)

   ![Creare il servizio di bilanciamento del carico](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)

### <a name="configure-the-load-balancer-backend-pool"></a>Configurare il pool back-end del servizio di bilanciamento del carico

1. Tornare al gruppo di risorse di Azure con le macchine virtuali e individuare il nuovo servizio di bilanciamento del carico. Potrebbe essere necessario aggiornare la visualizzazione del gruppo di risorse. Fare clic sul servizio di bilanciamento del carico.

1. Fare clic su **Pool back-end** e quindi su **+Aggiungi** per aggiungere un pool back-end.

1. Associare il pool back-end con il set di disponibilità contenente le macchine virtuali.

1. In **Configurazioni IP della rete di destinazione** selezionare **MACCHINA VIRTUALE** e scegliere le macchine virtuali che parteciperanno come nodi del cluster. Assicurarsi di includere tutte le macchine virtuali che ospiteranno l'istanza del cluster di failover. 

1. Fare clic su **OK** per creare il pool back-end.

### <a name="configure-a-load-balancer-health-probe"></a>Configurare un probe di integrità per il servizio di bilanciamento del carico

1. Nel pannello del servizio di bilanciamento del carico fare clic su **Probe integrità**.

1. Fare clic su **+ Aggiungi**.

1. Nel pannello **Aggiungi probe integrità** <a name="probe"></a>impostare i parametri del probe di integrità.

   - **Nome**: nome del probe di integrità.
   - **Protocollo**: TCP.
   - **Porta**: impostare sulla porta creata nel firewall per il probe di integrità in [questo passaggio](#ports). In questo articolo viene usata la porta TCP `59999`.
   - **Intervallo**: 5 secondi.
   - **Soglia di non integrità**: 2 errori consecutivi.

1. Fare clic su OK.

### <a name="set-load-balancing-rules"></a>Impostare le regole di bilanciamento del carico

1. Nel pannello del servizio di bilanciamento del carico fare clic su **regole di bilanciamento del carico**.

1. Fare clic su **+ Aggiungi**.

1. Impostare i parametri delle regole di bilanciamento del carico:

   - **Nome**: nome per le regole di bilanciamento del carico.
   - **Indirizzo IP front-end**: usare l'indirizzo IP per la risorsa di rete cluster dell'istanza del cluster di failover di SQL Server.
   - **Porta**: impostare la porta TCP dell'istanza del cluster di failover di SQL Server. La porta predefinita dell'istanza è 1433.
   - **Porta back-end**: per questo valore viene usata la stessa porta inserita come valore in **Porta** quando si abilita **IP mobile (Direct Server Return)** .
   - **Pool back-end**: usare il nome del pool back-end configurato in precedenza.
   - **Probe integrità**: usare il probe di integrità configurato in precedenza.
   - **Salvataggio permanente sessione**: Nessuno.
   - **Timeout di inattività (minuti)** : 4.
   - **IP mobile (Direct Server Return)** : Abilitato.

1. Fare clic su **OK**.

## <a name="step-7-configure-cluster-for-probe"></a>Passaggio 7: configurare il cluster per il probe

Impostare il parametro della porta probe del cluster in PowerShell.

Per impostare tale parametro, aggiornare le variabili nello script seguente con i valori dell'ambiente in uso. Rimuovere le parentesi acute `<>` dallo script. 

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

Nello script precedente impostare i valori per l'ambiente in uso. L'elenco seguente offre una descrizione dei valori.

   - `<Cluster Network Name>`: nome del cluster di failover di Windows Server per la rete. In **Gestione cluster di failover** > **Reti** fare clic con il pulsante destro del mouse sulla rete e scegliere **Proprietà**. Il valore corretto è riportato in **Nome** nella scheda **Generale**. 

   - `<SQL Server FCI IP Address Resource Name>`: nome della risorsa indirizzo IP dell'istanza del cluster di failover di SQL Server. In **Gestione cluster di failover** **ruoli** > , sotto il SQL Server ruolo FCI, in **nome server**, fare clic con il pulsante destro del mouse sulla risorsa indirizzo IP e scegliere **Proprietà**. Il valore corretto è riportato in **Nome** nella scheda **Generale**. 

   - `<ILBIP>`: indirizzo IP ILB. Questo indirizzo è configurato nel portale di Azure come indirizzo ILB front-end ed è anche l'indirizzo IP dell'istanza del cluster di failover di SQL Server. È riportato in **Gestione cluster di failover** nella stessa pagina delle proprietà in cui si trova `<SQL Server FCI IP Address Resource Name>`.  

   - `<nnnnn>`: porta probe configurata nel probe di integrità del servizio di bilanciamento del carico. È valida qualsiasi porta TCP inutilizzata. 

>[!IMPORTANT]
>La subnet mask per il parametro del cluster deve essere l'indirizzo di broadcast IP TCP: `255.255.255.255`.

Dopo aver impostato il probe del cluster, è possibile visualizzare tutti i parametri del cluster in PowerShell. Eseguire lo script seguente:

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-8-test-fci-failover"></a>Passaggio 8: testare il failover dell'istanza FCI

Testare il failover dell'istanza del cluster di failover per convalidare le funzionalità del cluster. Seguire anche questa procedura:

1. Connettersi a un nodo del cluster dell'istanza del cluster di failover di SQL Server con RDP.

1. Aprire **Gestione cluster di failover**. Fare clic su **Ruoli**. Rilevare quale nodo è proprietario del ruolo dell'istanza del cluster di failover di SQL Server.

1. Fare clic con il pulsante destro del mouse sul ruolo dell'istanza del cluster di failover di SQL Server.

1. Scegliere **Sposta** e quindi fare clic su **Miglior nodo possibile**.

In **Gestione cluster di failover** viene visualizzato il ruolo e le relative risorse passano alla modalità offline. Le risorse vengono quindi spostate e portate online nell'altro nodo.

### <a name="test-connectivity"></a>Testare la connettività

Per testare la connettività, accedere a un'altra macchina virtuale nella stessa rete virtuale. Aprire **SQL Server Management Studio** e connettersi al nome dell'istanza del cluster di failover di SQL Server.

>[!NOTE]
>Se necessario, è possibile [scaricare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="limitations"></a>Limitazioni

Macchine virtuali di Azure supporta Microsoft Distributed Transaction Coordinator (MSDTC) in Windows Server 2019 con archiviazione in volumi condivisi cluster (CSV) e un [bilanciamento del carico standard](../../../load-balancer/load-balancer-standard-overview.md).

Nelle macchine virtuali di Azure, MSDTC non è supportato in Windows Server 2016 e versioni precedenti perché:

- La risorsa MSDTC cluster non può essere configurata per usare l'archiviazione condivisa. Con Windows Server 2016 se si crea una risorsa MSDTC, non verrà visualizzata qualsiasi archiviazione condivisa disponibile, anche se lo spazio di archiviazione è disponibile. Questo problema è stato risolto per Windows Server 2019.
- Il servizio di bilanciamento del carico di base non gestisce le porte RPC.

## <a name="see-also"></a>Vedere anche

- [Tecnologie cluster di Windows](/windows-server/failover-clustering/failover-clustering-overview)
- [Istanze del cluster di failover di SQL Server](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
