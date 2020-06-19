---
title: Istanza del cluster di failover di SQL Server con condivisione file Premium - Macchine virtuali di Azure
description: Questo articolo illustra come creare un'istanza del cluster di failover di SQL Server tramite la condivisione file Premium nelle macchine virtuali di Azure.
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
ms.openlocfilehash: 60526dbeb3e221e6a2e4c6b900ff3a109d4cdf8f
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84032422"
---
# <a name="configure-a-sql-server-failover-cluster-instance-with-premium-file-share-on-azure-virtual-machines"></a>Configurare un'istanza del cluster di failover di SQL Server con condivisione file Premium nelle macchine virtuali di Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Questo articolo illustra come creare un'istanza del cluster di failover di SQL Server nelle macchine virtuali di Azure tramite la [condivisione file Premium](../../../storage/files/storage-how-to-create-premium-fileshare.md).

Le condivisioni file Premium sono condivisioni di file a bassa latenza costante basate su unità SSD completamente supportate per l'uso con le istanze del cluster di failover per SQL Server 2012 o versioni successive in Windows Server 2012 o versioni successive. Le condivisioni file Premium offrono maggiore flessibilità, consentendo di ridimensionare una condivisione file senza tempi di inattività.


## <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare, è necessario tenere presente quanto segue.

È necessario avere una conoscenza operativa di queste tecnologie:

