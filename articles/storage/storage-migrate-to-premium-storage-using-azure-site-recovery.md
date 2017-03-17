---
title: Migrazione ad Archiviazione Premium di Azure con Azure Site Recovery | Documentazione Microsoft
description: Eseguire la migrazione delle macchine virtuali esistenti ad Archiviazione Premium di Azure usando Site Recovery. Archiviazione Premium offre prestazioni elevate e supporto per dischi a bassa latenza per carichi di lavoro con I/O intensivo in esecuzione su Macchine virtuali di Azure.
services: storage
cloud: Azure
documentationcenter: na
author: luywang
manager: kavithag
ms.assetid: 
ms.service: <service per approved list>
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/1/2016
ms.author: luywang
translationtype: Human Translation
ms.sourcegitcommit: 106e03a5a99134eb6e5744cbf29ba32efc31f0ba
ms.openlocfilehash: d76aa3e62c691c4537684bc70d3a91a3dbb8b446
ms.lasthandoff: 03/02/2017


---
# <a name="migrating-to-premium-storage-using-azure-site-recovery"></a>Migrazione ad Archiviazione Premium con Azure Site Recovery

[Archiviazione Premium di Azure](storage-premium-storage.md) offre prestazioni elevate e supporto per dischi a bassa latenza per le macchine virtuali (VM) che eseguono carichi di lavoro con I/O intensivo. Questa guida consente agli utenti di eseguire la migrazione dei dischi delle VM da un account di archiviazione Standard a un account di archiviazione Premium usando [Azure Site Recovery](../site-recovery/site-recovery-overview.md).

Il servizio Azure Site Recovery contribuisce alla strategia di continuità aziendale e ripristino di emergenza orchestrando la replica dei server fisici locali e delle VM sul cloud (Azure) o in un data center secondario. In caso di interruzioni nella località primaria, verrà eseguito il failover alla località secondaria per mantenere disponibili applicazioni e carichi di lavoro. Quando la località primaria sarà di nuovo operativa, si tornerà a tale località. Site Recovery offre failover di test per supportare analisi del ripristino di emergenza senza interessare gli ambienti di produzione. È possibile eseguire failover con perdite di dati minime (a seconda della frequenza di replica) per emergenze impreviste. Nello scenario di migrazione all'Archiviazione Premium, è possibile usare il [failover in Site Recovery](../site-recovery/site-recovery-failover.md) per eseguire la migrazione dei dischi di destinazione a un account di archiviazione Premium.

