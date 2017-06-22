---
title: Replicare server fisici in Azure | Documentazione Microsoft
description: Descrive come distribuire Azure Site Recovery per orchestrare la replica, il failover e il ripristino di server fisici Windows/Linux locali in Azure tramite il portale di Azure
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: ab2a4b2771a4d45c947e35c8ee032e5ddf20a8aa
ms.contentlocale: it-it
ms.lasthandoff: 05/17/2017


---
# <a name="replicate-physical-machines-to-azure-by-using-site-recovery"></a>Replicare computer fisici in Azure con Site Recovery


Questo articolo descrive come replicare computer fisici locali in Azure usando il servizio Azure Site Recovery nel portale di Azure.

Se si vuole eseguire la migrazione di computer fisici in Azure (solo failover), leggere l'articolo [Eseguire la migrazione ad Azure con Site Recovery](site-recovery-migrate-to-azure.md).

Inserire commenti e domande nella parte inferiore di questo articolo oppure nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="prerequisites"></a>Prerequisiti

**Requisiti di supporto** | **Dettagli**
--- | ---
**Azure** | Vedere i [requisiti di Azure](site-recovery-prereq.md#azure-requirements).
**Server di configurazione locale** | Computer locale (computer fisico o VM VMware) che esegue Windows Server 2012 R2 o versione successiva. Questo server viene configurato durante la distribuzione di Site Recovery.<br/><br/> Per impostazione predefinita, in questo computer vengono installati anche il server di elaborazione e il server di destinazione master. Quando si esegue un aumento delle prestazioni, potrebbe essere necessario un server di elaborazione separato, che ha gli stessi requisiti del server di configurazione.<br/><br/> Altre informazioni su questi componenti sono reperibili in [Configurare l'ambiente di origine](site-recovery-set-up-vmware-to-azure.md#configuration-server-minimum-requirements).
**VM locali** | I computer da replicare devono eseguire un [sistema operativo supportato](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) ed essere conformi ai [prerequisiti di Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
**URL** | Il server di configurazione deve avere accesso a questi URL:<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> Se sono presenti regole del firewall basate sull'indirizzo IP, verificare che consentano la comunicazione con Azure.<br/></br> Consentire gli [intervalli IP del data center di Azure ](https://www.microsoft.com/download/confirmation.aspx?id=41653) e la porta (443) HTTPS.<br/></br> Consentire gli intervalli di indirizzi IP per l'area di Azure della sottoscrizione e per gli Stati Uniti occidentali (usati per il controllo di accesso e la gestione delle identità).<br/><br/> Consentire questo URL per il download di MySQL: http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi.
**Servizio Mobility** | Il servizio deve essere installato in ogni computer da replicare.

## <a name="limitations"></a>Limitazioni

**Limitazione** | **Dettagli**
--- | ---
**Azzurro** | Gli account di archiviazione e di rete devono trovarsi nella stessa area dell'insieme di credenziali.<br/><br/> Se si usa un account di archiviazione Premium, è necessario anche un account di archiviazione Standard per archiviare i log di replica.<br/><br/> In India centrale e India meridionale non è possibile eseguire la replica in account Premium.
**Server di configurazione locale** | Se si installa il server di configurazione in una VM VMware, il tipo di scheda della VM deve essere VMXNET3. In caso contrario, [installare questo aggiornamento](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1).<br/><br/> Se si usa una VM VMware, è necessario installarvi vSphere PowerCLI 6.0.<br/><br> Il computer non deve essere un controller di dominio.<br/><br/> Il computer deve avere un indirizzo IP statico.<br/><br/> Il nome host può contenere al massimo 15 caratteri e il sistema operativo deve essere in inglese.
**Computer replicati** | Verificare le [limitazioni delle VM di Azure](site-recovery-prereq.md#azure-requirements).<br/><br/> Se si vuole abilitare la coerenza tra più VM, che permette di ripristinare insieme le VM che eseguono lo stesso carico di lavoro in un punto dati coerente, aprire la porta 20004 sulla VM.<br/><br/> Sono supportati tipi specifici di [archiviazione Linux](site-recovery-support-matrix-to-azure.md#support-for-storage).
**Failback** | Non è possibile eseguire il failback da Azure a un computer fisico. Se si vuole eseguire il failback in locale dopo il failover, è necessario un ambiente VMware, in modo da poter eseguire il failback in una VM VMware.


## <a name="set-up-azure"></a>Configurare Azure

1. [Configurare una rete di Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

      a. Le VM di Azure create dopo il failover verranno inserite in questa rete.

      b. È possibile configurare una rete in modalità classica o in [Resource Manager](../resource-manager-deployment-model.md).

2. Configurare un [account di archiviazione di Azure](../storage/storage-create-storage-account.md#create-a-storage-account) per i dati replicati.

    a. L'account può essere Standard o [Premium](../storage/storage-premium-storage.md).

    b. È possibile configurare un account in modalità classica o in Resource Manager.

## <a name="prepare-the-configuration-server"></a>Preparare il server di configurazione

1. Installare Windows Server 2012 R2 o versione successiva in un server fisico locale o una VM VMware.

2. Verificare che il computer abbia accesso agli URL elencati nella sezione [Prerequisiti](#prerequisites).

## <a name="prepare-for-mobility-service-installation"></a>Preparare l'installazione del servizio Mobility

Se si vuole effettuare il push del servizio Mobility nel computer fisico, è necessario un account che possa essere usato dal server di elaborazione per accedere ai computer. L'account viene usato solo per l'installazione push. È possibile usare un account di dominio o locale:

  - Per Windows, se non si usa un account di dominio è necessario disabilitare il Controllo dell'accesso utente remoto nel computer locale. A tale scopo, nel Registro di sistema, in **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, aggiungere la voce DWORD **LocalAccountTokenFilterPolicy** con un valore di 1. Per aggiungere la voce del Registro di sistema per Windows da un'interfaccia della riga di comando, digitare:

        ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
  - Per Linux, l'account deve essere un utente ROOT nel server Linux di origine.


## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]


## <a name="select-the-protection-goal"></a>Selezionare l'obiettivo di protezione

Selezionare gli elementi da replicare e la posizione in cui eseguire la replica.

1. Fare clic su **Insiemi di credenziali dei servizi di ripristino** > **insiemi di credenziali**.
2. Nel menu delle **risorse** fare clic su **Site Recovery** > **Preparare l'infrastruttura** > **Obiettivo di protezione**.

    ![Scegliere gli obiettivi](./media/site-recovery-vmware-to-azure/choose-goal-physical.PNG)

3. In **Obiettivo di protezione** selezionare **In Azure** >  e quindi **Non virtualizzato/Altro**.


## <a name="set-up-the-source-environment"></a>Configurare l'ambiente di origine

Configurare il server di configurazione, registrarlo nell'insieme di credenziali e individuare le VM.

1. Fare clic su **Site Recovery** > **Preparare l'infrastruttura** > **Origine**.
2. Se non è disponibile un server di configurazione, fare clic su **+Server di configurazione**.

    ![Impostare l'origine](./media/site-recovery-vmware-to-azure/set-source1.png)

3. In **Aggiungi server** verificare che **Tipo di server** contenga **Server di configurazione**.
4. Scaricare il file di installazione per l'**Installazione unificata di Azure Site Recovery**.
5. Scaricare la **chiave di registrazione dell'insieme di credenziali**, che sarà necessaria quando si esegue l'Installazione unificata. La chiave è valida per cinque giorni dal momento in cui viene generata.

   ![Impostare l'origine](./media/site-recovery-vmware-to-azure/set-source2.png)


## <a name="run-site-recovery-unified-setup"></a>Eseguire l'Installazione unificata di Site Recovery

Prima di iniziare, eseguire le operazioni seguenti:

- Ottenere una breve panoramica video. Il video descrive come replicare le VM VMware, ma il processo è simile a quello per la replica di computer fisici.

    > [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video1-Source-Infrastructure-Setup/player]

- Nel server di configurazione verificare che il clock di sistema sia sincronizzato con un [server di riferimento ora](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service). Se è avanti o indietro di 15 minuti, l'installazione potrebbe avere esito negativo.
- Eseguire l'installazione come amministratore locale nel server di configurazione.
- Verificare che nel computer sia abilitato TLS 1.0.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> Il server di configurazione può essere installato anche [dalla riga di comando](http://aka.ms/installconfigsrv).


## <a name="set-up-the-target-environment"></a>Configurare l'ambiente di destinazione

Prima di configurare l'ambiente di destinazione, verificare di avere [una rete e un account di archiviazione di Azure](#set-up-azure).

1. Fare clic su **Preparare l'infrastruttura** > **Destinazione** e selezionare la sottoscrizione di Azure da usare.
2. Specificare se per la destinazione deve essere usato il modello di distribuzione classica o di Resource Manager.
3. Site Recovery verifica la disponibilità di uno o più account di archiviazione di Azure e reti compatibili.

   ![Destinazione](./media/site-recovery-vmware-to-azure/gs-target.png)

4. Se non si è creato un account di archiviazione o una rete, fare clic su **+Account di archiviazione** o **+Rete** per creare una rete o un account di Resource Manager inline.

## <a name="set-up-replication-settings"></a>Configurare le impostazioni di replica

Prima di iniziare, visualizzare questa breve panoramica video. Il video descrive come replicare le VM VMware, ma il processo è simile a quello per la replica di computer fisici.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video2-vCenter-Server-Discovery-and-Replication-Policy/player]

1. Per creare nuovi criteri di replica, fare clic su **Infrastruttura di Site Recovery** > **Criteri di replica** > **+Criteri di replica**.
2. In **Creare i criteri di replica** specificare un nome per i criteri.
3. In **Soglia RPO**specificare il limite per RPO. Questo valore specifica la frequenza con cui vengono creati punti di ripristino dei dati. Se la replica continua supera questo limite, viene generato un avviso.
4. In **Conservazione del punto di ripristino** specificare la durata in ore dell'intervallo di conservazione per ogni punto di ripristino. Le VM replicate possono essere ripristinate in qualsiasi punto all'interno di un intervallo. Per le macchine replicate nell'archiviazione Premium è supportato un intervallo di conservazione fino a 24 ore. Per le macchine replicate nell'archiviazione standard è supportato un intervallo di conservazione fino a 72 ore.
5. In **Frequenza snapshot coerenti con l'app**specificare la frequenza, in minuti, per la creazione di punti di ripristino contenenti snapshot coerenti con l'applicazione. Fare clic su **OK** per creare i criteri.

    ![Criteri di replica](./media/site-recovery-vmware-to-azure/gs-replication2.png)

6. Quando si creano nuovi criteri, questi vengono associati automaticamente al server di configurazione. Per impostazione predefinita vengono creati automaticamente criteri corrispondenti per il failback. Se, ad esempio, il criterio di replica è **rep-policy**, il criterio di failback sarà **rep-policy-failback**. Questi criteri non vengono usati fino a quando non si avvia un failback da Azure.  


## <a name="plan-capacity"></a>Pianificare la capacità

1. Dopo aver configurato l'infrastruttura di base è possibile passare alla pianificazione della capacità e valutare se sono necessarie altre risorse. [Altre informazioni](site-recovery-plan-capacity-vmware.md).
2. Dopo aver pianificato la capacità, scegliere **Sì** in **È stata completata la pianificazione della capacità?**

   ![Pianificazione della capacità](./media/site-recovery-vmware-to-azure/gs-capacity-planning.png)


## <a name="prepare-vms-for-replication"></a>Preparare le VM per la replica

In tutti i computer da replicare deve essere installo il servizio Mobility. È possibile installare il servizio Mobility in diversi modi:

- Installazione del servizio in modalità push dal server di elaborazione. Per usare questo metodo, è necessario preparare i computer.
- Installazione con strumenti di distribuzione come System Center Configuration Manager o 	Configurazione dello stato desiderato di automazione di Azure.
- Installare il servizio manualmente.

[Altre informazioni](site-recovery-vmware-to-azure-install-mob-svc.md).


## <a name="enable-replication"></a>Abilitare la replica

Prima di iniziare:

- L'account utente di Azure deve disporre di determinate [autorizzazioni](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) per abilitare la replica di una nuova macchina virtuale in Azure.
- Quando si aggiungono o si modificano VM, possono trascorrere 15 minuti o più prima che le modifiche diventino effettive e vengano visualizzate nel portale.
- È possibile controllare l'ora dell'ultima individuazione di VM in **Server di configurazione** > **Ora ultimo contatto**.
- Per aggiungere VM senza attendere l'individuazione pianificata, evidenziare il server di configurazione, senza selezionarlo, e fare clic su **Aggiorna**.
- Se una VM è pronta per l'installazione push, il server di elaborazione installa automaticamente il servizio Mobility quando si abilita la replica.
- Ottenere una breve panoramica video. Il video descrive come replicare le VM VMware, ma il processo è simile a quello per la replica di computer fisici.

    >[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video3-Protect-VMware-Virtual-Machines/player]


### <a name="exclude-disks-from-replication"></a>Escludere dischi dalla replica

Per impostazione predefinita, vengono replicati tutti i dischi presenti in un computer. È possibile escludere dischi dalla replica. Ad esempio, è possibile evitare di replicare i dischi con dati temporanei o dati che vengono aggiornati ogni volta che un computer o un'applicazione viene riavviata, come pagefile.sys o tempdb di SQL Server.

### <a name="replicate-vms"></a>Replicare le VM

1. Fare clic su **Eseguire la replica dell'applicazione** > **Origine**.
2. In **Origine** selezionare **Locale**.
3. In **Percorso di origine** selezionare il nome del server di configurazione.
4. In **Tipo di computer** selezionare **Computer fisici**.
5. In **Server di elaborazione** scegliere il server di elaborazione. Se non sono stati creati server di elaborazione aggiuntivi, questo sarà il server di configurazione. Fare quindi clic su **OK**.

    ![Abilitare la replica](./media/site-recovery-physical-to-azure/chooseVM.png)

6. In **Destinazione** selezionare la **sottoscrizione** e il **gruppo di risorse** in cui si vuole creare le VM di Azure dopo il failover. Scegliere il modello di distribuzione (classica o di Resource Manager) da usare in Azure per le VM di cui viene effettuato il failover.

7. Selezionare l'account di archiviazione di Azure da usare per la replica dei dati. Se non si vuole usare un account già configurato, è possibile crearne uno nuovo.

8. Selezionare la **rete** e la **subnet** di Azure a cui dovranno connettersi le VM di Azure dopo il failover. Scegliere **Configurare ora per le macchine virtuali selezionate** per applicare le impostazioni di rete a tutti i computer selezionati per la protezione. Scegliere **Configurare in seguito** per selezionare la rete di Azure per ogni computer. Se non si vuole usare una rete esistente, è possibile crearne una.

    ![Abilitare la replica](./media/site-recovery-physical-to-azure/targetsettings.png)

9. In **Computer fisici**, fare clic su **+Computer fisici** e inserire il **nome** e l'**indirizzo IP**. Scegliere il sistema operativo del computer da replicare. È necessario attendere alcuni minuti prima che i computer vengano individuati e visualizzati nell'elenco.

    ![Abilitare la replica](./media/site-recovery-physical-to-azure/machineselect.png)

10. In **Proprietà** > **Configura proprietà** selezionare l'account che verrà usato dal server di elaborazione per installare automaticamente il servizio Mobility nel computer.
11. Per impostazione predefinita, vengono replicati tutti i dischi. Fare clic su **Tutti i dischi** e deselezionare i dischi da non replicare. Fare quindi clic su **OK**. È possibile impostare proprietà aggiuntive delle VM in un secondo momento.

    ![Abilitare la replica](./media/site-recovery-physical-to-azure/configprop.png)

12. In **Impostazioni della replica** > **Configura impostazioni di replica** verificare che siano selezionati i criteri di replica corretti. Se si modificano i criteri, le modifiche verranno applicate al computer di replica e ai nuovi computer.
13. Abilitare la **Coerenza tra più VM** per raccogliere le macchine in un gruppo di replica e specificare un nome per il gruppo. Fare quindi clic su **OK**. Si noti che:

    a. I computer nei gruppi di replica vengono replicati insieme e hanno punti di ripristino condivisi coerenti con l'arresto anomalo del sistema e coerenti con l'app in caso di failover.

    b. È consigliabile raggruppare le macchine virtuali e i server fisici in modo da rispecchiare i carichi di lavoro. L'abilitazione della coerenza su più macchine virtuali può avere un impatto sulle prestazioni del carico di lavoro. Deve essere usata solo se i computer eseguono lo stesso carico di lavoro ed è necessaria la coerenza.

    ![Abilitare la replica](./media/site-recovery-physical-to-azure/policy.png)

14. Fare clic su **Abilita la replica**. È possibile tenere traccia dello stato del processo **Abilita protezione** in **Impostazioni** > **Processi** > **Processi di Site Recovery**. Dopo l'esecuzione del processo **Finalizza protezione** la macchina virtuale è pronta per il failover.

Se si configura l'installazione push, dopo l'abilitazione della replica verrà installato il servizio Mobility. Dopo l'installazione push del servizio Mobility in un computer, verrà avviato un processo di protezione che non riuscirà. Dopo l'errore, è necessario riavviare manualmente tutti i computer. Il processo di protezione verrà quindi avviato nuovamente e verrà eseguita la replica iniziale.


### <a name="view-and-manage-azure-vm-properties"></a>Visualizzare e gestire le proprietà delle VM di Azure

È consigliabile verificare le proprietà delle VM e apportare tutte le modifiche eventualmente necessarie.

1. Fare clic su **Elementi replicati** e selezionare il computer. Il pannello **Informazioni di base** visualizza informazioni sulle impostazioni e sullo stato dei computer.
2. In **Proprietà** sono disponibili le informazioni su replica e failover per la VM.
3. In **Calcolo e rete** > **Proprietà di calcolo** è possibile specificare le dimensioni di destinazione e il nome della VM di Azure. Se necessario, modificare il nome in modo che sia conforme ai [requisiti di Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) .
4. Modificare le impostazioni per la rete, la subnet e l'indirizzo IP di destinazione che verranno assegnati alla VM di Azure:

    a. È possibile impostare l'indirizzo IP di destinazione.

    b.  Se non si specifica un indirizzo, il computer di cui è stato eseguito il failover usa DHCP.

    c. Se si imposta un indirizzo che non è disponibile al momento del failover, il failover non riesce.

    d. Se l'indirizzo è disponibile nella rete di failover di test, è possibile usare lo stesso indirizzo IP di destinazione per il failover di test.

    e. Il numero di schede di rete dipende dalle dimensioni specificate per la macchina virtuale di destinazione:

     - Se il numero di schede di rete nel computer di origine è minore o uguale al numero di schede consentito per le dimensioni del computer di destinazione, la destinazione avrà lo stesso numero di schede dell'origine.
     - Se il numero di schede per la macchina virtuale di origine supera il numero consentito per le dimensioni di destinazione, verrà usata la dimensione di destinazione massima.
     - Ad esempio, se una macchina di origine ha due schede di rete e le dimensioni della macchina di destinazione ne supportano quattro, la macchina di destinazione avrà due schede. Se la macchina di origine ha due schede, ma le dimensioni di destinazione supportate ne consentono solo una, la macchina di destinazione avrà una sola scheda.     
   - Se la macchina virtuale ha più schede di rete, si connetteranno tutte alla stessa rete.
   - Se la macchina virtuale ha più schede di rete, la prima nell'elenco diventa la scheda di rete *predefinita* nella macchina virtuale di Azure.
5. In **Dischi** vengono visualizzati il sistema operativo della VM e i dischi dati che vengono replicati.

## <a name="run-a-test-failover"></a>Eseguire un failover di test

Dopo aver completato la configurazione, eseguire un failover di test per verificare che tutti gli elementi funzionino come previsto. Guardare una rapida panoramica video prima di iniziare.

>[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video4-Recovery-Plan-DR-Drill-and-Failover/player]


1. Per eseguire il failover di una singola macchina, in **Impostazioni** > **Elementi replicati** fare clic su **Failover di test**.

    ![Failover di test](./media/site-recovery-vmware-to-azure/TestFailover.png)

2. Per eseguire il failover di un piano di ripristino, in **Impostazioni** > **Piani di ripristino** fare clic con il pulsante destro del mouse sul piano e quindi scegliere **Failover di test**. Per creare un piano di ripristino, [seguire queste istruzioni](site-recovery-create-recovery-plans.md).  
3. In **Failover di test** selezionare la rete di Azure a cui si connetteranno le VM di Azure dopo il failover.
4. Fare clic su **OK** per iniziare il failover. Per tenere traccia dello stato del processo, fare clic sulla VM per visualizzarne le proprietà oppure fare clic sul processo **Failover di test** nel nome dell'insieme di credenziali > **Impostazioni** > **Processi** > **Processi di Site Recovery**.
5. Al termine del failover sarà anche possibile visualizzare la macchina virtuale di Azure di replica in **Macchine virtuali** nel portale di Azure. Verificare che la macchina virtuale sia delle dimensioni appropriate, che sia connessa alla rete giusta e che sia in esecuzione.
6. Se sono state [preparate le connessioni dopo il failover](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover), sarà possibile connettersi alla VM di Azure.
7. Al termine, fare clic su  **	Pulisci failover di test** nel piano di ripristino. Fare clic su **Note** per registrare e salvare eventuali osservazioni associate al failover di test. Questo passaggio elimina le macchine virtuali create durante il failover di test.

Per altre informazioni, vedere il documento [Failover di test in Azure](site-recovery-test-failover-to-azure.md).

## <a name="next-steps"></a>Passaggi successivi

Quando la replica è operativa, se si verifica un'interruzione viene eseguito il failover in Azure e vengono create VM di Azure dai dati replicati. È quindi possibile accedere a carichi di lavoro e app in Azure finché non viene effettuato il failback nella posizione primaria di nuovo operativa.

- Altre informazioni sui diversi tipi di failover e su come eseguirli sono disponibili [qui](site-recovery-failover.md).
- [Altre informazioni](site-recovery-migrate-to-azure.md#migrate-on-premises-vms-and-physical-servers) per eseguire la migrazione di computer invece della replica e del failback.
- Durante la replica dei computer fisici, è possibile solo eseguire il failback in un ambiente VMware. [Informazioni sul failback](site-recovery-failback-azure-to-vmware.md).

## <a name="third-party-software-notices-and-information"></a>Informazioni e comunicazioni sul software di terze parti
Do Not Translate or Localize

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, “Third-Party Code”). Microsoft is not the original author of the Third-Party Code. The original copyright notice and license, under which Microsoft received such Third-Party Code, are set forth below.

The information in Section A is regarding Third-Party Code components from the projects listed below. Such licenses and information are provided for informational purposes only. This Third-Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.  

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms.

The complete file can be found on the [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel, or otherwise.

