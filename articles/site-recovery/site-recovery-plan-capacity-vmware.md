---
title: Pianificare la capacità e la scalabilità per il ripristino di emergenza da VMware ad Azure tramite Azure Site Recovery | Microsoft Docs
description: Questo articolo aiuta a pianificare la capacità e la scalabilità quando si configura il ripristino di emergenza di macchine virtuali di VMware in Azure tramite Azure Site Recovery.
author: nsoneji
manager: garavd
ms.service: site-recovery
ms.date: 4/9/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 0bf1b34295d827124198206e743bc21d5f7eb904
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747895"
---
# <a name="plan-capacity-and-scaling-for-vmware-disaster-recovery-to-azure"></a>Pianificare la capacità e la scalabilità per il ripristino di emergenza di VMware in Azure

Consultare questo articolo per pianificare la capacità e la scalabilità durante la replica locale di macchine virtuali VMware e server fisici in Azure tramite [Azure Site Recovery](site-recovery-overview.md).

## <a name="how-do-i-start-capacity-planning"></a>Come si inizia per pianificare la capacità?

Per conoscere i requisiti dell'infrastruttura per Azure Site Recovery, raccogliere informazioni sull'ambiente di replica eseguendo [Azure Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner-doc) per la replica VMware. Per altre informazioni, consultare [Informazioni su Azure Site Recovery Deployment Planner per il ripristino di emergenza da VMware ad Azure](site-recovery-deployment-planner.md). 

Site Recovery Deployment Planner fornisce un report con informazioni complete sulle macchine virtuali compatibili e non, i dischi per ogni macchina virtuale e la varianza di dati per ogni disco. Lo strumento riepiloga anche i requisiti della larghezza di banda di rete per soddisfare l'RPO di destinazione e l'infrastruttura di Azure necessaria per la corretta esecuzione della replica e del failover del test.

## <a name="capacity-considerations"></a>Considerazioni sulla capacità

Componente | Dettagli
--- | ---
**Replica** | **Frequenza di modifica giornaliera massima**: una macchina protetta può usare un solo server di elaborazione. Un singolo server di elaborazione può gestire fino a 2 TB di frequenza di modifica giornaliera. La massima frequenza di modifica dei dati giornaliera supportata per una macchina protetta è quindi di 2 TB.<br /><br /> **Velocità effettiva massima**: una macchina replicata può appartenere a un account di archiviazione in Azure. Un account di Archiviazione di Azure standard può gestire fino a un massimo di 20.000 richieste al secondo. È consigliabile limitare il numero di operazioni di input/output al secondo (IOPS) a 20.000 in una macchina di origine. Se, ad esempio, si ha un computer di origine con 5 dischi e ogni disco genera 120 IOPS (dimensione pari a 8 K) nella macchina di origine, il computer di origine rientra nel limite di Azure di 500 IOPS per disco. Il numero di account di archiviazione necessari è uguale al numero complessivo di IOPS della macchina di origine diviso per 20.000.
**Server di configurazione** | Il server di configurazione deve essere in grado di gestire la capacità di frequenza di modifica giornaliera in tutti i carichi di lavoro in esecuzione nei computer protetti. La macchina di configurazione deve avere una larghezza di banda sufficiente per eseguire la replica continua dei dati in Archiviazione di Azure.<br /><br /> Come procedura consigliata, il server di configurazione deve trovarsi nella stessa rete e nello stesso segmento LAN dei computer da proteggere. È possibile inserire il server di configurazione in una rete diversa, ma i computer da proteggere devono avere visibilità di rete L3.<br /><br /> Le dimensioni consigliate per il server di configurazione sono riepilogate nella tabella riportata nella sezione successiva.
**Server di elaborazione** | Per impostazione predefinita, il primo server di elaborazione viene installato nel server di configurazione. È possibile distribuire altri server di elaborazione per ridimensionare l'ambiente. <br /><br /> Il server di elaborazione riceve i dati di replica dai computer protetti. Il server di elaborazione ottimizza i dati tramite la memorizzazione nella cache, la compressione e la crittografia. Quindi il server di elaborazione invia i dati ad Azure. La macchina del server di elaborazione deve avere risorse sufficienti per eseguire queste attività.<br /><br /> Il server di elaborazione usa una cache basata su disco. Usare un disco della cache separato di almeno 600 GB per gestire le modifiche ai dati archiviate in caso di colli di bottiglia o interruzioni della rete.

## <a name="size-recommendations-for-the-configuration-server-and-inbuilt-process-server"></a>Dimensioni consigliate per il server di configurazione e il server di elaborazione predefinito

