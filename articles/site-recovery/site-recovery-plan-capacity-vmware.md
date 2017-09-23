---
title: "Pianificare la capacità e la scalabilità per la replica VMware in Azure con Azure Site Recovery | Microsoft Docs"
description: "Consultare questo articolo per pianificare la capacità e la scalabilità quando si esegue la replica delle macchine virtuali VMware in Azure con Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 0a1cd8eb-a8f7-4228-ab84-9449e0b2887b
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/24/2017
ms.author: rayne
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 8b580ac239bfb6d7b633fb03d4cfb91b168b0610
ms.contentlocale: it-it
ms.lasthandoff: 05/25/2017

---
# <a name="plan-capacity-and-scaling-for-vmware-replication-with-azure-site-recovery"></a>Pianificare la capacità e la scalabilità per la replica VMware con Azure Site Recovery

Consultare questo articolo per scoprire come pianificare la capacità e la scalabilità durante la replica locale delle macchine virtuali VMware e dei server fisici in Azure, con [Azure Site Recovery](site-recovery-overview.md).

## <a name="how-do-i-start-capacity-planning"></a>Come si inizia per pianificare la capacità?

Raccogliere informazioni sull'ambiente di replica usando [Azure Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner-doc) per la replica VMware. [Altre informazioni](site-recovery-deployment-planner.md) su questo strumento. È possibile ottenere informazioni sulle macchine virtuali compatibili e non, i dischi per ogni macchina virtuale e la varianza di dati per ogni disco. Lo strumento specifica anche i requisiti della larghezza di banda di rete e l'infrastruttura di Azure necessaria per la corretta esecuzione della replica e il failover del test.

## <a name="capacity-considerations"></a>Considerazioni sulla capacità

**Componente** | **Dettagli** |
--- | --- | ---
**Replica** | **Frequenza di modifica giornaliera massima**: un computer protetto può usare un solo server di elaborazione e la frequenza di modifica giornaliera gestita da un singolo server di elaborazione può arrivare fino a 2 TB. La massima frequenza di modifica dei dati giornaliera supportata per una macchina protetta è quindi di 2 TB.<br/><br/> **Velocità effettiva massima**: una macchina replicata può appartenere a un account di archiviazione in Azure. Un account di archiviazione standard può gestire fino a 20.000 richieste al secondo ed è consigliabile fare in modo che il numero di operazioni di input/output al secondo (IOPS) in una macchina di origine rimanga impostato su 20.000. Se, ad esempio, si ha un computer di origine con 5 dischi e ogni disco genera 120 IOPS (dimensione pari a 8 K) nella macchina di origine, il computer rientra nel limite di Azure di 500 IOPS per disco. Il numero di account di archiviazione necessari è uguale al numero complessivo di IOPS della macchina di origine diviso per 20.000.
**Server di configurazione** | Il server di configurazione deve poter gestire la capacità della frequenza di modifica giornaliera in tutti i carichi di lavoro in esecuzione nei computer protetti e avere a disposizione una larghezza di banda sufficiente per eseguire la replica continua dei dati in Archiviazione di Azure.<br/><br/> Come procedura consigliata, il server di configurazione deve trovarsi nella stessa rete e nello stesso segmento LAN dei computer da proteggere. Se si trova in una rete diversa, i computer da proteggere devono avere la visibilità di rete L3 per il server.<br/><br/> Le dimensioni consigliate per il server di configurazione sono riepilogate nella tabella riportata nella sezione successiva.
**Server di elaborazione** | Per impostazione predefinita, il primo server di elaborazione viene installato nel server di configurazione. È possibile distribuire altri server di elaborazione per ridimensionare l'ambiente. <br/><br/> Il server di elaborazione riceve i dati di replica da computer protetti e li ottimizza attraverso la memorizzazione nella cache, la compressione e la crittografia, prima di inviarli ad Azure. Il computer server di elaborazione deve avere risorse sufficienti per eseguire queste attività.<br/><br/> Il server di elaborazione usa una cache basata su disco. Usare un disco della cache separato di almeno 600 GB per gestire le modifiche ai dati archiviate in caso di colli di bottiglia o interruzioni della rete.

## <a name="size-recommendations-for-the-configuration-server"></a>Dimensioni consigliate per il server di configurazione

**CPU** | **Memoria** | **Dimensione disco cache** | **Frequenza di modifica dei dati** | **Computer protetti**
--- | --- | --- | --- | ---
8 vCPU (2 socket * 4 core a 2.5 gigahertz [GHz]) | 16 GB | 300 GB | 500 GB o inferiore | Replicare meno di 100 computer.
12 vCPU (2 socket * 6 core a 2,5 GHz) | 18 GB | 600 GB | Da 500 GB a 1 TB | Replicare tra 100 e 150 computer.
16 vCPU (2 socket * 8 core a 2,5 GHz) | 32 GB | 1 TB | Da 1 TB a 2 TB | Replicare tra 150 e 200 computer.
Distribuire un altro server di elaborazione | | | Superiore a 2 TB | Distribuire server di elaborazione aggiuntivi se si esegue la replica di più di 200 computer o la frequenza di modifica dei dati giornaliera è superiore a 2 TB.