- [Tecnologie cluster di Windows](/windows-server/failover-clustering/failover-clustering-overview)
- [Istanze del cluster di failover di SQL Server](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

Occorre tenere presente che in un cluster di failover di macchine virtuali IaaS di Azure è consigliabile usare una singola scheda di interfaccia di rete per ogni server (nodo del cluster) e una singola subnet. La ridondanza fisica della rete di Azure rende superfluo l'uso di altre schede di interfaccia di rete e subnet in un cluster guest di macchine virtuali IaaS di Azure. Il report di convalida del cluster informa che i nodi sono raggiungibili solo in una singola rete. È possibile ignorare questo avviso nei cluster di failover di macchine virtuali IaaS di Azure.

È anche necessario avere una conoscenza generale di queste tecnologie:

- [Condivisione file Premium di Azure](../../../storage/files/storage-how-to-create-premium-fileshare.md)
- [Gruppi di risorse di Azure](../../../azure-resource-manager/management/manage-resource-groups-portal.md)

> [!IMPORTANT]
> A questo punto, le istanze del cluster di failover di SQL Server nelle macchine virtuali di Azure sono supportate solo con la [modalità di gestione leggera](sql-vm-resource-provider-register.md#management-modes) dell'[Estensione Agente IaaS di SQL Server](sql-server-iaas-agent-extension-automate-management.md). Per passare dalla modalità di estensione completa a quella leggera, eliminare le risorse di **macchina virtuale SQL** per le macchine virtuali corrispondenti e quindi registrarle con il provider di risorse di macchine virtuali SQL in modalità leggera. Per eliminare la risorsa di **macchina virtuale SQL** con il portale di Azure, **deselezionare la casella di controllo accanto alla macchina virtuale corretta**. L'estensione completa supporta funzionalità quali backup automatizzato, applicazione di patch e gestione avanzata del portale. Queste funzionalità non funzioneranno per le macchine virtuali di SQL dopo la reinstallazione dell'agente in modalità di gestione leggera.

Le condivisioni file Premium offrono capacità di operazioni di I/O al secondo e velocità effettiva in grado di soddisfare le esigenze di molti carichi di lavoro. Per i carichi di lavoro con I/O elevato, prendere in considerazione le [istanze del cluster di failover di SQL Server con Spazi di archiviazione diretta](failover-cluster-instance-storage-spaces-direct-manually-configure.md), basate su dischi Premium gestiti o dischi Ultra.  

Controllare l'attività delle operazioni di I/O al secondo dell'ambiente e verificare che le condivisioni file Premium offrano le operazioni di I/O al secondo necessarie prima di avviare una distribuzione o una migrazione. Usare i contatori del disco Monitor prestazioni di Windows per monitorare il numero totale di operazioni di I/O al secondo (trasferimenti disco/secondo) e la velocità effettiva (byte disco/secondo) richieste per i file di dati di SQL Server, file di log e file di database temporanei.

Molti carichi di lavoro hanno un'espansione di I/O, quindi è consigliabile verificare i periodi di uso intensivo e prendere nota sia del numero massimo di operazioni di I/O al secondo sia del numero medio di operazioni di I/O al secondo. Le condivisioni file Premium offrono operazioni di I/O al secondo in base alle dimensioni della condivisione. Le condivisioni file Premium forniscono anche un'espansione gratuita che consente di aumentare le operazioni di I/O in modo da triplicare la quantità di baseline per un massimo di un'ora.

Per altre informazioni sulle prestazioni di condivisione file Premium, vedere [Livelli di prestazioni delle condivisioni file](https://docs.microsoft.com/azure/storage/files/storage-files-planning#storage-tiers).

### <a name="licensing-and-pricing"></a>Licenze e prezzi

Nelle macchine virtuali di Azure è possibile concedere in licenza SQL Server usando immagini di macchine virtuali con pagamento in base al consumo (PAYG) o Bring Your Own License (BYOL). Il tipo di immagine selezionato influisce sul modo in cui vengono applicati i costi.

Con le licenze con pagamento in base al consumo, un'istanza del cluster di failover (FCI) di SQL Server nelle macchine virtuali di Microsoft Azure comporta costi per tutti i nodi dell'istanza del cluster di failover, inclusi i nodi passivi. Per ulteriori informazioni, consultare le [tariffe delle Macchine virtuali SQL Server Enterprise](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/).

Se si dispone di un contratto Enterprise con Software Assurance, è possibile usare un nodo dell'istanza del cluster di failover passivo gratuito per ogni nodo attivo. Per sfruttarne i vantaggi In Azure, usare le immagini di macchine virtuali BYOL, quindi usare la stessa licenza in entrambi i nodi attivo e passivo dell'istanza del cluster di failover. Per altre informazioni, consultare il [Contratto Enterprise](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx).

Per confrontare le licenze con pagamento in base al consumo e BYOL per SQL Server nelle macchine virtuali di Azure, consultare [Introduzione alle VM di SQL](sql-server-on-azure-vm-iaas-what-is-overview.md#get-started-with-sql-vms).

Per informazioni complete sulle licenze SQL Server, consultare [Prezzi](https://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="filestream"></a>Filestream

FileStream non è supportato per un cluster di failover con una condivisione file Premium. Per usare Filestream, distribuire il cluster usando [Spazi di archiviazione diretta](failover-cluster-instance-storage-spaces-direct-manually-configure.md).

## <a name="prerequisites"></a>Prerequisiti

Per poter completare la procedura descritta in questo articolo, è necessario disporre di:

- Una sottoscrizione di Microsoft Azure.
- Un dominio Windows in macchine virtuali di Azure.
- Un account utente di dominio con autorizzazioni per creare oggetti sia nelle macchine virtuali di Azure sia in Active Directory.
- Un account utente di dominio per eseguire il servizio SQL Server e la possibilità di accedere alla macchina virtuale durante il montaggio della condivisione file.  
- Una rete virtuale di Azure e una subnet con uno spazio indirizzi IP sufficiente per questi componenti:
   - Due macchine virtuali.
   - Indirizzo IP del cluster di failover.
   - Indirizzo IP per ogni istanza del cluster di failover.
- DNS configurato nella rete di Azure, che punta ai controller di dominio.
- Una [condivisione file Premium](../../../storage/files/storage-how-to-create-premium-fileshare.md) da usare come unità del cluster, in base alla quota di archiviazione del database per i file di dati.
- Se si usa Windows Server 2012 R2 e versioni precedenti, è necessaria un'altra condivisione file da usare come controllo di condivisione file, poiché i cloud di controllo sono supportati per Windows 2016 e versioni successive. È possibile usare un'altra condivisione file di Azure oppure una condivisione file in una macchina virtuale separata. Se si intende usare un'altra condivisione file di Azure, è possibile montarla con la stessa procedura della condivisione file Premium usata per l'unità del cluster. 

Dopo aver soddisfatto questi prerequisiti, è possibile iniziare a creare il cluster di failover. Il primo passaggio consiste nel creare le macchine virtuali.

## <a name="step-1-create-the-virtual-machines"></a>Passaggio 1: Creare le macchine virtuali

1. Accedere al [portale di Azure](https://portal.azure.com) con la propria sottoscrizione.

1. [Creare un set di disponibilità di Azure](../../../virtual-machines/linux/tutorial-availability-sets.md).

   Un set di disponibilità raggruppa le macchine virtuali in più domini di errore e domini di aggiornamento. Garantisce che l'applicazione non sia influenzata da singoli punti di guasto, come il commutatore di rete o l'unità di alimentazione di un rack di server.

   Se non è stato creato il gruppo di risorse per le macchine virtuali, crearlo al momento della creazione di un set di disponibilità di Azure. Se si usa il portale di Azure per creare il set di disponibilità, seguire questa procedura:

   1. Nel portale di Azure selezionare **Crea una risorsa** per aprire Azure Marketplace. Cercare **Set di disponibilità**.
   1. Selezionare **Set di disponibilità**.
   1. Selezionare **Crea**.
   1. In **Crea set di disponibilità**specificare i valori seguenti:
      - **Name**: un nome per il set di disponibilità.
      - **Sottoscrizione** La sottoscrizione di Azure.
      - **Gruppo di risorse**: se si vuole usare un gruppo esistente, fare clic su **Seleziona esistente** e selezionare il gruppo dall'elenco. In caso contrario, selezionare **Crea nuovo** e immettere un nome per il gruppo.
      - **Località**: impostare la località in cui si intende creare le macchine virtuali.
      - **Domini di errore**: usare il valore predefinito (**3**).
      - **Domini di aggiornamento**: usare il valore predefinito (**5**).
   1. Selezionare **Crea** per creare il set di disponibilità.

1. Creare le macchine virtuali nel set di disponibilità.

   Effettuare il provisioning di due macchine virtuali di SQL Server nel set di disponibilità di Azure. Per istruzioni, vedere [Effettuare il provisioning di una macchina virtuale di SQL Server nel portale di Azure](create-sql-vm-portal.md).

   Inserire entrambe le macchine virtuali:

   - Nello stesso gruppo di risorse di Azure del set di disponibilità.
   - Nella stessa rete del controller di dominio.
   - In una subnet con spazio indirizzi IP sufficiente per entrambe le macchine virtuali e tutte le istanze del cluster di failover che si potrebbero usare nel cluster.
   - Nel set di disponibilità di Azure.

      >[!IMPORTANT]
      >Non è possibile impostare o modificare il set di disponibilità dopo aver creato una macchina virtuale.

   Scegliere un'immagine da Azure Marketplace. È possibile usare un'immagine di Azure Marketplace che include Windows Server e SQL Server, oppure usarne una che include solo Windows Server. Per informazioni dettagliate, vedere [Panoramica di SQL Server in macchine virtuali di Azure](sql-server-on-azure-vm-iaas-what-is-overview.md).

   Le immagini ufficiali di SQL Server nella raccolta di Azure includono un'istanza installata di SQL Server, il software di installazione di SQL Server e la chiave necessaria.

   >[!IMPORTANT]
   > Dopo aver creato la macchina virtuale, rimuovere l'istanza autonoma preinstallata di SQL Server. Usare i supporti dell'istanza preinstallata di SQL Server per creare l'istanza del cluster di failover di SQL Server dopo aver configurato il cluster di failover e la condivisione file Premium come archiviazione.

   In alternativa, è possibile usare immagini di Azure Marketplace che contengono solo il sistema operativo. Scegliere un'immagine di **Windows Server 2016 Datacenter** e installare l'istanza del cluster di failover di SQL Server dopo aver configurato il cluster di failover e la condivisione file Premium come archiviazione. Un'immagine di questo tipo non contiene i supporti di installazione di SQL Server. Posizionare i supporti di installazione di SQL Server in un percorso in cui è possibile eseguirli per ogni server.

1. Dopo aver creato le macchine virtuali di Azure, connettersi a ognuna con RDP.

   Quando ci si connette per la prima volta a una macchina virtuale con RDP, un messaggio chiede se si vuole rendere il PC individuabile sulla rete. Selezionare **Sì**.

1. Se si usa un'immagine di macchina virtuale basata su SQL Server, rimuovere l'istanza di SQL Server.

   1. In **Programmi e funzionalità** fare clic con il pulsante destro del mouse su **Microsoft SQL Server 201_ (64-bit)** e selezionare **Disinstalla/Cambia**.
   1. Selezionare **Rimuovi**.
   1. Selezionare l'istanza predefinita.
   1. Rimuovere tutte le funzionalità in **Servizi motore di database**. Non rimuovere **Funzionalità condivise**. Il contenuto visualizzato sarà simile allo screenshot seguente:

        ![Seleziona caratteristiche](./media/failover-cluster-instance-premium-file-share-manually-configure/03-remove-features.png)

   1. Selezionare **Avanti** e quindi **Rimuovi**.

1. <span id="ports"> </span> Aprire le porte del firewall.  

   In ogni macchina virtuale aprire queste porte in Windows Firewall:

   | Scopo | Porta TCP | Note
   | ------ | ------ | ------
   | SQL Server | 1433 | Porta normale per le istanze predefinite di SQL Server. Se è stata usata un'immagine della raccolta, questa porta è automaticamente aperta.
   | Probe di integrità | 59999 | Qualsiasi porta TCP aperta. In un passaggio successivo, configurare il [probe di integrità](#probe) del servizio di bilanciamento del carico e il cluster per l'uso di questa porta.
   | Condivisione file | 445 | Porta usata dal servizio di condivisione file.

1. [Aggiungere le macchine virtuali al dominio preesistente](availability-group-manually-configure-prerequisites-tutorial.md#joinDomain).

Dopo aver creato e configurato le macchine virtuali, è possibile configurare la condivisione file Premium.

## <a name="step-2-mount-the-premium-file-share"></a>Passaggio 2: montare la condivisione file Premium

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'account di archiviazione.
1. Passare a **Condivisioni file** in **Servizi file** e selezionare la condivisione file Premium da usare per l'archiviazione SQL.
1. Selezionare **Connetti** per visualizzare la stringa di connessione per la condivisione file.
1. Selezionare la lettera di unità che si intende usare dall'elenco a discesa, quindi copiare entrambi i blocchi di codice nel blocco note.


   :::image type="content" source="media/manually-configure-failover-cluster-instance-premium-file-share/premium-file-storage-commands.png" alt-text="Copiare entrambi i comandi di PowerShell dal portale di connessione della condivisione file":::

1. Usare RDP per connettersi alla macchina virtuale di SQL Server con l'account usato dall'istanza del cluster di failover di SQL Server per l'account del servizio.
1. Aprire una console di comandi di PowerShell come amministratore.
1. Eseguire i comandi salvati in precedenza durante il lavoro nel portale.
1. Passare alla condivisione usando Esplora file o la finestra di dialogo **Esegui** (tasto logo Windows + r). Usare il percorso di rete `\\storageaccountname.file.core.windows.net\filesharename`. Ad esempio, usare `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`

1. Creare almeno una cartella nella condivisione file appena collegata in cui inserire i file di dati SQL.
1. Ripetere questi passaggi in ogni macchina virtuale di SQL Server che parteciperà al cluster.

  > [!IMPORTANT]
  > - È possibile usare una condivisione file separata per i file di backup per salvare le capacità in termini di operazioni di I/O al secondo e di spazio di questa condivisione per i file di dati e di log. È possibile usare una condivisione file Premium o Standard per i file di backup.
  > - Se si usa Windows 2012 R2 e versioni precedenti, seguire la stessa procedura per montare la condivisione file che si intende usare come server di controllo di condivisione file. 

## <a name="step-3-configure-the-failover-cluster"></a>Passaggio 3: configurare il cluster di failover

Il passaggio successivo consiste nel configurare il cluster di failover. Questo passaggio include i passaggi secondari seguenti:

1. Aggiungere la funzionalità Clustering di failover di Windows Server.
1. Convalidare il cluster.
1. Creare il cluster di failover.
1. Creare il cloud di controllo (per Windows Server 2016 e versioni successive) o il controllo di condivisione file (per Windows Server 2012 R2 e versioni precedenti).


### <a name="add-windows-server-failover-clustering"></a>Aggiungere Clustering di failover di Windows Server

1. Connettersi alla prima macchina virtuale con RDP usando un account di dominio che è membro del gruppo degli amministratori locali e ha le autorizzazioni necessarie per creare oggetti in Active Directory. Usare questo account per il resto della configurazione.

1. [Aggiungere Clustering di failover a ogni macchina virtuale](availability-group-manually-configure-prerequisites-tutorial.md#add-failover-clustering-features-to-both-sql-server-vms).

   Per installare Clustering di failover dall'interfaccia utente, eseguire questa procedura in entrambe le macchine virtuali:
   1. In **Server Manager** selezionare **Gestione**, e quindi **Aggiungi ruoli e funzionalità**.
   1. Nell'**Aggiunta guidata ruoli e funzionalità** selezionare **Avanti** fino a visualizzare **Seleziona funzionalità**.
   1. In **Seleziona funzionalità** selezionare **Clustering di failover**. Includere tutte le funzionalità necessarie e gli strumenti di gestione. Selezionare **Aggiungi funzionalità**.
   1. Selezionare **Avanti** e quindi selezionare **Fine** per installare le funzionalità.

   Per installare Clustering di failover con PowerShell, eseguire lo script seguente da una sessione di PowerShell in modalità amministratore in una delle macchine virtuali:

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

### <a name="validate-the-cluster"></a>Convalidare il cluster

Convalidare il cluster nell'interfaccia utente o usando PowerShell.

Per convalidare il cluster usando l'interfaccia utente, eseguire questa procedura su una delle macchine virtuali:

1. In **Server Manager** selezionare **Strumenti**, quindi selezionare **Gestione cluster di failover**.
1. In **Gestione cluster di failover**selezionare **Azione**e quindi selezionare **Convalida configurazione**.
1. Selezionare **Avanti**.
1. In **Selezione di server o di un cluster** immettere i nomi di entrambe le macchine virtuali.
1. In **Opzioni di testing** selezionare **Esegui solo test selezionati**. Selezionare **Avanti**.
1. In **Selezione dei test** selezionare tutti i test tranne **Archiviazione** e **Spazi di archiviazione diretta**, come illustrato di seguito:

   :::image type="content" source="media/manually-configure-failover-cluster-instance-premium-file-share/cluster-validation.png" alt-text="Selezionare i test di convalida del cluster":::

1. Selezionare **Avanti**.
1. In **Conferma** selezionare **Avanti**.

La **Convalida guidata configurazione** eseguirà i test di convalida.

Per convalidare il cluster usando PowerShell, eseguire lo script seguente da una sessione di PowerShell in modalità amministratore in una delle macchine virtuali:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

Dopo aver convalidato il cluster, creare il cluster di failover.

### <a name="create-the-failover-cluster"></a>Creare il cluster di failover

Per creare il cluster di failover è necessario:
- I nomi delle macchine virtuali che diventeranno i nodi del cluster.
- un nome per il cluster di failover
- un indirizzo IP per il cluster di failover. È possibile usare un indirizzo IP non usato nella stessa rete virtuale di Azure e nella stessa subnet dei nodi del cluster.

#### <a name="windows-server-2012-through-windows-server-2016"></a>Windows Server 2012 tramite Windows Server 2016

Lo script di PowerShell seguente crea un cluster di failover per Windows Server 2012 tramite Windows Server 2016. Aggiornare lo script con i nomi dei nodi (ossia i nomi delle macchine virtuali) e un indirizzo IP disponibile della rete virtuale di Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

Lo script di PowerShell seguente crea un cluster di failover per Windows Server 2019. Per altre informazioni, vedere [Cluster di failover: oggetto di rete cluster](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97). Aggiornare lo script con i nomi dei nodi (ossia i nomi delle macchine virtuali) e un indirizzo IP disponibile della rete virtuale di Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness-win-2016-"></a>Creare un cloud di controllo (Windows 2016 +)

Se si usa Windows Server 2016 e versioni successive, è necessario creare un cloud di controllo. Il cloud di controllo è un nuovo tipo di quorum di controllo del cluster archiviato in un BLOB del servizio di archiviazione di Azure. In questo modo si elimina la necessità di una macchina virtuale separata che ospita una condivisione di controllo o che usa una condivisione file separata.

1. [Creare un cloud di controllo per il cluster di failover](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Creare un contenitore BLOB.

1. Salvare le chiavi di accesso e l'URL del contenitore.

### <a name="configure-quorum"></a>Configurare il quorum 

Per Windows Server 2016 e versioni successive, è necessario configurare il cluster per usare il cloud di controllo appena creato. Seguire la procedura descritta in [Configurare il quorum di controllo nell'interfaccia utente](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness).

Per Windows Server 2012 R2 e versioni precedenti, seguire la stessa procedura descritta in [Configurare il quorum di controllo nell'interfaccia utente](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness) e nella pagina **Selezione quorum di controllo** selezionare l'opzione **Configura condivisione file di controllo**. Specificare la condivisione file allocata come controllo di condivisione file, indipendentemente dal fatto che sia stata configurata in una macchina virtuale separata o montata da Azure. 


## <a name="step-4-test-cluster-failover"></a>Passaggio 4: testare il failover del cluster

Testare il failover del cluster. In **Gestione cluster di failover** fare clic con il pulsante destro del mouse sul cluster e selezionare **Altre azioni** > **Sposta risorse principali del cluster** > **Seleziona nodo**, quindi selezionare l'altro nodo del cluster. Spostare le risorse principali del cluster in ogni nodo del cluster, quindi spostarle di nuovo nel nodo primario. Se lo spostamento del cluster in ogni nodo avviene in modo corretto, è possibile installare SQL Server.  

:::image type="content" source="media/manually-configure-failover-cluster-instance-premium-file-share/test-cluster-failover.png" alt-text="Testare il failover del cluster spostando le risorse principali negli altri nodi":::

## <a name="step-5-create-the-sql-server-fci"></a>Passaggio 5: creare l'istanza del cluster di failover di SQL Server

Dopo aver configurato il cluster di failover, è possibile creare l'istanza del cluster di failover di SQL Server.

1. Connettersi alla prima macchina virtuale con RDP.

1. In **Gestione cluster di failover** verificare che tutte le risorse principali del cluster si trovino nella prima macchina virtuale. Se necessario, spostare tutte le risorse in questa macchina virtuale.

1. Individuare i supporti di installazione. Se la macchina virtuale usa una delle immagini di Azure Marketplace, i supporti si trovano in `C:\SQLServer_<version number>_Full`. Selezionare **Imposta**.

1. Nel **Centro installazione SQL Server** selezionare **Installazione**.

1. Selezionare **Installazione di un nuovo cluster di failover di SQL Server**. Seguire le istruzioni della procedura guidata per installare l'istanza del cluster di failover di SQL Server.

   Le directory di dati dell'istanza del cluster di failover devono trovarsi nella condivisione file Premium. Immettere il percorso completo della condivisione, in questo formato: `\\storageaccountname.file.core.windows.net\filesharename\foldername`. Verrà visualizzato un avviso che informa che è stato specificato un file server come directory di dati. Si tratta di un avviso previsto. Per evitare possibili errori, assicurarsi che l'account utente con cui si è eseguita la connessione RDP nella macchina virtuale quando è stata resa permanente la condivisione file sia lo stesso account usato dal servizio SQL Server.

   :::image type="content" source="media/manually-configure-failover-cluster-instance-premium-file-share/use-file-share-as-data-directories.png" alt-text="Usare la condivisione file come directory dati SQL":::

1. Dopo aver completato i passaggi della procedura guidata, verrà installata un'istanza del cluster di failover di SQL Server nel primo nodo.

1. Al termine dell'installazione dell'istanza del cluster di failover nel primo nodo, connettersi al secondo nodo usando RDP.

1. Aprire il **Centro installazione SQL Server**. Selezionare **Installazione**.

1. Selezionare **Aggiungi nodo a cluster di failover di SQL Server**. Seguire le istruzioni della procedura guidata per installare SQL Server e aggiungere il server all'istanza del cluster di failover.

   >[!NOTE]
   >Se è stata usata un'immagine della raccolta di Azure Marketplace con SQL Server, gli strumenti di SQL Server sono stati inclusi con l'immagine. In caso contrario, installare gli strumenti di SQL Server separatamente. Vedere [Scaricare SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-6-create-the-azure-load-balancer"></a>Passaggio 6: creare il servizio di bilanciamento del carico di Azure

Nelle macchine virtuali di Azure, per contenere un indirizzo IP che deve trovarsi in un nodo del cluster alla volta viene usato nei cluster un servizio di bilanciamento del carico. In questa soluzione, il servizio di bilanciamento del carico contiene l'indirizzo IP per l'istanza del cluster di failover di SQL Server.

Per altre informazioni, vedere [Creare e configurare un servizio di bilanciamento del carico di Azure](availability-group-manually-configure-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Creare il servizio di bilanciamento del carico nel portale di Azure

Per creare il servizio di bilanciamento del carico:

1. Nel portale di Azure passare al gruppo di risorse contenente le macchine virtuali.

1. Selezionare **Aggiungi**. Cercare **Bilanciamento del carico** in Azure Marketplace. Selezionare **Bilanciamento del carico**.

1. Selezionare **Crea**.

1. Configurare il servizio di bilanciamento del carico usando i valori seguenti:

   - **Sottoscrizione** La sottoscrizione di Azure.
   - **Gruppo di risorse**: il gruppo di risorse contenente le macchine virtuali.
   - **Name**: un nome che identifichi il servizio di bilanciamento del carico.
   - **Area**: la località di Azure contenente le macchine virtuali.
   - **Tipo**: pubblico o privato. Un servizio di bilanciamento del carico privato è accessibile dalla rete virtuale. La maggior parte delle applicazioni Azure può usare un servizio di bilanciamento del carico privato. Se l'applicazione deve accedere a SQL Server direttamente su Internet, usare un servizio di bilanciamento del carico pubblico.
   - **SKU**: Standard.
   - **Rete virtuale**: la stessa rete delle macchine virtuali.
   - **Assegnazione indirizzo IP**: statico. 
   - **Indirizzo IP privato**: l'indirizzo IP assegnato alla risorsa di rete cluster dell'istanza del cluster di failover di SQL Server.

   La figura seguente mostra l'interfaccia utente della pagina **Crea servizio di bilanciamento del carico**:

   ![Configurare il servizio di bilanciamento del carico](./media/failover-cluster-instance-premium-file-share-manually-configure/30-load-balancer-create.png)
   

### <a name="configure-the-load-balancer-backend-pool"></a>Configurare il pool back-end del servizio di bilanciamento del carico

1. Tornare al gruppo di risorse di Azure contenente le macchine virtuali e individuare il nuovo servizio di bilanciamento del carico. Potrebbe essere necessario aggiornare la visualizzazione del gruppo di risorse. Selezionare l'istanza di Load Balancer.

1. Selezionare **Pool back-end** e quindi **Aggiungi**.

1. Associare il pool back-end con il set di disponibilità contenente le macchine virtuali.

1. In **Configurazioni IP della rete di destinazione** selezionare **MACCHINA VIRTUALE** e scegliere le macchine virtuali che parteciperanno come nodi del cluster. Assicurarsi di includere tutte le macchine virtuali che ospiteranno l'istanza del cluster di failover.

1. Selezionare **OK** per creare il pool back-end.

### <a name="configure-a-load-balancer-health-probe"></a>Configurare un probe di integrità per il servizio di bilanciamento del carico

1. Nel pannello del servizio di bilanciamento del carico selezionare **Probe integrità**.

1. Selezionare **Aggiungi**.

1. Nel pannello **Aggiungi probe integrità** <span id="probe"> </span> impostare i parametri del probe di integrità seguenti.

   - **Name**: un nome per il probe di integrità.
   - **Protocollo**: TCP.
   - **Porta**: la porta creata nel firewall per il probe di integrità in [questo passaggio](#ports). In questo articolo, l'esempio usa la porta TCP `59999`.
   - **Intervallo**: 5 secondi.
   - **Soglia non integra**: 2 errori consecutivi.

1. Selezionare **OK**.

### <a name="set-load-balancing-rules"></a>Impostare le regole di bilanciamento del carico

1. Nel pannello del servizio di bilanciamento del carico selezionare **Regole di bilanciamento del carico**.

1. Selezionare **Aggiungi**.

1. Impostare i parametri della regola di bilanciamento del carico:

   - **Name**: un nome per le regole di bilanciamento del carico.
   - **Indirizzo IP front-end**: l'indirizzo IP per la risorsa di rete cluster dell'istanza del cluster di failover di SQL Server.
   - **Porta**: la porta TCP dell'istanza del cluster di failover di SQL Server. La porta predefinita dell'istanza è 1433.
   - **Porta back-end**: usa la stessa porta specificata nel campo **Porta** quando si abilita **IP mobile (Direct Server Return)** .
   - **Pool back-end**: il nome del pool back-end configurato in precedenza.
   - **Probe di integrità**: il probe di integrità configurato in precedenza.
   - **Salvataggio permanente sessione**: No.
   - **Timeout di inattività (minuti)** : 4.
   - **IP mobile (Direct Server Return)** : abilitato.

1. Selezionare **OK**.

## <a name="step-7-configure-the-cluster-for-the-probe"></a>Passaggio 7: configurare il cluster per il probe

Impostare il parametro della porta probe del cluster in PowerShell.

Per impostare tale parametro, aggiornare le variabili nello script seguente con i valori dell'ambiente in uso. Rimuovere le parentesi acute (`<` e `>`) dallo script.

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

L'elenco seguente offre una descrizione dei valori da aggiornare:

   - `<Cluster Network Name>`: il nome del cluster di failover di Windows Server per la rete. In **Gestione cluster di failover** > **Reti** fare clic con il pulsante destro del mouse sulla rete e selezionare **Proprietà**. Il valore corretto è riportato in **Nome** nella scheda **Generale**.

   - `<SQL Server FCI IP Address Resource Name>`: il nome della risorsa indirizzo IP dell'istanza del cluster di failover di SQL Server. In **Gestione cluster di failover** > **Ruoli** fare clic con il pulsante destro del mouse sulla risorsa indirizzo IP sotto **Nome server** nel ruolo dell'istanza del cluster di failover di SQL Server e quindi selezionare **Proprietà**. Il valore corretto è riportato in **Nome** nella scheda **Generale**.

   - `<ILBIP>`: indirizzo IP ILB. Questo indirizzo è configurato nel portale di Azure come indirizzo ILB front-end ed è anche l'indirizzo IP dell'istanza del cluster di failover di SQL Server. È riportato in **Gestione cluster di failover** nella stessa pagina delle proprietà in cui si trova `<SQL Server FCI IP Address Resource Name>`.  

   - `<nnnnn>`: la porta probe configurata nel probe di integrità del servizio di bilanciamento del carico. È valida qualsiasi porta TCP inutilizzata.

>[!IMPORTANT]
>La subnet mask per il parametro del cluster deve essere l'indirizzo di broadcast IP TCP: `255.255.255.255`.

Dopo aver configurato il probe del cluster, è possibile visualizzare tutti i parametri del cluster in PowerShell. Eseguire questo script:

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter
  ```

## <a name="step-8-test-fci-failover"></a>Passaggio 8: Testare il failover dell'istanza del cluster di failover

Testare il failover dell'istanza del cluster di failover per convalidare le funzionalità del cluster. Eseguire questa procedura:

1. Connettersi a un nodo del cluster dell'istanza del cluster di failover di SQL Server usando RDP.

1. Aprire **Gestione cluster di failover**. Selezionare **Ruoli**. Rilevare quale nodo è proprietario del ruolo dell'istanza del cluster di failover di SQL Server.

1. Fare clic con il pulsante destro del mouse sul ruolo dell'istanza del cluster di failover di SQL Server.

1. Selezionare **Sposta**, quindi selezionare **Miglior nodo possibile**.

In **Gestione cluster di failover** viene visualizzato il ruolo e le relative risorse passano alla modalità offline. Le risorse vengono quindi spostate e portate di nuovo online nell'altro nodo.

### <a name="test-connectivity"></a>Testare la connettività

Per testare la connettività, accedere a un'altra macchina virtuale nella stessa rete virtuale. Aprire **SQL Server Management Studio** e connettersi al nome dell'istanza del cluster di failover di SQL Server.

>[!NOTE]
>Se necessario, è possibile [scaricare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="limitations"></a>Limitazioni

Le macchine virtuali di Azure supportano Microsoft Distributed Transaction Coordinator (MSDTC) in Windows Server 2019 con archiviazione in volumi condivisi cluster (CSV) e un [bilanciamento del carico standard](../../../load-balancer/load-balancer-standard-overview.md).

Nelle macchine virtuali di Azure, MSDTC non è supportato in Windows Server 2016 o versioni precedenti perché:

- La risorsa MSDTC cluster non può essere configurata per usare l'archiviazione condivisa. In Windows Server 2016, se si crea una risorsa MSDTC, non verrà visualizzata alcuna archiviazione condivisa disponibile per l'uso, anche se lo spazio di archiviazione è disponibile. Questo problema è stato risolto per Windows Server 2019.
- Il servizio di bilanciamento del carico di base non gestisce le porte RPC.

## <a name="see-also"></a>Vedere anche

- [Tecnologie cluster di Windows](/windows-server/failover-clustering/failover-clustering-overview)
- [Istanze del cluster di failover di SQL Server](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
