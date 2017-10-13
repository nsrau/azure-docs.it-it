---
title: Eseguire la migrazione ad Archiviazione Premium di Azure con Azure Site Recovery | Documentazione Microsoft
description: Eseguire la migrazione delle macchine virtuali esistenti ad Archiviazione Premium di Azure usando Site Recovery. Archiviazione Premium offre prestazioni elevate e supporto per dischi a bassa latenza per carichi di lavoro con I/O intensivo in esecuzione su Macchine virtuali di Azure.
services: storage
cloud: Azure
documentationcenter: na
author: luywang
manager: kavithag
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: luywang
ms.openlocfilehash: 45d89cb9b51e7091f8921d3f58d3a4594851ca7f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-to-premium-storage-by-using-azure-site-recovery"></a>Migrazione ad Archiviazione Premium con Azure Site Recovery

[Archiviazione Premium di Azure](storage-premium-storage.md) offre prestazioni elevate e supporto per dischi a bassa latenza per le macchine virtuali (VM) che eseguono carichi di lavoro con I/O intensivo. Questa guida consente agli utenti di eseguire la migrazione dei dischi delle macchine virtuali da un account di archiviazione Standard a un account di archiviazione Premium usando [Azure Site Recovery](../../site-recovery/site-recovery-overview.md).

Il servizio Azure Site Recovery contribuisce alla strategia di continuità aziendale e ripristino di emergenza orchestrando la replica dei server fisici locali e delle macchine virtuali sul cloud (Azure) o in un data center secondario. In caso di interruzioni nella località primaria, verrà eseguito il failover alla località secondaria per mantenere disponibili applicazioni e carichi di lavoro. Quando la località primaria sarà di nuovo operativa, si tornerà a tale località. 

Site Recovery offre failover di test per supportare analisi del ripristino di emergenza senza interessare gli ambienti di produzione. È possibile eseguire failover con perdite di dati minime (a seconda della frequenza di replica) per emergenze impreviste. Nello scenario di migrazione all'Archiviazione Premium, è possibile usare il [failover in Site Recovery](../../site-recovery/site-recovery-failover.md) per eseguire la migrazione dei dischi di destinazione a un account di archiviazione Premium.

È consigliabile eseguire la migrazione ad Archiviazione Premium usando Site Recovery perché questa opzione garantisce un tempo di inattività minimo ed evita di dover eseguire manualmente la copia dei dischi e la creazione di nuove VM. Site Recovery copierà in modo sistematico i dischi e creerà le nuove VM durante il failover. 

Site Recovery supporta diversi tipi di failover con un tempo di inattività minimo o inesistente. Per pianificare il tempo di inattività e stimare la perdita di dati, vedere i [Tipi di failover in Site Recovery](../../site-recovery/site-recovery-failover.md). Se si [prepara la connessione alle macchine virtuali di Azure dopo il failover](../../site-recovery/vmware-walkthrough-overview.md), sarà possibile connettersi alla macchina virtuale di Azure usando RDP dopo il failover.

![Diagramma sul ripristino di emergenza][1]

## <a name="azure-site-recovery-components"></a>Componenti di Azure Site Recovery

Questi componenti di Site Recovery sono rilevanti per questo scenario di migrazione:

* Un **Server di configurazione** è una VM di Azure che coordina le comunicazioni e gestisce i processi di ripristino e replica dei dati. In questa macchina virtuale si eseguirà un singolo file di installazione per installare il server di configurazione e un componente aggiuntivo, denominato server di elaborazione, come gateway di replica. Vedere le informazioni sui [prerequisiti del server di configurazione](../../site-recovery/vmware-walkthrough-overview.md). Il server di configurazione deve essere configurato una sola volta e può essere usato per tutte le migrazioni alla stessa area.

* **Server di elaborazione**: agisce come un gateway di replica: 

  1. Riceve i dati di replica da macchine virtuali di origine.
  2. Consente di ottimizzare i dati mediante memorizzazione nella cache, compressione e crittografia.
  3. Invia i dati a un account di archiviazione. 

  Gestisce anche l'installazione push del servizio Mobility nelle VM di origine ed esegue l'individuazione automatica delle VM di origine. Il server di elaborazione predefinito viene installato nel server di configurazione. È possibile distribuire altri server di elaborazione autonomi per ridimensionare l'ambiente. Vedere le informazioni sulle [procedure consigliate per la distribuzione del server di elaborazione](https://azure.microsoft.com/blog/best-practices-for-process-server-deployment-when-protecting-vmware-and-physical-workloads-with-azure-site-recovery/) e sulla [distribuzione di server di elaborazione aggiuntivi](../../site-recovery/site-recovery-plan-capacity-vmware.md#deploy-additional-process-servers). Il server di processo deve essere configurato una sola volta e può essere usato per tutte le migrazioni alla stessa area.