Dove:

* Ogni computer di origine è configurato con 3 dischi da 100 GB.
* La risorsa di archiviazione di benchmarking usata per le misurazioni del disco della cache è di 8 unità SAS a 10.000 RPM con RAID 10.

## <a name="size-recommendations-for-the-process-server"></a>Dimensioni consigliate per il server di elaborazione

Se è necessario proteggere più di 200 computer o la frequenza di modifica giornaliera è superiore a 2 TB, è possibile aggiungere altri server di elaborazione per gestire il carico di replica. Per la scalabilità orizzontale è possibile:

* Aumentare il numero di server di configurazione. Ad esempio, è possibile proteggere fino a 400 computer con due server di configurazione.
* Aggiungere altri server di elaborazione e usarli per gestire il traffico al posto del server di gestione o in aggiunta al server di configurazione.

La tabella seguente descrive uno scenario in cui:

* Non si prevede di usare il server di configurazione come server di elaborazione.
* È stato configurato un server di elaborazione aggiuntivo.
* Le macchine virtuali protette sono state configurate per l'uso del server di elaborazione aggiuntivo.
* Ogni computer di origine protetto è configurato con tre dischi da 100 GB.

**Server di configurazione** | **Server di elaborazione aggiuntivo** | **Dimensione disco cache** | **Frequenza di modifica dei dati** | **Computer protetti**
--- | --- | --- | --- | ---
8 vCPU (2 socket * 4 core a 2,5 GHz), 16 GB di memoria | 4 vCPU (2 socket * 2 core a 2,5 GHz), 8 GB di memoria | 300 GB | 250 GB o inferiore | Eseguire la replica di un massimo di 85 macchine.
8 vCPU (2 socket * 4 core a 2,5 GHz), 16 GB di memoria | 8 vCPU (2 socket * 4 core a 2,5 GHz), 12 GB di memoria | 600 GB | Da 250 GB a 1 TB | Replicare tra 85 e 150 computer.
12 vCPU (2 socket * 6 core a 2,5 GHz), 18 GB di memoria | 12 vCPU (2 socket * 6 core a 2,5 GHz), 24 GB di memoria | 1 TB | Da 1 TB a 2 TB | Replicare tra 150 e 225 computer.

Il modo in cui i server vengono adattati dipende dalle preferenze personali in merito al modello di scalabilità orizzontale o verticale.  L'aumento delle prestazioni si ottiene distribuendo alcuni server di configurazione e di elaborazione avanzati, mentre l'aumento del numero di istanze si ottiene distribuendo più server con meno risorse. Ad esempio, per proteggere 220 computer è possibile eseguire una di queste operazioni:

* Configurare il server di configurazione con 12 vCPU e 18 GB di memoria e un server di elaborazione aggiuntivo con 12 vCPU e 24 GB di memoria. Configurare quindi le macchine protette affinché usino solo il server di elaborazione aggiuntivo.
* Configurare due server di configurazione (2 x 8 vCPU, 16 GB di RAM) e due server di elaborazione aggiuntivi (1 x 8 vCPU e 1 x 4 vCPU per gestire 135 + 85 [220] macchine). Configurare quindi le macchine protette affinché usino solo i server di elaborazione aggiuntivi.


## <a name="control-network-bandwidth"></a>Controllare la larghezza di banda della rete

Dopo aver usato lo [strumento di pianificazione della distribuzione](site-recovery-deployment-planner.md) per calcolare la larghezza di banda necessaria per la replica, per la replica iniziale e poi delta, è possibile controllare la quantità di larghezza di banda usata per la replica con due opzioni:

* **Limitare la larghezza di banda**: il traffico VMware che viene replicato in Azure passa attraverso un server di elaborazione specifico. È possibile limitare la larghezza di banda nei computer eseguiti come server di elaborazione.
* **Influire sulla larghezza di banda**: è possibile influire sulla larghezza di banda usata per la replica tramite una coppia di chiavi del Registro di sistema.
  * Il valore del Registro di sistema **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\UploadThreadsPerVM** specifica il numero di thread usati per il trasferimento dati di un disco, durante la replica iniziale o differenziale. Un valore più elevato aumenta la larghezza di banda di rete usata per la replica.
  * Il valore **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DownloadThreadsPerVM** specifica il numero di thread usati per il trasferimento dati durante il failback.

### <a name="throttle-bandwidth"></a>Limitare la larghezza di banda

