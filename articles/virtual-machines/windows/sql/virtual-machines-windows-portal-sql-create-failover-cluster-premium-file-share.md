---
title: FCI di SQL Server con condivisione file premium - Macchine virtuali di Azure
description: Questo articolo illustra come creare un'istanza del cluster di failover di SQL Server usando una condivisione file premium nelle macchine virtuali di Azure.This article explains how to create a SQL Server failover cluster instance by using a premium file share on Azure virtual machines.
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
ms.openlocfilehash: 9595ee87801fa4ce187a50197fc58d6c448eac24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303223"
---
# <a name="configure-a-sql-server-failover-cluster-instance-with-premium-file-share-on-azure-virtual-machines"></a>Configurare un'istanza del cluster di failover di SQL Server con condivisione file premium nelle macchine virtuali di AzureConfigure a SQL Server failover cluster instance with premium file share on Azure virtual machines

In questo articolo viene illustrato come creare un'istanza del cluster di failover di SQL Server nelle macchine virtuali di Azure usando una [condivisione file Premium.](../../../storage/files/storage-how-to-create-premium-fileshare.md)

Le condivisioni file Premium sono condivisioni file con supporto SSD e costantemente a bassa latenza, completamente supportate per l'utilizzo con istanze del cluster di failover per SQL Server 2012 o versioni successive in Windows Server 2012 o versioni successive. Le condivisioni file Premium offrono una maggiore flessibilità, consentendo di ridimensionare e ridimensionare una condivisione file senza tempi di inattività.


## <a name="before-you-begin"></a>Prima di iniziare

Ci sono alcune cose che devi sapere e avere sul posto prima di iniziare.

È necessario avere una comprensione operativa di queste tecnologie:You should have an operational understanding of these technologies:

- [Tecnologie cluster di Windows](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server Failover Cluster Instances](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

Una cosa da tenere presente è che in un cluster di failover della macchina virtuale di Azure IaaS è consigliabile una singola scheda di interfaccia di rete per ogni server (nodo del cluster) e una singola subnet. La rete di Azure include una ridondanza fisica che rende superflue le schede di interfaccia di rete e le subnet aggiuntive in un cluster guest vm di Azure IaaS.Azure networking has physical redundancy that makes additional NICs and subnets unnecessary on an Azure IaaS VM guest cluster. Il report di convalida del cluster avviserà l'utente che i nodi sono raggiungibili solo su una singola rete. È possibile ignorare questo avviso nei cluster di failover delle macchine virtuali di Azure IaaS.You can ignore this warning on Azure IaaS VM failover clusters.

Si dovrebbe anche avere una comprensione generale di queste tecnologie:

- [Condivisione file Premium di AzureAzure premium file share](../../../storage/files/storage-how-to-create-premium-fileshare.md)
- [Gruppi di risorse di Azure](../../../azure-resource-manager/management/manage-resource-groups-portal.md)

> [!IMPORTANT]
> Al momento, le istanze del cluster di failover di SQL Server nelle macchine virtuali di Azure sono supportate solo con la [modalità](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes) di gestione leggera dell'estensione [agente IaaS](virtual-machines-windows-sql-server-agent-extension.md)di SQL Server. Per passare dalla modalità di estensione completa a quella leggera, eliminare la risorsa **macchina virtuale SQL** per le macchine virtuali corrispondenti e quindi registrarle con il provider di risorse della macchina virtuale SQL in modalità leggera. Quando si elimina la risorsa **macchina virtuale SQL** tramite il portale di Azure, **deselezionare la casella di controllo accanto alla macchina virtuale corretta.** L'estensione completa supporta funzionalità quali il backup automatico, l'applicazione di patch e la gestione avanzata del portale. Queste funzionalità non funzioneranno per le macchine virtuali SQL dopo la reinstallazione dell'agente in modalità di gestione leggera.

Le condivisioni file Premium forniscono operazioni di I/O al secondo e capacità che soddisferanno le esigenze di molti carichi di lavoro. Per carichi di lavoro che richiedono un utilizzo intensivo di I/O, prendere in considerazione le istanze del cluster di failover di SQL Server con spazi di [archiviazione diretti,](virtual-machines-windows-portal-sql-create-failover-cluster.md)basati su dischi Premium gestiti o dischi ultra.  

Controllare l'attività di IOPS dell'ambiente e verificare che le condivisioni file premium forniscano le operazioni di I/O al secondo necessarie prima di avviare una distribuzione o una migrazione. Utilizzare i contatori del disco di Performance Monitor di Windows per monitorare il numero totale di operazioni di I/O al secondo (trasferimenti disco/secondo) necessarie per i file di dati, log e database temporaneo di SQL Server.

Molti carichi di lavoro hanno un'i/O burst, pertanto è consigliabile controllare durante i periodi di utilizzo intenso e prendere nota sia del numero massimo di operazioni di I/O al secondo che delle operazioni di I/O al secondo medie. Le condivisioni file Premium forniscono operazioni di I/O al secondo in base alle dimensioni della condivisione. Le condivisioni file Premium forniscono anche bursting gratuito che consente di scoppiare il vostro IO per triplicare l'importo di base per un massimo di un'ora.

Per ulteriori informazioni sulle prestazioni della condivisione file premium, vedere [Livelli di prestazioni della condivisione file](https://docs.microsoft.com/azure/storage/files/storage-files-planning#storage-tiers).

### <a name="licensing-and-pricing"></a>Licenze e prezzi

Nelle macchine virtuali di Azure è possibile concedere in licenza SQL Server usando immagini di macchine virtuali con pagamento in base al client (PAYG) o BYOL (Bring Your Own License). Il tipo di immagine che scegli influisce sulla modalità di addebito.

Con le licenze con pagamento in base al costo, un'istanza del cluster di failover (FCI) di SQL Server nelle macchine virtuali di Azure comporta costi per tutti i nodi dell'istanza fIS, inclusi i nodi passivi. Per ulteriori informazioni, consultare le [tariffe delle Macchine virtuali SQL Server Enterprise](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/).

Se si dispone di Enterprise Agreement con Software Assurance, è possibile utilizzare un nodo FCI passivo libero per ogni nodo attivo. Per sfruttare questo vantaggio in Azure, usare immagini di macchine virtuali BYOL e usare la stessa licenza sia sui nodi attivi che passivi dell'fCI. Per altre informazioni, consultare il [Contratto Enterprise](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx).

Per confrontare le licenze con pagamento in base al pagamento e BYOL per SQL Server in macchine virtuali di Azure, vedere [Introduzione alle macchine virtuali SQL.](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms)

Per informazioni complete sulle licenze SQL Server, consultare [Prezzi](https://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="filestream"></a>Filestream

Filestream non è supportato per un cluster di failover con una condivisione file premium. Per utilizzare filestream, distribuire il cluster utilizzando [Spazi di archiviazione diretta](virtual-machines-windows-portal-sql-create-failover-cluster.md).

## <a name="prerequisites"></a>Prerequisiti

Prima di completare la procedura descritta in questo articolo, è necessario disporre già di:Before you complete the steps in this article, you should already have:

- Una sottoscrizione di Microsoft Azure.
- Un dominio Windows in macchine virtuali di Azure.
- Un account utente di dominio che dispone delle autorizzazioni per creare oggetti sia nelle macchine virtuali di Azure che in Active Directory.
- Un account utente di dominio per eseguire il servizio SQL Server e che è possibile accedere alla macchina virtuale con durante il montaggio della condivisione file.  
- Una rete virtuale di Azure e una subnet con spazio di indirizzi IP sufficiente per questi componenti:An Azure virtual network and subnet with enough IP address space for these components:
   - Due macchine virtuali.
   - Indirizzo IP del cluster di failover.
   - Indirizzo IP per ogni istanza del cluster di failover.
- DNS configurato nella rete di Azure, che punta ai controller di dominio.
- Una [condivisione file premium](../../../storage/files/storage-how-to-create-premium-fileshare.md) da utilizzare come unità cluster, in base alla quota di archiviazione del database per i file di dati.
- Se si usa Windows Server 2012 R2 e versioni precedenti, è necessaria un'altra condivisione file da utilizzare come controllo di condivisione file, poiché i testimoni cloud sono supportati per Windows 2016 e versioni precedenti. È possibile usare un'altra condivisione file di Azure oppure una condivisione file in una macchina virtuale separata. Se si intende usare un'altra condivisione file di Azure, è possibile montarla con lo stesso processo usato per la condivisione file Premium usata per l'unità cluster. 

Con questi prerequisiti, è possibile iniziare a creare il cluster di failover. Il primo passaggio consiste nel creare le macchine virtuali.

## <a name="step-1-create-the-virtual-machines"></a>Passaggio 1: Creare le macchine virtualiStep 1: Create the virtual machines

1. Accedere al [portale](https://portal.azure.com) di Azure con la sottoscrizione.

1. [Creare un set di disponibilità di Azure](../tutorial-availability-sets.md).

   Un set di disponibilità raggruppa le macchine virtuali in più domini di errore e domini di aggiornamento. Garantisce che l'applicazione non sia interessata da singoli punti di errore, ad esempio lo switch di rete o l'unità di alimentazione di un rack di server.

   Se non è stato creato il gruppo di risorse per le macchine virtuali, eseguire questa operazione quando si crea un set di disponibilità di Azure.If you haven't created the resource group for your virtual machines, do it when you create an Azure availability set. Se si usa il portale di Azure per creare il set di disponibilità, eseguire la procedura seguente:If you're using the Azure portal to create the availability set, take these steps:

   1. Nel portale di Azure selezionare **Crea una risorsa** per aprire Azure Marketplace.In the Azure portal, select Create a resource to open Azure Marketplace. Cercare **Set di disponibilità**.
   1. Selezionare **Set di disponibilità**.
   1. Selezionare **Crea**.
   1. In Crea set di disponibilità specificare i valori seguenti:Under **Create availability set**, provide these values:
      - **Nome**: nome del set di disponibilità.
      - **Sottoscrizione:** sottoscrizione di Azure.Subscription : Your Azure subscription.
      - Gruppo di **risorse:** se si vuole usare un gruppo esistente, fare clic su **Seleziona esistente** e quindi selezionare il gruppo dall'elenco. In caso contrario, selezionare **Crea nuovo** e immettere un nome per il gruppo.
      - **Località**: impostare la località in cui si intende creare le macchine virtuali.
      - **Domini di errore**: utilizzare il valore predefinito (**3**).
      - **Update domains**: Utilizza il valore predefinito (**5**).
   1. Selezionare **Crea** per creare il set di disponibilità.

1. Creare le macchine virtuali nel set di disponibilità.

   Effettuare il provisioning di due macchine virtuali di SQL Server nel set di disponibilità di Azure. Per istruzioni, vedere [Effettuare il provisioning di una macchina virtuale di SQL Server nel portale di Azure](virtual-machines-windows-portal-sql-server-provision.md).

   Inserire entrambe le macchine virtuali:

   - Nello stesso gruppo di risorse di Azure del set di disponibilità.
   - Nella stessa rete del controller di dominio.
   - In una subnet con spazio di indirizzi IP sufficiente per le macchine virtuali e per tutte le impendii che potrebbero essere usate nel cluster.
   - Nel set di disponibilità di Azure.

      >[!IMPORTANT]
      >Non è possibile impostare o modificare il set di disponibilità dopo aver creato una macchina virtuale.

   Scegliere un'immagine da Azure Marketplace.Choose an image from Azure Marketplace. È possibile usare un'immagine di Azure Marketplace che include Windows Server e SQL Server oppure usarne una che includa solo Windows Server.You can use an Azure Marketplace image that includes Windows Server and SQL Server, or one that just includes Windows Server. Per informazioni dettagliate, vedere [Panoramica di SQL Server nelle macchine virtuali](virtual-machines-windows-sql-server-iaas-overview.md)di Azure.

   Le immagini ufficiali di SQL Server nella raccolta di Azure includono un'istanza di SQL Server installata, il software di installazione di SQL Server e la chiave necessaria.

   >[!IMPORTANT]
   > Dopo aver creato la macchina virtuale, rimuovere l'istanza autonoma preinstallata di SQL Server. Si utilizzerà il supporto di SQL Server preinstallato per creare l'applicazione file di autenticazione fCI di SQL Server dopo aver configurato il cluster di failover e la condivisione file premium come archiviazione.

   In alternativa, è possibile usare immagini di Azure Marketplace che contengono solo il sistema operativo. Scegliere un'immagine di **Windows Server 2016 Datacenter** e installare l'applicazione file FCI di SQL Server dopo aver configurato il cluster di failover e la condivisione file premium come archiviazione. Questa immagine non contiene supporti di installazione di SQL Server.This image doesn't contain SQL Server installation media. Inserire il supporto di installazione di SQL Server in un percorso in cui è possibile eseguirlo per ogni server.

1. Dopo aver creato le macchine virtuali in Azure, connettersi a ognuna di esse tramite RDP.

   Quando ci si connette per la prima volta a una macchina virtuale tramite RDP, viene richiesto se si desidera consentire al PC di essere individuabile in rete. Selezionare **Sì**.

1. Se si usa una delle immagini della macchina virtuale basata su SQL Server, rimuovere l'istanza di SQL Server.

   1. In **Programmi e funzionalità**fare clic con il pulsante destro del mouse su Microsoft SQL Server **201_ (64 bit)** e scegliere **Disinstalla/Cambia**.
   1. Selezionare **Rimuovi**.
   1. Selezionare l'istanza predefinita.
   1. Rimuovere tutte le funzionalità in **Servizi motore di database**. Non rimuovere **le funzioni condivise**. Vedrai qualcosa di simile alla seguente schermata:

        ![Seleziona caratteristiche](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   1. Selezionare **Avanti**, quindi **Rimuovi**.

1. <span id="ports"> </span> Aprire le porte del firewall.  

   On each virtual machine, open these ports on the Windows Firewall:

   | Scopo | Porta TCP | Note
   | ------ | ------ | ------
   | SQL Server | 1433 | Porta normale per le istanze predefinite di SQL Server. Se è stata usata un'immagine della raccolta, questa porta è automaticamente aperta.
   | Probe di integrità | 59999 | Qualsiasi porta TCP aperta. In un passaggio successivo, configurare il [probe di integrità](#probe) del servizio di bilanciamento del carico e il cluster per l'uso di questa porta.
   | Condivisione file | 445 | Porta utilizzata dal servizio di condivisione file.

1. [Aggiungere le macchine virtuali al dominio preesistente](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

Dopo aver creato e configurato le macchine virtuali, è possibile configurare la condivisione file premium.

## <a name="step-2-mount-the-premium-file-share"></a>Passaggio 2: Montare la condivisione file premiumStep 2: Mount the premium file share

1. Accedere al [portale](https://portal.azure.com) di Azure e passare all'account di archiviazione.
1. Passare a **Condivisioni file** in **Servizio file** e selezionare la condivisione file Premium che si vuole usare per l'archiviazione SQL.
1. Selezionare **Connetti** per visualizzare la stringa di connessione per la condivisione file.
1. Selezionare la lettera di unità che si desidera utilizzare dall'elenco a discesa e quindi copiare entrambi i blocchi di codice nel Blocco note.


   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/premium-file-storage-commands.png" alt-text="Copiare entrambi i comandi di PowerShell dal portale di connessione di condivisione fileCopy both PowerShell commands from the file share connect portal":::

1. Usare RDP per connettersi alla macchina virtuale di SQL Server con l'account che verrà usato dall'autenticazione fCI di SQL Server per l'account del servizio.
1. Aprire una console dei comandi di PowerShell amministrativa.
1. Eseguire i comandi salvati in precedenza mentre si lavora nel portale.
1. Passare alla condivisione utilizzando Esplora file o la finestra di dialogo **Esegui** (tasto WINDOWS e r). Utilizzare il `\\storageaccountname.file.core.windows.net\filesharename`percorso di rete . Ad esempio, usare `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`

1. Creare almeno una cartella nella condivisione file appena connessa in cui inserire i file di dati SQL.
1. Ripetere questi passaggi in ogni macchina virtuale di SQL Server che farà parte del cluster.

  > [!IMPORTANT]
  > - È consigliabile usare una condivisione file separata per i file di backup per salvare le operazioni di I/O al secondo e la capacità di spazio di questa condivisione per i file di dati e di log. È possibile utilizzare una condivisione file premium o standard per i file di backup.
  > - Se si utilizza Windows 2012 R2 e versioni precedenti, seguire la stessa procedura per montare la condivisione file che si intende utilizzare come controllo di condivisione file. 

## <a name="step-3-configure-the-failover-cluster"></a>Passaggio 3: Configurare il cluster di failoverStep 3: Configure the failover cluster

Il passaggio successivo consiste nel configurare il cluster di failover. In questo passaggio verranno completati i passaggi secondari seguenti:In this step, you'll complete the following substeps:

1. Aggiungere la funzionalità Clustering di failover di Windows Server.Add the Windows Server Failover Clustering feature.
1. Convalidare il cluster.
1. Creare il cluster di failover.
1. Creare il cloud di controllo (per Windows Server 2016 e versioni precedenti) o il controllo di condivisione file (per Windows Server 2012 R2 e versioni precedenti).


### <a name="add-windows-server-failover-clustering"></a>Aggiungere il clustering di failover di Windows ServerAdd Windows Server Failover Clustering

1. Connettersi alla prima macchina virtuale con RDP utilizzando un account di dominio membro degli amministratori locali e che dispone dell'autorizzazione per creare oggetti in Active Directory. Usare questo account per il resto della configurazione.

1. [Aggiungere Clustering di failover a ogni macchina virtuale.](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms)

   Per installare Clustering di failover dall'interfaccia utente, eseguire la procedura seguente in entrambe le macchine virtuali:To install Failover Clustering from the UI, take these steps on both virtual machines:
   1. In **Server Manager**selezionare **Gestisci**, quindi Aggiungi ruoli **e funzionalità**.
   1. **Nell'Aggiunta guidata ruoli e funzionalità**selezionare **Avanti** finché non si arriva a **Selezione funzionalità**.
   1. In **Selezione funzionalità**selezionare **Clustering di failover**. Includere tutte le funzionalità necessarie e gli strumenti di gestione. Selezionare **Aggiungi funzionalità**.
   1. Selezionare **Avanti**, quindi **Fine** per installare le funzionalità.

   Per installare Clustering di failover tramite PowerShell, eseguire lo script seguente da una sessione di PowerShell di amministratore in una delle macchine virtuali:

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

### <a name="validate-the-cluster"></a>Convalidare il cluster

Convalidare il cluster nell'interfaccia utente o tramite PowerShell.Validate the cluster in the UI or by using PowerShell.

Per convalidare il cluster tramite l'interfaccia utente, eseguire la procedura seguente in una delle macchine virtuali:

1. In **Server Manager**selezionare **Strumenti**e quindi Gestione cluster **di failover**.
1. In **Gestione cluster di failover**selezionare **Azione**e quindi Convalida **configurazione**.
1. Fare clic su **Avanti**.
1. In **Seleziona server o un cluster**immettere i nomi di entrambe le macchine virtuali.
1. In **Opzioni di test**selezionare Esegui solo test **selezionare**. Fare clic su **Avanti**.
1. In **Selezione test**selezionare tutti i test ad **eccezione di Archiviazione** e Spazi di archiviazione **diretti**, come illustrato di seguito:

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/cluster-validation.png" alt-text="Selezionare i test di convalida del cluster":::

1. Fare clic su **Avanti**.
1. In **Conferma**selezionare **Avanti**.

La **Convalida guidata configurazione** eseguirà i test di convalida.

Per convalidare il cluster tramite PowerShell, eseguire lo script seguente da una sessione di PowerShell di amministratore in una delle macchine virtuali:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

Dopo aver convalidato il cluster, creare il cluster di failover.

### <a name="create-the-failover-cluster"></a>Creare il cluster di failover

Per creare il cluster di failover è necessario:
- Nomi delle macchine virtuali che diventeranno i nodi del cluster.
- un nome per il cluster di failover
- un indirizzo IP per il cluster di failover. È possibile usare un indirizzo IP non usato nella stessa rete virtuale di Azure e nella stessa subnet dei nodi del cluster.

#### <a name="windows-server-2012-through-windows-server-2016"></a>Da Windows Server 2012 a Windows Server 2016

Lo script di PowerShell seguente crea un cluster di failover per Windows Server 2012 fino a Windows Server 2016.The following PowerShell script creates a failover cluster for Windows Server 2012 through Windows Server 2016. Aggiornare lo script con i nomi dei nodi (i nomi delle macchine virtuali) e un indirizzo IP disponibile dalla rete virtuale di Azure.Update the script with the names of the nodes (the virtual machine names) and an available IP address from the Azure virtual network.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

Lo script di PowerShell seguente crea un cluster di failover per Windows Server 2019.The following PowerShell script creates a failover cluster for Windows Server 2019. Per ulteriori informazioni, vedere [Cluster di failover: oggetto Rete cluster](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97). Aggiornare lo script con i nomi dei nodi (i nomi delle macchine virtuali) e un indirizzo IP disponibile dalla rete virtuale di Azure.Update the script with the names of the nodes (the virtual machine names) and an available IP address from the Azure virtual network.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness-win-2016-"></a>Creare un cloud di controllo (Win 2016 )

Se si è in Windows Server 2016 e windows, è necessario creare un cloud di controllo. Cloud di controllo è un nuovo tipo di controllo quorum del cluster archiviato in un BLOB di archiviazione di Azure.Cloud Witness is a new type of cluster quorum witness that's stored in an Azure storage blob. In questo modo si elimina la necessità di una macchina virtuale separata che ospita una condivisione di controllo o usando una condivisione file separata.

1. [Creare un cloud di controllo per il cluster di failover](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Creare un contenitore BLOB.

1. Salvare le chiavi di accesso e l'URL del contenitore.

### <a name="configure-quorum"></a>Configurare quorum 

Per Windows Server 2016 e versione successiva, configurare il cluster per l'utilizzo del cloud di controllo appena creato. Seguire tutti i passaggi [di configurare il quorum di controllo nell'interfaccia utente](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness).

Per Windows Server 2012 R2 e versioni precedenti, seguire la stessa procedura descritta in [Configurare il quorum di controllo nell'interfaccia utente,](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness) ma nella pagina **Seleziona controllo quorum** selezionare l'opzione Configura un controllo di **condivisione file.** Specificare la condivisione file allocata come controllo di condivisione file, che si tratti di una macchina virtuale separata o di un controllo di Azure.Specify the file share you allocated to be the file share witness, whether it's one you configured on a separate virtual machine, or mounted from Azure. 


## <a name="step-4-test-cluster-failover"></a>Passaggio 4: Testare il failover del clusterStep 4: Test cluster failover

Testare il failover del cluster. In **Gestione cluster di failover**fare clic con il pulsante destro del mouse sul cluster e scegliere Altre **azioni** > Sposta nodo di**selezione****risorse** > cluster di base , quindi selezionare l'altro nodo del cluster. Spostare la risorsa cluster principale in ogni nodo del cluster e quindi spostarla nuovamente nel nodo primario. Se è possibile spostare correttamente il cluster in ogni nodo, è possibile installare SQL Server.If you can successfully move the cluster to each node, you're ready to install SQL Server.  

:::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/test-cluster-failover.png" alt-text="Testare il failover del cluster spostando la risorsa di base negli altri nodiTest cluster failover by moving the core resource to the other nodes":::

## <a name="step-5-create-the-sql-server-fci"></a>Passaggio 5: Creare l'autenticazione FCI di SQL ServerStep 5: Create the SQL Server FCI

Dopo aver configurato il cluster di failover, è possibile creare l'autenticazione FCI di SQL Server.After you've configured the failover cluster, you can create the SQL Server FCI.

1. Connettersi alla prima macchina virtuale utilizzando RDP.

1. In **Gestione cluster di failover**verificare che tutte le risorse cluster di base si trovino nella prima macchina virtuale. Se necessario, spostare tutte le risorse in questa macchina virtuale.

1. Individuare i supporti di installazione. Se la macchina virtuale usa una delle immagini di Azure Marketplace, i supporti si trovano in `C:\SQLServer_<version number>_Full`. Selezionare **Imposta**.

1. Nel **Centro installazione SQL Server**selezionare **Installazione**.

1. Selezionare **Nuova installazione cluster**di failover di SQL Server . Seguire le istruzioni della procedura guidata per installare l'istanza del cluster di failover di SQL Server.

   Le directory dei dati FCI devono essere nella condivisione file premium. Immettere il percorso completo della condivisione, nel formato seguente: `\\storageaccountname.file.core.windows.net\filesharename\foldername`. Verrà visualizzato un avviso che informa che è stato specificato un file server come directory dei dati. Si tratta di un comportamento previsto. Verificare che l'account utente RDP'd nella macchina virtuale con quando è stata resa persistente la condivisione file è lo stesso account utilizzato dal servizio SQL Server per evitare possibili errori.

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/use-file-share-as-data-directories.png" alt-text="Utilizzare la condivisione file come directory di dati SQLUse file share as SQL data directories":::

1. Dopo aver completato i passaggi della procedura guidata, il programma di installazione installerà un'applicazione file di autenticazione fCI di SQL Server nel primo nodo.

1. Dopo aver installato l'fCI nel primo nodo, connettersi al secondo nodo utilizzando RDP.

1. Aprire il **Centro installazione SQL Server**. Selezionare **Installazione**.

1. Selezionare Aggiungi nodo a un cluster di failover di **SQL ServerSQL Server**. Seguire le istruzioni della procedura guidata per installare SQL Server e aggiungere il server all'oggetto FCI.

   >[!NOTE]
   >Se è stata usata un'immagine della raccolta di Azure Marketplace con SQL Server, gli strumenti di SQL Server sono stati inclusi con l'immagine. Se non si usa una di queste immagini, installare gli strumenti di SQL Server separatamente. Vedere [Scaricare SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-6-create-the-azure-load-balancer"></a>Passaggio 6: Creare il servizio di bilanciamento del carico di AzureStep 6: Create the Azure load balancer

Nelle macchine virtuali di Azure, per contenere un indirizzo IP che deve trovarsi in un nodo del cluster alla volta viene usato nei cluster un servizio di bilanciamento del carico. In questa soluzione, il servizio di bilanciamento del carico contiene l'indirizzo IP per l'istanza del cluster di failover di SQL Server.

Per altre informazioni, vedere Creare e configurare un servizio di bilanciamento del carico di Azure.For more information, see [Create and configure an Azure load balancer.](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Creare il servizio di bilanciamento del carico nel portale di Azure

Per creare il servizio di bilanciamento del carico:

1. Nel portale di Azure passare al gruppo di risorse che contiene le macchine virtuali.

1. Selezionare **Aggiungi**. Cercare **Bilanciamento del carico**in Azure Marketplace. Selezionare **Load Balancer**.

1. Selezionare **Crea**.

1. Configurare il servizio di bilanciamento del carico usando i valori seguenti:Set up the load balancer by using the following values:

   - **Sottoscrizione:** sottoscrizione di Azure.Subscription : Your Azure subscription.
   - **Gruppo**di risorse: il gruppo di risorse che contiene le macchine virtuali.
   - **Nome**: un nome che identifichi il servizio di bilanciamento del carico.
   - **Area:** il percorso di Azure che contiene le macchine virtuali.
   - **Tipo**: pubblico o privato. È possibile accedere a un servizio di bilanciamento del carico privato dall'interno della rete virtuale. La maggior parte delle applicazioni Azure può usare un servizio di bilanciamento del carico privato. Se l'applicazione deve accedere a SQL Server direttamente tramite Internet, usare un servizio di bilanciamento del carico pubblico.
   - **SKU**: Standard.
   - **Rete virtuale:** la stessa rete delle macchine virtuali.
   - **Assegnazione indirizzo IP**: Statica. 
   - **Private IP address (Indirizzo IP privato):** l'indirizzo IP assegnato alla risorsa di rete cluster FCI di SQL Server.

   L'immagine seguente mostra l'interfaccia utente **di Crea bilanciamento del carico:The** following image shows the Create load balancer UI:

   ![Configurare il servizio di bilanciamento del caricoSet up the load balancer](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)
   

### <a name="configure-the-load-balancer-backend-pool"></a>Configurare il pool back-end del servizio di bilanciamento del carico

1. Tornare al gruppo di risorse di Azure che contiene le macchine virtuali e individuare il nuovo servizio di bilanciamento del carico. Potrebbe essere necessario aggiornare la visualizzazione nel gruppo di risorse. Selezionare l'istanza di Load Balancer.

1. Selezionare **Pool back-end**, quindi **scegliere Aggiungi**.

1. Associare il pool back-end con il set di disponibilità contenente le macchine virtuali.

1. In **Configurazioni IP**di rete di destinazione selezionare **VIRTUAL MACHINE** e scegliere le macchine virtuali che parteciperanno come nodi del cluster. Assicurarsi di includere tutte le macchine virtuali che ospiteranno l'istanza del cluster di failover.

1. Selezionare **OK** per creare il pool back-end.

### <a name="configure-a-load-balancer-health-probe"></a>Configurare un probe di integrità per il servizio di bilanciamento del carico

1. Nel pannello del servizio di bilanciamento del carico selezionare Probe di **integrità**.

1. Selezionare **Aggiungi**.

1. Nel pannello Aggiungi probe <span id="probe"> </span> di **integrità** impostare i parametri del probe di integrità seguenti.

   - **Nome**: nome del probe di integrità.
   - **Protocollo**: TCP.
   - **Porta**: la porta creata nel firewall per il probe di integrità in [questo passaggio](#ports). In questo articolo viene utilizzata `59999`la porta TCP .
   - **Intervallo**: 5 secondi.
   - **Soglia di non integrità**: 2 errori consecutivi.

1. Selezionare **OK**.

### <a name="set-load-balancing-rules"></a>Impostare le regole di bilanciamento del carico

1. Nel pannello del servizio di bilanciamento del carico selezionare **Regole di bilanciamento del**carico.

1. Selezionare **Aggiungi**.

1. Impostare i parametri della regola di bilanciamento del carico:Set the load balancing rule parameters:

   - **Nome**: nome delle regole di bilanciamento del carico.
   - **Indirizzo IP front-end:** indirizzo IP per la risorsa di rete del cluster FCI di SQL Server.
   - **Porta**: La porta TCP dell'fCI di SQL Server. La porta predefinita dell'istanza è 1433.
   - **Porta back-end**: Utilizza la stessa porta del valore **Porta** quando si abilita **IP mobile (restituzione diretta del server)**.
   - **Pool back-end:** nome del pool back-end configurato in precedenza.
   - **Probe di integrità:** il probe di integrità configurato in precedenza.
   - **Salvataggio permanente sessione**: Nessuno.
   - **Timeout di inattività (minuti)**: 4.
   - **IP mobile (ritorno diretto al server)**: Abilitato.

1. Selezionare **OK**.

## <a name="step-7-configure-the-cluster-for-the-probe"></a>Passaggio 7: Configurare il cluster per il probeStep 7: Configure the cluster for the probe

Impostare il parametro della porta probe del cluster in PowerShell.

Per impostare il parametro della porta probe del cluster, aggiornare le variabili nello script seguente con i valori dell'ambiente. Rimuovere le parentesi`<` `>`angolari ( e ) dallo script.

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

Nell'elenco seguente vengono descritti i valori che è necessario aggiornare:

   - `<Cluster Network Name>`: nome del cluster di failover di Windows Server per la rete. In**Reti**di **Cluster Manager** > di failover fare clic con il pulsante destro del mouse sulla rete e scegliere **Proprietà**. Il valore corretto è riportato in **Nome** nella scheda **Generale**.

   - `<SQL Server FCI IP Address Resource Name>`: nome della risorsa indirizzo IP FCI di SQL Server. In**Ruoli** **di Gestione** > cluster di failover , nel ruolo Dell'istanza fCI di SQL Server , in **Nome server**, fare clic con il pulsante destro del mouse sulla risorsa indirizzo IP e scegliere **Proprietà**. Il valore corretto è riportato in **Nome** nella scheda **Generale**.

   - `<ILBIP>`: indirizzo IP ILB. Questo indirizzo è configurato nel portale di Azure come indirizzo ILB front-end ed è anche l'indirizzo IP dell'istanza del cluster di failover di SQL Server. È riportato in **Gestione cluster di failover** nella stessa pagina delle proprietà in cui si trova `<SQL Server FCI IP Address Resource Name>`.  

   - `<nnnnn>`: la porta probe configurata nel probe di integrità del servizio di bilanciamento del carico. È valida qualsiasi porta TCP inutilizzata.

>[!IMPORTANT]
>La subnet mask per il parametro del cluster deve essere l'indirizzo di broadcast IP TCP: `255.255.255.255`.

Dopo aver impostato il probe del cluster, è possibile visualizzare tutti i parametri del cluster in PowerShell.After you set the cluster probe, you can see all the cluster parameters in PowerShell. Eseguire questo script:

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter
  ```

## <a name="step-8-test-fci-failover"></a>Passaggio 8: Testare il failover FCIStep 8: Test FCI failover

Testare il failover dell'istanza del cluster di failover per convalidare le funzionalità del cluster. Eseguire questa procedura:

1. Connettersi a uno dei nodi del cluster di tipo FCI di SQL Server utilizzando RDP.

1. Aprire **Gestione cluster di failover**. Selezionare **Ruoli**. Rilevare quale nodo è proprietario del ruolo dell'istanza del cluster di failover di SQL Server.

1. Fare clic con il pulsante destro del mouse sul ruolo dell'istanza del cluster di failover di SQL Server.

1. Selezionare **Sposta**, quindi Nodo **migliore possibile**.

**Gestione cluster di failover** mostra il ruolo e le relative risorse non in linea. Le risorse vengono quindi spostate e tornate online nell'altro nodo.

### <a name="test-connectivity"></a>Testare la connettività

Per testare la connettività, accedere a un'altra macchina virtuale nella stessa rete virtuale. Aprire **SQL Server Management Studio** e connettersi al nome dell'istanza del cluster di failover di SQL Server.

>[!NOTE]
>Se necessario, è possibile [scaricare SQL Server Management Studio.](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="limitations"></a>Limitazioni

Le macchine virtuali di Azure supportano Microsoft Distributed Transaction Coordinator (MSDTC) in Windows Server 2019 con archiviazione in Clustered Shared Volumes (CSV) e un servizio di [bilanciamento del carico standard.](../../../load-balancer/load-balancer-standard-overview.md)

Nelle macchine virtuali di Azure MSDTC non è supportato in Windows Server 2016 o versioni precedenti per i nomi:On Azure virtual machines, MSDTC isn't supported on Windows Server 2016 or earlier because:

- La risorsa MSDTC in cluster non può essere configurata per l'utilizzo dell'archiviazione condivisa. In Windows Server 2016, se si crea una risorsa MSDTC, non verrà visualizzata alcuna risorsa di archiviazione condivisa disponibile per l'utilizzo, anche se lo spazio di archiviazione è disponibile. Questo problema è stato risolto per Windows Server 2019.
- Il servizio di bilanciamento del carico di base non gestisce le porte RPC.

## <a name="see-also"></a>Vedere anche

- [Tecnologie cluster di Windows](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server Failover Cluster Instances](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