Un server di configurazione che usa un server di elaborazione predefinito per proteggere il carico di lavoro può gestire fino a 200 macchine virtuali basate sulle configurazioni seguenti:

CPU | Memoria | Dimensione disco cache | Frequenza di modifica dei dati | Computer protetti
--- | --- | --- | --- | ---
8 vCPU (2 socket * 4 core \@ 2,5 GHz) | 16 GB | 300 GB | 500 GB o inferiore | Per replicare meno di 100 computer.
12 vCPU (2 socket * 6 core \@ 2,5 GHz) | 18 GB | 600 GB | Da 501 GB a 1 TB | Per replicare da 100 a 150 computer.
16 vCPU (2 socket * 8 core \@ 2,5 GHz) | 32 GB | 1 TB | >1 TB-2 TB | Per replicare da 151 a 200 computer.
Distribuire un altro server di configurazione tramite il [modello OVF](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template). | | | | Distribuire un nuovo server di configurazione se si esegue la replica di più di 200 computer.
Distribuire un altro [server di elaborazione](vmware-azure-set-up-process-server-scale.md#download-installation-file). | | | Più di 2 TB| Distribuire un nuovo server di elaborazione scale-out se la frequenza di modifica dei dati giornaliera complessiva è maggiore di 2 TB.

In queste configurazioni:

* Ogni computer di origine dispone di tre dischi da 100 GB ognuno.
* La risorsa di archiviazione di benchmarking usata per le misurazioni del disco della cache è di 8 unità SAS a 10.000 RPM con RAID 10.

## <a name="size-recommendations-for-the-process-server"></a>Dimensioni consigliate per il server di elaborazione

Il server di elaborazione è il componente che gestisce la replica dei dati in Azure Site Recovery. Se la frequenza di modifica giornaliera è superiore a 2 TB, è necessario aggiungere altri server di elaborazione scale-out per gestire il carico di replica. Per la scalabilità orizzontale è possibile:

* Aumentare il numero di server di configurazione eseguendo la distribuzione tramite il [modello OVF](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template). Ad esempio, è possibile proteggere fino a 400 computer con due server di configurazione.
* Aggiungere [server di elaborazione scale-out](vmware-azure-set-up-process-server-scale.md#download-installation-file). Usare i server di elaborazione scale-out per gestire il traffico di replica al posto del server di configurazione o in aggiunta.

La tabella seguente descrive questo scenario:

* Configurare un server di elaborazione scale-out.
* Le macchine virtuali protette sono state configurate per l'uso del server di elaborazione scale-out.
* Ogni computer di origine protetto dispone di tre dischi da 100 GB ognuno.

Server di elaborazione aggiuntivo | Dimensione disco cache | Frequenza di modifica dei dati | Computer protetti
--- | --- | --- | ---
4 vCPU (2 socket * 2 core \@ 2,5 GHz), 8 GB di memoria | 300 GB | 250 GB o inferiore | Per replicare un massimo di 85 computer.
8 vCPU (2 socket * 4 core \@ 2,5 GHz), 12 GB di memoria | 600 GB | Da 251 GB a 1 TB | Per replicare da 86 a 150 computer.
12 vCPU (2 socket * 6 core \@ 2,5 GHz), 24 GB di memoria | 1 TB | >1 TB-2 TB | Per replicare da 151 a 225 computer.

Il modo in cui i server vengono ridimensionati dipende dalle preferenze per il modello di aumento delle prestazioni o di aumento del numero di istanze. Per aumentare le prestazioni, distribuire alcuni server di elaborazione e di configurazione avanzati. Per aggiungere un livello, distribuire più server con un numero minore di risorse. Ad esempio, per proteggere 200 computer con una frequenza di modifica dei dati giornaliera complessiva di 1,5 TB, è possibile eseguire una di queste operazioni:

* Configurare un singolo server di elaborazione (16 vCPU, 24 GB di RAM).
* Configurare due server di elaborazione (2 x 8 vCPU, 2* 12 GB di RAM).

## <a name="control-network-bandwidth"></a>Controllare la larghezza di banda della rete

Dopo aver usato [Site Recovery Deployment Planner](site-recovery-deployment-planner.md) per calcolare la larghezza di banda necessaria per la replica (la replica iniziale e successivamente il delta), sono disponibili due opzioni per controllare la quantità di larghezza di banda usata per la replica:

* **Limitare la larghezza di banda**: il traffico VMware che viene replicato in Azure passa attraverso un server di elaborazione specifico. È possibile limitare la larghezza di banda nei computer in esecuzione come server di elaborazione.
* **Influire sulla larghezza di banda**: è possibile influire sulla larghezza di banda usata per la replica usando un paio di chiavi del registro di sistema:
  * Il valore del Registro di sistema **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM** specifica il numero di thread usati per il trasferimento dati di un disco, durante la replica iniziale o differenziale. Un valore più elevato aumenta la larghezza di banda di rete usata per la replica.
  * Il valore del Registro **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\DownloadThreadsPerVM** specifica il numero di thread usati per il trasferimento dati durante il failback.

### <a name="throttle-bandwidth"></a>Limitare la larghezza di banda

1. Aprire lo snap-in MMC di Azure Backup nel computer usato come server di elaborazione. Per impostazione predefinita, un collegamento per il backup è disponibile sul desktop o nella cartella seguente: c:\Programmi\Microsoft Azure Recovery Services Agent\bin.
2. Nello snap-in selezionare **Modifica proprietà**.

    ![Screenshot dello snap-in di MMC di Azure Backup per modificare le proprietà](./media/site-recovery-vmware-to-azure/throttle1.png)
3. Nella scheda **Limitazione larghezza di banda rete** selezionare **Abilita la limitazione all'utilizzo della larghezza di banda Internet per le operazioni di backup**. Impostare i limiti per le ore lavorative e non lavorative. Gli intervalli validi sono compresi tra 512 Kbps e 1.023 Mbps.

    ![Screenshot della finestra di dialogo Proprietà di Azure Backup](./media/site-recovery-vmware-to-azure/throttle2.png)

È anche possibile usare il cmdlet [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) per impostare la limitazione. Ad esempio:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** indica che non è necessaria alcuna limitazione.

### <a name="alter-the-network-bandwidth-for-a-vm"></a>Modificare la larghezza di banda della rete per una macchina virtuale

1. Nel Registro di sistema della macchina virtuale passare a **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
   * Per intervenire sul traffico della larghezza di banda in un disco di replica, modificare il valore di **UploadThreadsPerVM**. Se la chiave non esiste, crearla.
   * Per intervenire sulla larghezza di banda per il traffico di failback da Azure, modificare il valore di **DownloadThreadsPerVM**.
2. Il valore predefinito per ogni chiave è **4**. In una rete con provisioning eccessivo è necessario modificare i valori predefiniti di queste chiavi del Registro di sistema. Il valore massimo che è possibile usare è **32**. Monitorare il traffico per ottimizzare il valore.

## <a name="set-up-the-site-recovery-infrastructure-to-protect-more-than-500-vms"></a>Configurare l'infrastruttura di Azure Site Recovery per proteggere più di 500 macchine virtuali

Prima di configurare l'infrastruttura di Azure Site Recovery è necessario accedere all'ambiente per misurare i fattori seguenti: le macchine virtuali compatibili, la frequenza di modifica dei dati giornaliera, la larghezza di banda di rete richiesta per l'RPO desiderato, il numero di componenti di Azure Site Recovery necessari e il tempo impiegato per completare la replica iniziale. Completare i passaggi seguenti per raccogliere le informazioni necessarie:

1. Per misurare questi parametri, eseguire Site Recovery Deployment Planner nel proprio ambiente. Per linee guida utili, consultare [Informazioni su Azure Site Recovery Deployment Planner per il ripristino di emergenza da VMware ad Azure](site-recovery-deployment-planner.md).
2. Distribuire un server di configurazione che soddisfi le [dimensioni consigliate per il server di configurazione](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server). Se il carico di lavoro di produzione è superiore a 650 macchine virtuali, distribuire un altro server di configurazione.
3. In base alla frequenza di modifica dei dati giornaliera misurata, distribuire i [server di elaborazione scale-out](vmware-azure-set-up-process-server-scale.md#download-installation-file) seguendo le [linee guida sulle dimensioni](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server).
4. Se si prevede che la frequenza di modifica dei dati per una macchina virtuale disco superi 2 MBps, assicurarsi di usare Managed disks Premium. Site Recovery Deployment Planner viene eseguito per un periodo di tempo specifico. I picchi della frequenza di modifica dei dati in altri momenti potrebbero non comparire nel report.
5. [Impostare la larghezza di banda di rete](site-recovery-plan-capacity-vmware.md#control-network-bandwidth) a seconda dell'RPO che si desidera ottenere.
6. Al termine della configurazione dell'infrastruttura abilitare il ripristino di emergenza per il carico di lavoro. Per informazioni sulla procedura, consultare [Configurare l'ambiente di origine per la replica da VMware in Azure](vmware-azure-set-up-source.md).

## <a name="deploy-additional-process-servers"></a>Distribuire server di elaborazione aggiuntivi

Se si amplia la distribuzione a oltre 200 computer di origine oppure la varianza totale giornaliera è superiore a 2 TB, sono necessari server di elaborazione aggiuntivi per gestire il volume di traffico. Il prodotto è stato migliorato nella versione 9,24 per fornire [avvisi del server di elaborazione](vmware-physical-azure-monitor-process-server.md#process-server-alerts) su quando configurare un server di elaborazione con scalabilità orizzontale. [Configurare il server di elaborazione](vmware-azure-set-up-process-server-scale.md) per proteggere i nuovi computer di origine o [bilanciare il carico](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load).

### <a name="migrate-machines-to-use-the-new-process-server"></a>Eseguire la migrazione dei computer per usare il nuovo server di elaborazione

1. Selezionare **Impostazioni** > **Server di Site Recovery**. Selezionare il server di configurazione, quindi espandere **Server di elaborazione**.

    ![Screenshot della finestra di dialogo Server di elaborazione](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
2. Fare clic con il pulsante destro del mouse sul server di elaborazione corrente e scegliere **Passa a**.

    ![Screenshot della finestra di dialogo Server di configurazione](./media/site-recovery-vmware-to-azure/migrate-ps3.png)
3. In **Selezionare il server di elaborazione di destinazione**, selezionare il nuovo server di elaborazione da usare. Quindi selezionare le macchine virtuali che verranno gestite dal server. Selezionare l'icona delle informazioni per ottenere informazioni sul server. Per consentire di prendere le decisioni relative al carico, viene visualizzato lo spazio medio necessario per replicare ogni macchina virtuale selezionata nel nuovo server di elaborazione. Fare clic sul segno di spunta per avviare la replica nel nuovo server di elaborazione.

## <a name="deploy-additional-master-target-servers"></a>Distribuire server di destinazione master aggiuntivi

Negli scenari seguenti, è necessario più di un server di destinazione master:

*   Si vuole proteggere una macchina virtuale basata su Linux.
*   Se il server di destinazione master disponibile nel server di configurazione non ha accesso all'archivio dati della macchina virtuale.
*   Il numero totale di dischi nel server di destinazione master (il numero di dischi locali nel server più il numero di dischi da proteggere) è maggiore di 60.

Per informazioni su come aggiungere un server di destinazione master per una macchina virtuale basata su Linux, consultare [Installare un server di destinazione master Linux per il failback](vmware-azure-install-linux-master-target.md).

Per aggiungere un nuovo server di destinazione master per una macchina virtuale basata su Windows:

1. Passare a **Credenziali di Servizi di ripristino** > **Infrastruttura di Site Recovery** > **Server di configurazione**.
2. Selezionare il server di configurazione necessario e quindi selezionare **Server di destinazione master**.

    ![Screenshot che mostra il pulsante Aggiungi Server di destinazione master](media/site-recovery-plan-capacity-vmware/add-master-target-server.png)
3. Scaricare il file di installazione unificata e quindi eseguire il file nella macchina virtuale per configurare il server di destinazione master.
4. Selezionare**Installare la destinazione master** > **Avanti**.

    ![Screenshot che mostra la selezione dell'opzione Installare la destinazione master](media/site-recovery-plan-capacity-vmware/choose-MT.PNG)
5. Selezionare il percorso di installazione predefinito e quindi selezionare **Installa**.

     ![Screenshot che mostra il percorso di installazione predefinito](media/site-recovery-plan-capacity-vmware/MT-installation.PNG)
6. Selezionare **Procedi alla configurazione** per registrare il server di destinazione master con il server di configurazione.

    ![Screenshot che mostra il pulsante Procedi alla configurazione](media/site-recovery-plan-capacity-vmware/MT-proceed-configuration.PNG)
7. Immettere l'indirizzo IP del server di configurazione e la passphrase. Per informazioni su come generare una passphrase, consultare [Generate a configuration server passphrase (Generare la passphrase del server di configurazione)](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase). 

    ![Screenshot che mostra dove immettere l'indirizzo IP e la passphrase per il server di configurazione](media/site-recovery-plan-capacity-vmware/cs-ip-passphrase.PNG)
8. Selezionare **Registra**. Al termine della registrazione, selezionare **Fine**.

Al termine della registrazione il server è elencato nel portale di Azure in **Credenziali di Servizi di ripristino** > **Infrastruttura di Site Recovery** > **Server di configurazione**, nei server di destinazione master del server di configurazione.

 > [!NOTE]
 > Scaricare la versione più recente del [file di configurazione unificata del server di destinazione master per Windows](https://aka.ms/latestmobsvc).

## <a name="next-steps"></a>Passaggi successivi

Scaricare ed eseguire [Azure Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner).