È consigliabile eseguire la migrazione ad Archiviazione Premium usando Site Recovery perché questa opzione garantisce un tempo di inattività minimo ed evita di dover eseguire manualmente la copia dei dischi e la creazione di nuove VM. Site Recovery copierà in modo sistematico i dischi e creerà le nuove VM durante il failover. Site Recovery supporta diversi tipi di failover con un tempo di inattività minimo o inesistente. Per pianificare il tempo di inattività e stimare la perdita di dati, vedere la tabella [Tipi di failover](../site-recovery/site-recovery-failover.md) in Site Recovery. Se si [prepara la connessione alle VM di Azure dopo il failover](../site-recovery/site-recovery-vmware-to-azure.md#prepare-vms-for-replication), sarà possibile connettersi alla VM di Azure usando RDP dopo il failover.

![][1]

## <a name="migration-scenario-components"></a>Componenti dello scenario di migrazione

**Componenti di Site Recovery pertinenti in questo scenario di migrazione:**

* Un **server di configurazione** è una VM di Azure che coordina le comunicazioni e gestisce i processi di ripristino e replica dei dati. In questa VM si eseguirà un singolo file di installazione per installare il server di configurazione e un componente aggiuntivo, denominato server di elaborazione, come gateway di replica. Vedere le informazioni sui [prerequisiti del server di configurazione](../site-recovery/site-recovery-vmware-to-azure.md#prerequisites). Il server di configurazione deve essere configurato una sola volta e può essere usato per tutte le migrazioni alla stessa area.

* Un **server di elaborazione** è un gateway di replica che riceve i dati di replica dalle VM di origine, ottimizza i dati con la memorizzazione nella cache, la compressione e la crittografia e li invia a un account di archiviazione. Gestisce anche l'installazione push del servizio Mobility nelle VM di origine ed esegue l'individuazione automatica delle VM di origine. Il server di elaborazione predefinito viene installato nel server di configurazione. È possibile distribuire altri server di elaborazione autonomi per ridimensionare l'ambiente. Vedere le informazioni sulle [procedure consigliate per la distribuzione del server di elaborazione](https://azure.microsoft.com/en-us/blog/best-practices-for-process-server-deployment-when-protecting-vmware-and-physical-workloads-with-azure-site-recovery/) e sulla [distribuzione di server di elaborazione aggiuntivi](../site-recovery/site-recovery-plan-capacity-vmware.md#deploy-additional-process-servers). Il server di elaborazione deve essere configurato una sola volta e può essere usato per tutte le migrazioni alla stessa area.

* Un **servizio Mobility** è un componente che viene distribuito in ogni VM standard che si vuole replicare. Acquisisce le scritture di dati nella VM standard e le inoltra al server di elaborazione. Vedere le informazioni sui [prerequisiti dei computer replicati](../site-recovery/site-recovery-vmware-to-azure.md#prerequisites).

**Informazioni di base su Azure**: 

* Una sottoscrizione di Azure.
* Un account di archiviazione Premium per archiviare i dati replicati.
* Una rete virtuale di Azure a cui le VM di Azure possano connettersi quando vengono create in fase di failover. La rete virtuale di Azure deve trovarsi nella stessa area di quella in cui viene eseguito Site Recovery.
* Un account di archiviazione Standard di Azure in cui archiviare i log delle repliche. Può essere lo stesso account di archiviazione dei dischi delle VM di cui viene eseguita la migrazione.

Il grafico seguente mostra l'interazione tra questi componenti.

![][15]

> [!NOTE]
> Site Recovery non supporta la migrazione di dischi di Spazi di archiviazione.

Per i componenti aggiuntivi di altri scenari, vedere [Architettura dello scenario](../site-recovery/site-recovery-vmware-to-azure.md).

## <a name="prerequisites"></a>Prerequisiti

* Conoscere i componenti dello scenario di migrazione pertinenti nella sezione precedente
* Pianificare il tempo di inattività dopo avere letto le informazioni sul [failover in Site Recovery](../site-recovery/site-recovery-failover.md)

## <a name="setup-and-migration-steps"></a>Passaggi di configurazione e migrazione

È possibile usare Site Recovery per eseguire la migrazione di VM IaaS di Azure tra aree geografiche o nella stessa area. Le istruzioni seguenti sono state adattate a questo scenario di migrazione dall'articolo [Eseguire la replica di macchine virtuali VMware o server fisici in Azure](../site-recovery/site-recovery-vmware-to-azure.md). Seguire i collegamenti per conoscere i passaggi dettagliati a completamento delle istruzioni del presente articolo.

1. **Creare un insieme di credenziali di Servizi di ripristino**. Creare e gestire l'insieme di credenziali di Site Recovery tramite il [portale di Azure](https://portal.azure.com). Fare clic su **Nuovo** > **Gestione** > **Backup** e **Site Recovery (OMS)**. In alternativa, è possibile fare clic su **Sfoglia** > **Insieme di credenziali dei servizi di ripristino** > **Aggiungi**. Le VM verranno replicate nell'area specificata in questo passaggio. Per eseguire la migrazione nella stessa area, selezionare l'area in cui si trovano le VM di origine e gli account di archiviazione di origine. Si noti che la migrazione agli account di archiviazione Premium è supportata solo nel [portale di Azure](https://portal.azure.com), non nel [portale classico](https://manage.windowsazure.com).

2. **Scegliere gli obiettivi della protezione**. Nella VM in cui si vuole installare il server di configurazione aprire il [portale di Azure](https://portal.azure.com). Andare a **Insiemi di credenziali dei servizi di ripristino** > **Impostazioni**. In **Impostazioni** selezionare **Site Recovery**. In **Site Recovery** selezionare **Passaggio 1: Preparare l'infrastruttura**. In **Preparare l'infrastruttura** selezionare **Obiettivo di protezione**.
  
  ![][2]
  
  In **Obiettivo di protezione**, nel primo elenco a discesa sezionare **In Azure**. Nel secondo elenco a discesa selezionare **Non virtualizzato/Altro** e quindi fare clic su **OK**.
  
  ![][3]
  
3. **Configurare l'ambiente di origine (server di configurazione)**. Scaricare l'**installazione unificata di Azure Site Recovery** e la **chiave di registrazione dell'insieme di credenziali** andando al pannello **Preparare l'infrastruttura** > **Prepara origine** > **Aggiungi server**. Sarà necessaria la chiave di registrazione dell'insieme di credenziali per eseguire l'installazione unificata. La chiave è valida per cinque giorni dal momento in cui viene generata.
  
  ![][4]
  
  ![][5]
  
  Nella VM che si usa come server di configurazione, eseguire l'Installazione unificata per installare il server di configurazione e il server di elaborazione. È possibile seguire gli screenshot [qui](../site-recovery/site-recovery-vmware-to-azure.md#set-up-the-source-environment) per completare l'installazione. È possibile vedere gli screenshot seguenti per i passaggi specificati per questo scenario di migrazione.

  In **Prima di iniziare** selezionare l'opzione **Installare il server di configurazione e il server di elaborazione**.
  
  ![][6]

  In **Registrazione** cercare e selezionare la chiave di registrazione scaricata dall'insieme di credenziali.
  
  ![][7]

  In **Dettagli ambiente** specificare se si vuole eseguire la replica di VM VMware. Per questo scenario di migrazione, scegliere **No**.
  
  ![][8]
  
  Al termine dell'installazione, verrà visualizzata la finestra **Microsoft Azure Site Recovery Configuration Server** (Server di configurazione Microsoft Azure Site Recovery). Usare la scheda **Gestisci account** per creare l'account che Site Recovery può usare per l'individuazione automatica. Nello scenario sulla protezione dei computer fisici la configurazione dell'account non è pertinente, ma è necessario almeno un account per abilitare uno dei passaggi seguenti. In questo caso, è possibile usare qualsiasi account e password. Usare la scheda **Vault Registration** (Registrazione dell'insieme di credenziali) per caricare il file delle credenziali dell'insieme di credenziali.
  
  ![][9]

4. **Configurare l'ambiente di destinazione**. Fare clic su **Preparare l'infrastruttura** > **Destinazione** e specificare il modello di distribuzione da usare per le macchine virtuali dopo il failover. È possibile scegliere **Classica** o **Resource Manager**, a seconda dello scenario.
  
  ![][10]

  Site Recovery verifica la disponibilità di uno o più account di archiviazione di Azure e reti compatibili. Si noti che, se si usa un account di archiviazione Premium per i dati replicati, è necessario configurare un account di archiviazione Standard aggiuntivo per l'archiviazione dei log di replica.

5. **Configurare le impostazioni di replica**. Seguire i passaggi [qui](../site-recovery/site-recovery-vmware-to-azure.md#set-up-replication-settings) per verificare che il server di configurazione venga associato correttamente ai criteri di replica creati.

6. **Pianificazione della capacità**. Usare [Capacity Planner](../site-recovery/site-recovery-capacity-planner.md) per stimare con precisione la larghezza di banda di rete, lo spazio di archiviazione e gli altri requisiti per poter soddisfare le esigenze di replica. Al termine scegliere **Sì** in **È stata completata la pianificazione della capacità?**
  
  ![][11]

7. **Installare il servizio Mobility e abilitare la replica**. È possibile scegliere di [effettuare il push dell'installazione](../site-recovery/site-recovery-vmware-to-azure.md#prepare-for-automatic-discovery-and-push-installation) nelle VM di origine o di [installare manualmente il servizio Mobility](../site-recovery/site-recovery-vmware-to-azure-install-mob-svc.md) nelle VM di origine. È possibile trovare il requisito del push dell'installazione e il percorso del programma di installazione manuale nel collegamento fornito. Se si esegue un'installazione manuale, potrebbe essere necessario usare un indirizzo IP interno per trovare il server di configurazione. 
  
  ![][12]
  
  La VM sottoposta a failover avrà due dischi temporanei: uno dalla VM primaria e l'altro creato durante il provisioning della VM nell'area di ripristino. Per escludere il disco temporaneo prima della replica, installare il servizio Mobility prima di abilitare la replica. Per altre informazioni su come escludere il disco temporaneo, vedere [Escludere dischi dalla replica](../site-recovery/site-recovery-vmware-to-azure.md#exclude-disks-from-replication).
**Per abilitare la replica, procedere come descritto di seguito**:
  * Fare clic su **Eseguire la replica dell'applicazione** > **Origine**. Dopo avere abilitato la replica per la prima volta, fare clic su +Replica nell'insieme di credenziali per abilitare la replica per altri computer.
  * Nel passaggio 1 configurare l'origine come server di elaborazione.
  * Nel passaggio 2 specificare il modello di distribuzione successivo al failover, un account di archiviazione Premium a cui eseguire la migrazione, un account di archiviazione Standard per salvare i log e una rete virtuale in cui effettuare il failover. 
  * Nel passaggio 3 aggiungere le VM protette per indirizzo IP. Per trovarle, potrebbe essere necessario un indirizzo IP interno. 
  * Nel passaggio 4 configurare le proprietà selezionando gli account configurati prima nel server di elaborazione. 
  * Nel passaggio 5 scegliere i criteri di replica creati prima e configurare le impostazioni di replica. 
  Fare clic su **OK** e abilitare la replica.
  
  > [!NOTE]
  > Quando una VM di Azure viene deallocata e riavviata, non è certo che ottenga lo stesso indirizzo IP. Se l'indirizzo IP del server di configurazione/server di elaborazione o le VM di Azure protette cambiano, la replica in questo scenario potrebbe non funzionare correttamente.
  
  ![][13]
  
  Quando si progetta l'ambiente di Archiviazione di Azure, è consigliabile usare account di archiviazione distinti per ogni VM in un set di disponibilità. È preferibile seguire le procedure consigliate nel livello di archiviazione per le VM [Windows](../virtual-machines/virtual-machines-windows-manage-availability.md#use-multiple-storage-accounts-for-each-availability-set) e [Linux](../virtual-machines/virtual-machines-linux-manage-availability.md#use-multiple-storage-accounts-for-each-availability-set). Distribuire i dischi delle VM in più account di archiviazione consente di migliorare la disponibilità di archiviazione e di suddividere le operazioni di I/O nell'infrastruttura di archiviazione di Azure. Se le VM sono in un set di disponibilità, invece di replicare i dischi di tutte le VM in un account di archiviazione, è consigliabile eseguire più volte la migrazione di più VM, in modo che le VM nello stesso set di disponibilità non condividano un solo account di archiviazione. Usare il pannello **Abilitazione della replica** per configurare contemporaneamente un account di archiviazione di destinazione per ogni VM. È possibile scegliere un modello di distribuzione post-failover adatto alle proprie esigenze. Se si sceglie Resource Manager (RM) come modello di distribuzione post-failover, è possibile effettuare il failover di una VM RM in una VM RM o di una VM classica in una VM RM.

8. **Eseguire un failover di test**. Per controllare se la replica è completa, fare clic Site Recovery e quindi su **Impostazioni** > **Elementi replicati**. Verranno visualizzati lo stato e la percentuale del processo di replica. Al termine della replica iniziale, eseguire il failover di test per convalidare la strategia di replica. Per i passaggi dettagliati del failover di test, vedere [Eseguire un failover di test in Site Recovery](../site-recovery/site-recovery-vmware-to-azure.md#run-a-test-failover). È possibile visualizzare lo stato del failover di test in **Impostazioni** > **Processi** > **NOME_PIANO_FAILOVER**. Nel pannello verranno visualizzati una suddivisione dei passaggi e i risultati positivi/negativi. Se il failover di test ha esito negativo in qualche passaggio, fare clic sul passaggio per controllare il messaggio di errore. Verificare che le macchine virtuali e la strategia di replica soddisfino i requisiti prima di eseguire un failover non pianificato. Per altre informazioni e istruzioni sul failover di test, leggere [Failover di test in Azure Site Recovery](../site-recovery/site-recovery-test-failover-to-azure.md).

9. **Eseguire un failover**. Al termine del failover di test, eseguire un failover per eseguire la migrazione dei dischi all'Archiviazione Premium e replicare le istanze delle macchine virtuali. Seguire la procedura dettagliata in [Eseguire un failover](../site-recovery/site-recovery-failover.md#run-a-failover). Assicurarsi di selezionare **Arresta le macchine virtuali e sincronizza i dati più recenti** per specificare che Site Recovery deve provare ad arrestare le VM protette e sincronizzare i dati in modo che venga eseguito il failover dei dati più recenti. Se non si seleziona questa opzione o il tentativo ha esito negativo, il failover avverrà dall'ultimo punto di ripristino disponibile per la VM. Site Recovery creerà un'istanza di una VM di tipo uguale o simile a quello di una VM che supporta Archiviazione Premium. Per controllare le prestazioni e il prezzo delle diverse istanze delle VM, vedere [Prezzi di Macchine virtuali Windows](https://azure.microsoft.com/en-us/pricing/details/virtual-machines/windows/) o [Prezzi di Macchine virtuali Linux](https://azure.microsoft.com/en-us/pricing/details/virtual-machines/linux/).

## <a name="post-migration-steps"></a>Passaggi post-migrazione

1. **Configurare le VM replicate per il set di disponibilità, se applicabile**. Site Recovery non supporta la migrazione di VM con il set di disponibilità. A seconda della distribuzione della VM replicata, eseguire una di queste operazioni:
  * Per una VM creata usando il modello di distribuzione classica: aggiungere la VM al set di disponibilità nel portale di Azure. Per i passaggi dettagliati, vedere [Aggiungere una macchina virtuale esistente a un set di disponibilità](../virtual-machines/virtual-machines-windows-classic-configure-availability.md#a-idaddmachine-aoption-2-add-an-existing-virtual-machine-to-an-availability-set).
  * Per il modello di distribuzione Resource Manager: salvare la configurazione della VM e quindi eliminare e ricreare le VM nel set di disponibilità. A questo scopo, usare lo script in [Set Azure Resource Manager VM Availability Set](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4) (Impostare il set di disponibilità di VM di Azure Resource Manager). Controllare la limitazione di questo script e pianificare il tempo di inattività prima di eseguire lo script.

2. **Eliminare le VM e i dischi precedenti**. Prima di eliminarli, verificare che i dischi Premium siano coerenti con i dischi di origine e che le nuove VM eseguano la stessa funzione delle VM di origine. Nel modello di distribuzione Resource Manager (RM) eliminare la VM ed eliminare i dischi dagli account di archiviazione di origine nel portale di Azure. Nel modello di distribuzione classica è possibile eliminare la VM e i dischi nel portale classico nel portale di Azure. Se si verifica un problema per cui il disco non viene eliminato anche se è stata eliminata la VM, vedere [Risolvere gli errori quando si eliminano dischi rigidi virtuali di Azure in una distribuzione Resource Manager](storage-resource-manager-cannot-delete-storage-account-container-vhd.md) o [Risolvere i problemi di eliminazione dei dischi rigidi virtuali in una distribuzione classica](storage-cannot-delete-storage-account-container-vhd.md).

3. **Pulire l'infrastruttura di Azure Site Recovery**. Se Site Recovery non è più necessario, è possibile pulirne l'infrastruttura eliminando gli elementi replicati, il server di configurazione e i criteri di recupero e quindi eliminando l'insieme di credenziali di Azure Site Recovery.

## <a name="troubleshooting"></a>Risoluzione dei problemi

* [Monitorare e risolvere i problemi di protezione per le macchine virtuali e i server fisici](../site-recovery/site-recovery-monitoring-and-troubleshooting.md)
* [Forum di Microsoft Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hypervrecovmgr)

## <a name="next-steps"></a>Passaggi successivi

Controllare le risorse seguenti per scenari specifici per la migrazione di macchine virtuali:

* [Eseguire la migrazione di macchine virtuali di Azure tra account di archiviazione](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Creare e caricare un disco rigido virtuale Windows Server in Azure.](../virtual-machines/virtual-machines-windows-classic-createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Creazione e caricamento di un disco rigido virtuale contenente il sistema operativo Linux](../virtual-machines/virtual-machines-linux-classic-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
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