* Un **Servizio Mobility** è un componente che viene distribuito in ogni macchina virtuale standard che si vuole replicare. Acquisisce le scritture di dati nella VM standard e le inoltra al server di elaborazione. Vedere le informazioni sui [prerequisiti dei computer replicati](../../site-recovery/vmware-walkthrough-overview.md).

Il grafico seguente mostra l'interazione tra questi componenti:

![Interazione dei componenti di Site Recovery][15]

> [!NOTE]
> Site Recovery non supporta la migrazione di dischi di Spazi di archiviazione.

Per i componenti aggiuntivi di altri scenari, vedere [Architettura dello scenario](../../site-recovery/vmware-walkthrough-overview.md).

## <a name="azure-essentials"></a>Informazioni di base su Azure

Questi sono i requisiti di Azure per questo scenario di migrazione:

* Una sottoscrizione di Azure.
* Un account di archiviazione Premium per archiviare i dati replicati.
* Una rete virtuale di Azure a cui le macchine virtuali di Azure possano connettersi quando vengono create in fase di failover. La rete virtuale di Azure deve trovarsi nella stessa area dell'insieme di esecuzione di Site Recovery.
* Un account di archiviazione Standard di Azure in cui archiviare i log delle repliche. Può essere lo stesso account di archiviazione dei dischi delle macchine virtuali di cui viene eseguita la migrazione.

## <a name="prerequisites"></a>Prerequisiti

* Conoscere i componenti dello scenario di migrazione pertinenti nella sezione precedente.
* Pianificare il tempo di inattività dopo avere letto le informazioni sul [failover in Site Recovery](../../site-recovery/site-recovery-failover.md).

## <a name="setup-and-migration-steps"></a>Passaggi di configurazione e migrazione

È possibile usare Site Recovery per eseguire la migrazione di VM IaaS di Azure tra aree geografiche o nella stessa area. Le istruzioni seguenti sono adattate a questo scenario di migrazione dall'articolo [Eseguire la replica di macchine virtuali VMware o server fisici in Azure](../../site-recovery/vmware-walkthrough-overview.md). Seguire i collegamenti per conoscere i passaggi dettagliati a completamento delle istruzioni del presente articolo.

### <a name="step-1-create-a-recovery-services-vault"></a>Passaggio 1: Creare l'insieme di credenziali dei servizi di ripristino