1. Aprire lo snap-in MMC di Azure Backup nel computer usato come server di elaborazione. Per impostazione predefinita, è disponibile un collegamento a Backup sul desktop o nella cartella seguente: C:\Programmi\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. Nello snap-in fare clic su **Modifica proprietà**.

    ![Screenshot dello snap-in MMC di Azure Backup facoltativo per modificare le proprietà](./media/site-recovery-vmware-to-azure/throttle1.png)
3. Nella scheda **Limitazione larghezza di banda rete** selezionare **Abilita la limitazione all'utilizzo della larghezza di banda Internet per le operazioni di backup**. Impostare i limiti per le ore lavorative e non lavorative. Gli intervalli validi sono compresi tra 512 Kbps e 102 Mbps al secondo.

    ![Screenshot della finestra di dialogo Proprietà di Azure Backup](./media/site-recovery-vmware-to-azure/throttle2.png)

È anche possibile usare il cmdlet [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) per impostare la limitazione. Di seguito è riportato un esempio:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** indica che non è necessaria alcuna limitazione.

### <a name="influence-network-bandwidth-for-a-vm"></a>Influire sulla larghezza di banda della rete per una VM

1. Nel Registro di sistema della VM passare a **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
   * Per intervenire sul traffico della larghezza di banda in un disco di replica, modificare il valore di **UploadThreadsPerVM** oppure creare la chiave, se non esiste.
   * Per intervenire sulla larghezza di banda per il traffico di failback da Azure, modificare il valore di **DownloadThreadsPerVM**.
2. Il valore predefinito è 4. In una rete con provisioning eccessivo è necessario modificare i valori predefiniti di queste chiavi del Registro di sistema. Il valore massimo è 32. Monitorare il traffico per ottimizzare il valore.


## <a name="deploy-additional-process-servers"></a>Distribuire server di elaborazione aggiuntivi

Se è necessario ridimensionare la distribuzione oltre 200 computer di origine oppure la varianza totale giornaliera è superiore a 2 TB, sono necessari server di elaborazione aggiuntivi per gestire il volume di traffico. Seguire queste istruzioni per configurare il server di elaborazione. Dopo aver configurato il server è possibile eseguire la migrazione dei computer di origine per usarlo.

1. In **Site Recovery servers** (Server di Site Recovery) fare clic sul server di configurazione e quindi su **Server di elaborazione**.

    ![Screenshot dell'opzione relativa ai server Site Recovery da aggiungere a un server di elaborazione](./media/site-recovery-vmware-to-azure/migrate-ps1.png)
2. In **Tipo di server** fare clic su **Process server (on-premises)** (Server di elaborazione (locale)).

    ![Screenshot della finestra di dialogo Server di elaborazione](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
3. Scaricare il file per l'Installazione unificata di Site Recovery ed eseguirlo per installare il server di elaborazione e registrarlo nell'insieme di credenziali.
4. In **Prima di iniziare** selezionare **Add additional process servers to scale out deployment** (Aggiungere server di elaborazione per aumentare le istanze di distribuzione).
5. Completare la procedura guidata come per la [configurazione](#step-2-set-up-the-source-environment) del server di configurazione.

    ![Screenshot della procedura guidata per l'installazione unificata di Azure Site Recovery](./media/site-recovery-vmware-to-azure/add-ps1.png)
6. In **Dettagli del server di configurazione** specificare l'indirizzo IP del server di configurazione e la passphrase. Per ottenere la passphrase, eseguire **[SiteRecoveryInstallationFolder]\home\sysystems\bin\genpassphrase.exe –n** nel server di configurazione.

    ![Screenshot della pagina dei dettagli del server di configurazione](./media/site-recovery-vmware-to-azure/add-ps2.png)

### <a name="migrate-machines-to-use-the-new-process-server"></a>Eseguire la migrazione dei computer per usare il nuovo server di elaborazione
1. In **Impostazioni** > **Server di Site Recovery** fare clic sul server di configurazione e quindi espandere **Server di elaborazione**.

    ![Screenshot della finestra di dialogo Server di elaborazione](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
2. Fare clic con il pulsante destro del mouse sul server di elaborazione corrente e scegliere **Passa a**.

    ![Screenshot della finestra di dialogo Server di configurazione](./media/site-recovery-vmware-to-azure/migrate-ps3.png)
3. In **Selezionare il server di elaborazione di destinazione** selezionare il nuovo server di elaborazione da usare e le macchine virtuali che dovranno essere gestite dal server. Fare clic sull'icona informazioni per ottenere informazioni sul server. Per consentire di prendere le decisioni relative al carico, viene visualizzato lo spazio medio necessario per replicare ogni macchina virtuale selezionata nel nuovo server di elaborazione. Fare clic sul segno di spunta per avviare la replica nel nuovo server di elaborazione.


## <a name="next-steps"></a>Passaggi successivi

Scaricare ed eseguire [Azure Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner)