1. Aprire il [portale di Azure](https://portal.azure.com).
2. Selezionare **Nuovo** > **Gestione** > **Backup** e **Site Recovery (OMS)**. In alternativa, è possibile selezionare **Sfoglia** > **Insieme di credenziali dei servizi di ripristino** > **Aggiungi**. 
3. Specificare un'area in cui verranno replicate le macchine virtuali. Per eseguire la migrazione nella stessa area, selezionare l'area in cui si trovano le VM di origine e gli account di archiviazione di origine. 

### <a name="step-2-choose-your-protection-goals"></a>Passaggio 2: Scegliere gli obiettivi della protezione 

1. Nella VM in cui si vuole installare il server di configurazione aprire il [portale di Azure](https://portal.azure.com).
2. Andare su **Insiemi di credenziali di Recovery Services**  > **Impostazioni** > **Site Recovery** > **Passaggio 1: Preparare l'infrastruttura** > **Obiettivo di protezione**.

   ![Passare al riquadro Obiettivo di protezione][2]

3. In **Obiettivo di protezione**, nel primo elenco a discesa sezionare **In Azure**. Nel secondo elenco a discesa, selezionare **Non virtualizzato/Altro**, quindi fare clic su **OK**.

   ![Riquadro Obiettivo di protezione con caselle compilate][3]

### <a name="step-3-set-up-the-source-environment-configuration-server"></a>Passaggio 3: Configurare l'ambiente di origine (server di configurazione)

1. Scaricare l'**installazione unificata di Azure Site Recovery** e la chiave di registrazione dell'insieme di credenziali andando ai pannelli **Preparare l'infrastruttura** > **Prepara origine** > **Aggiungi server**. 
 
   Sarà necessaria la chiave di registrazione dell'insieme di credenziali per eseguire l'installazione unificata. La chiave è valida per cinque giorni dal momento in cui viene generata.

   ![Passare al riquadro Aggiungi Server][4]

2. Nel riquadro **Aggiungi Server**, aggiungere un server di configurazione.

   ![Riquadro Aggiungi Server con Server di configurazione selezionato][5]

3. Nella macchina virtuale che si usa come server di configurazione, eseguire l'Installazione unificata per installare il server di configurazione e il server di elaborazione. È possibile [seguire gli screenshot](../../site-recovery/vmware-walkthrough-overview.md) per completare l'installazione. È possibile vedere gli screenshot seguenti per i passaggi specificati per questo scenario di migrazione.

   1. In **Before you begin** (Prima di iniziare) selezionare **Install the configuration server and process server** (Installare il server di configurazione e il server di elaborazione).

      ![Pagina Prima di iniziare][6]

   2. In **Registrazione** cercare e selezionare la chiave di registrazione scaricata dall'insieme di credenziali.

      ![Pagina di registrazione][7]

   3. In **Dettagli ambiente** specificare se si vuole eseguire la replica di VM VMware. Per questo scenario di migrazione, scegliere **No**.

      ![Pagina dei dettagli dell'ambiente][8]

4. Al termine dell'installazione, eseguire la procedura seguente nella finestra **Microsoft Azure Site Recovery Configuration Server** (Server di configurazione Microsoft Azure Site Recovery).
 
   1. Usare la scheda **Gestisci account** per creare l'account che Site Recovery può usare per l'individuazione automatica. Nello scenario sulla protezione dei computer fisici la configurazione dell'account non è pertinente, ma è necessario almeno un account per abilitare uno dei passaggi seguenti. In questo caso, è possibile usare qualsiasi account e password. 
   2. Usare la scheda **Vault Registration** (Registrazione dell'insieme di credenziali) per caricare il file delle credenziali dell'insieme di credenziali.

      ![Scheda Vault Registration (Registrazione dell'insieme di credenziali)][9]

### <a name="step-4-set-up-the-target-environment"></a>Passaggio 4: Configurare l'ambiente di destinazione

Selezionare **Preparare l'infrastruttura** > **Destinazione** e specificare il modello di distribuzione da usare per le macchine virtuali dopo il failover. È possibile scegliere **Classica** o **Resource Manager**, a seconda dello scenario.

![Riquadro di destinazione][10]

Site Recovery verifica la disponibilità di uno o più account di archiviazione di Azure e reti compatibili. 

> [!NOTE]
> Se si usa un account di archiviazione Premium per i dati replicati, è necessario configurare un account di archiviazione Standard aggiuntivo per l'archiviazione dei log di replica.

### <a name="step-5-set-up-replication-settings"></a>Passaggio 5: Configurare le impostazioni di replica

Seguire [Configurare le impostazioni di replica](../../site-recovery/vmware-walkthrough-overview.md) per verificare che il server di configurazione venga associato correttamente ai criteri di replica creati.

### <a name="step-6-plan-capacity"></a>Passaggio 6: Pianificare la capacità

1. Usare [Capacity Planner](../../site-recovery/site-recovery-capacity-planner.md) per stimare con precisione la larghezza di banda di rete, lo spazio di archiviazione e gli altri requisiti per poter soddisfare le esigenze di replica. 
2. Al termine scegliere **Operazione completata** in **È stata completata la pianificazione della capacità?**.

   ![Casella per confermare di aver completato la pianificazione della capacità][11]

### <a name="step-7-install-the-mobility-service-and-enable-replication"></a>Passaggio 7:Installare il servizio Mobility e abilitare la replica

1. È possibile scegliere di [effettuare il push dell'installazione](../../site-recovery/vmware-walkthrough-overview.md) nelle macchine virtuali di origine o di [installare manualmente il servizio Mobility](../../site-recovery/site-recovery-vmware-to-azure-install-mob-svc.md) nelle macchine virtuali di origine. È possibile trovare il requisito del push dell'installazione e il percorso del programma di installazione manuale nel collegamento fornito. Se si esegue un'installazione manuale, potrebbe essere necessario usare un indirizzo IP interno per trovare il server di configurazione.

   ![Pagina dei dettagli del server di configurazione][12]

   La macchina virtuale sottoposta a failover avrà due dischi temporanei: uno dalla macchina virtuale primaria e l'altro creato durante il provisioning della macchina virtuale nell'area di ripristino. Per escludere il disco temporaneo prima della replica, installare il servizio Mobility prima di abilitare la replica. Per altre informazioni su come escludere il disco temporaneo, vedere [Escludere dischi dalla replica](../../site-recovery/vmware-walkthrough-overview.md).

2. Per abilitare la replica, procedere come descritto di seguito:
   1. Selezionare **Eseguire la replica dell'applicazione** > **Origine**. Dopo avere abilitato la replica per la prima volta, è necessario selezionare **+Replica** nell'insieme di credenziali per abilitare la replica per altre macchine.
   2. Nel passaggio 1 configurare l'**Origine** come server di elaborazione.
   3. Nel passaggio 2 specificare il modello di distribuzione successivo al failover, un account di archiviazione Premium a cui eseguire la migrazione, un account di archiviazione Standard per salvare i log e una rete virtuale in cui effettuare il failover.
   4. Nel passaggio 3, aggiungere macchine virtuali protette in base all'indirizzo IP. (Potrebbe essere necessario un indirizzo IP interno per trovarle).
   5. Nel passaggio 4, configurare le proprietà selezionando gli account configurati prima nel server di elaborazione.
   6. Nel passaggio 5, scegliere i criteri di replica creati prima al "Passaggio 5: Configurare le impostazioni di replica".
   7. Selezionare **OK**.

   > [!NOTE]
   > Quando una VM di Azure viene deallocata e riavviata, non è certo che ottenga lo stesso indirizzo IP. Se l'indirizzo IP del server di configurazione/server di elaborazione o le macchine virtuali di Azure protette cambiano, la replica in questo scenario potrebbe non funzionare correttamente.

   ![Abilitare il riquadro di replica con l'origine selezionata][13]

Quando si progetta l'ambiente di Archiviazione di Azure, è consigliabile usare account di archiviazione distinti per ogni VM in un set di disponibilità. È consigliabile seguire le procedure consigliate nel livello di archiviazione per [usare più account di archiviazione per ogni set di disponibilità](../../virtual-machines/windows/manage-availability.md). Distribuire i dischi delle VM in più account di archiviazione consente di migliorare la disponibilità di archiviazione e di suddividere le operazioni di I/O nell'infrastruttura di archiviazione di Azure.

Se le macchine virtuali sono in un set di disponibilità, invece di replicare i dischi di tutte le macchine virtuali in un account di archiviazione, è consigliabile eseguire più volte la migrazione di più macchine virtuali, in modo che le macchine virtuali nello stesso set di disponibilità non condividano un solo account di archiviazione. Usare il riquadro **Abilitazione della replica** per configurare contemporaneamente un account di archiviazione di destinazione per ogni VM.
 
È possibile scegliere un modello di distribuzione post-failover adatto alle proprie esigenze. Se si sceglie Azure Resource Manager come il modello distribuzione dopo il failover, è possibile eseguire il failover da una macchina virtuale (Resource Manager) verso una macchina virtuale (Resource Manager) oppure da una macchina virtuale (classica) a una macchina virtuale (Resource Manager).

### <a name="step-8-run-a-test-failover"></a>Passaggio 8: Eseguire un failover di test

Per controllare se la replica è completa, selezionare l'istanza Site Recovery, quindi selezionare **Impostazioni** > **Elementi replicati**. Verranno visualizzati lo stato e la percentuale del processo di replica. 

Al termine della replica iniziale, eseguire un failover di test per convalidare la strategia di replica. Per i passaggi dettagliati del failover di test, vedere [Eseguire un failover di test in Site Recovery](../../site-recovery/vmware-walkthrough-overview.md). 

> [!NOTE]
> Verificare che le macchine virtuali e la strategia di replica soddisfino i requisiti prima di eseguire un failover non pianificato. Per altre informazioni sul failover di test, leggere [Failover di test in Azure Site Recovery](../../site-recovery/site-recovery-test-failover-to-azure.md).

È possibile visualizzare lo stato del failover di test in **Impostazioni** > **Processi** > *NOME_PIANO_FAILOVER*. Nel riquadro verranno visualizzati una suddivisione dei passaggi e i risultati positivi/negativi. Se il failover di test ha esito negativo in qualche passaggio, fare clic sul passaggio per controllare il messaggio di errore. 

### <a name="step-9-run-a-failover"></a>Passaggio 9: Eseguire un failover

Al termine del failover di test, eseguire un failover per eseguire la migrazione dei dischi all'Archiviazione Premium e replicare le istanze delle macchine virtuali. Seguire la procedura dettagliata in [Eseguire un failover](../../site-recovery/site-recovery-failover.md#run-a-failover). 

Assicurarsi di selezionare **Arresta le macchine virtuali e sincronizza i dati più recenti** per specificare che Site Recovery deve provare ad arrestare le macchine virtuali protette e sincronizzare i dati in modo che venga eseguito il failover dei dati più recenti. Se non si seleziona questa opzione o il tentativo ha esito negativo, il failover avverrà dall'ultimo punto di recupero disponibile per la macchina virtuale. 

Site Recovery creerà un'istanza di una macchina virtuale di tipo uguale o simile a quella di una macchina virtuale che supporta Archiviazione Premium. Per controllare le prestazioni e il prezzo delle diverse istanze delle VM, vedere [Prezzi di Macchine virtuali Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) o [Prezzi di Macchine virtuali Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="post-migration-steps"></a>Passaggi post-migrazione

1. **Configurare le VM replicate per il set di disponibilità, se applicabile**. Site Recovery non supporta la migrazione di VM con il set di disponibilità. A seconda della distribuzione della VM replicata, eseguire una di queste operazioni:
   * Per una macchina virtuale creata usando il modello di distribuzione classica: aggiungere la macchina virtuale al set di disponibilità nel portale di Azure. Per i passaggi dettagliati, vedere [Aggiungere una macchina virtuale esistente a un set di disponibilità](../../virtual-machines/windows/classic/configure-availability.md#addmachine).
   * Per le macchine virtuali create tramite il modello di distribuzione Resource Manager: salvare la configurazione della macchina virtuale e quindi eliminare e ricreare le macchine virtuali nel set di disponibilità. A questo scopo, usare lo script in [Set Azure Resource Manager VM Availability Set](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4) (Impostare il set di disponibilità di VM di Azure Resource Manager). Prima di eseguire questo script, controllare le relative limitazioni e pianificare il tempo di inattività.

2. **Eliminare le VM e i dischi precedenti**. Verificare che i dischi Premium siano coerenti con i dischi di origine e che le nuove macchine virtuali eseguano la stessa funzione delle macchine virtuali di origine. Nel modello di distribuzione Resource Manager, eliminare la macchina virtuale ed eliminare i dischi dagli account di archiviazione di origine nel portale di Azure. Nel modello di distribuzione classica è possibile eliminare la macchina virtuale e i dischi nel portale classico o nel portale di Azure. Se si verifica un problema per cui il disco non viene eliminato anche se è stata eliminata la macchina virtuale, vedere [Risolvere gli errori quando si eliminano dischi rigidi virtuali di Azure in una distribuzione Resource Manager](storage-resource-manager-cannot-delete-storage-account-container-vhd.md).

3. **Pulire l'infrastruttura di Azure Site Recovery**. Se Site Recovery non è più necessario, è possibile rimuovere l'infrastruttura. Eliminare gli elementi replicati, il server di configurazione e i criteri di ripristino, quindi eliminare l'insieme di credenziali di Azure Site Recovery.

## <a name="troubleshooting"></a>Risoluzione dei problemi

* [Monitorare e risolvere i problemi di protezione per le macchine virtuali e i server fisici](../../site-recovery/site-recovery-monitoring-and-troubleshooting.md)
* [Forum di Microsoft Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)

## <a name="next-steps"></a>Passaggi successivi

Per scenari specifici per la migrazione di macchine virtuali, controllare le risorse seguenti:

* [Eseguire la migrazione di macchine virtuali di Azure tra account di archiviazione](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Creare e caricare un disco rigido virtuale Windows Server in Azure](../../virtual-machines/windows/classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Creazione e caricamento di un disco rigido virtuale contenente il sistema operativo Linux](../../virtual-machines/linux/classic/create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Migrazione di macchine virtuali da Amazon AWS a Microsoft Azure](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Inoltre, controllare le seguenti risorse per altre informazioni su Archiviazione di Azure e sulle macchine virtuali di Azure:

* [Archiviazione di Azure](https://azure.microsoft.com/documentation/services/storage/)
* [Macchine virtuali di Azure](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](storage-premium-storage.md)

[1]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-1.png
[2]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-2.png
[3]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-3.png
[4]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-4.png
[5]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-5.png
[6]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-6.PNG
[7]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-7.PNG
[8]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-8.PNG
[9]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-9.PNG
[10]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-10.png
[11]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-11.PNG
[12]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-12.PNG
[13]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-13.png
[14]:../site-recovery/media/site-recovery-vmware-to-azure/v2a-architecture-henry.png
[15]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-14.png
