---
title: Replicare VM VMware in Azure | Microsoft Docs
description: Riepilogo dei passaggi necessari per replicare in Archiviazione di Azure i carichi di lavoro in esecuzione in VM VMware
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: dab98aa5-9c41-4475-b7dc-2e07ab1cfd18
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 9eb2d7f4b431c01983620cb0cfcffd63a9f4d4e2
ms.openlocfilehash: f7251dffc3dd922a6abeba0faca90843de64430f
ms.lasthandoff: 02/22/2017


---
# <a name="replicate-vmware-virtual-machines-to-azure-with-azure-site-recovery"></a>Replicare macchine virtuali VMware in Azure con Azure Site Recovery

> [!div class="op_single_selector"]
> * [Portale di Azure](site-recovery-vmware-to-azure.md)
> * [Azure classico](site-recovery-vmware-to-azure-classic.md)


Questo articolo illustra come replicare macchine virtuali VMware locali in Azure usando il servizio [Azure Site Recovery](site-recovery-overview.md) nel portale di Azure.

 Se si intende eseguire la migrazione di macchine virtuali VMware in Azure, leggere [questo articolo](site-recovery-migrate-to-azure.md) per ottenere altre informazioni prima di continuare.

Inserire commenti e domande nella parte inferiore di questo articolo oppure nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="steps"></a>Passi

È necessario eseguire queste operazioni:

1. Verificare i prerequisiti e le limitazioni.
2. Configurare la rete e gli account di archiviazione di Azure.
3. Preparare il computer locale che si vuole distribuire come server di configurazione.
4. Preparare gli account VMware da usare per l'individuazione automatica delle VM e, facoltativamente, per l'installazione push del servizio Mobility.
4. Creare un insieme di credenziali dei servizi di ripristino. L'insieme di credenziali contiene le impostazioni di configurazione e orchestra la replica.
5. Specificare origine, destinazione e impostazioni di replica.
6. Distribuire il servizio Mobility nelle VM di cui si vuole eseguire la replica.
7. Abilitare la replica per le VM.
7. Eseguire un failover di test per verificare che tutti gli elementi funzionino come previsto.

## <a name="prerequisites"></a>Prerequisiti

**Requisiti di supporto** | **Dettagli**
--- | ---
**Azzurro** | Vedere i [requisiti di Azure](site-recovery-prereq.md#azure-requirements).
**Server di configurazione locale** | È necessaria una VM VMware che esegue Windows Server 2012 R2 o versione successiva. Questo server viene configurato durante la distribuzione di Site Recovery.<br/><br/> Per impostazione predefinita, in questa VM vengono installati anche il server di elaborazione e il server di destinazione master. Quando si esegue un aumento delle prestazioni, potrebbe essere necessario un server di elaborazione separato, che ha gli stessi requisiti del server di configurazione.<br/><br/> Altre informazioni su questi componenti sono disponibili [qui](site-recovery-set-up-vmware-to-azure.md#configuration-server-minimum-requirements)
**Server VMware locali** | Uno o più server VMware vSphere, che eseguono la versione 6.0, 5.5 o 5.1 con gli ultimi aggiornamenti. I server devono trovarsi nella stessa rete del server di configurazione (o del server di elaborazione separato).<br/><br/> È consigliabile usare un server vCenter, che esegue la versione 6.0 o 5.5 con gli ultimi aggiornamenti, per gestire gli host. Quando si distribuisce la versione 6.0, sono supportate solo le funzionalità disponibili nella versione 5.5.
**VM locali** | Le VM da replicare devono eseguire un [sistema operativo supportato](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) ed essere conformi ai [prerequisiti di Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements). Nella VM devono essere in esecuzione gli strumenti VMware.
**URL** | Il server di configurazione deve avere accesso a questi URL:<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> Se sono presenti regole del firewall basate sull'indirizzo IP, verificare che consentano la comunicazione con Azure.<br/></br> Consentire gli [intervalli IP del data center di Azure ](https://www.microsoft.com/download/confirmation.aspx?id=41653) e la porta HTTPS (443).<br/></br> Consentire gli intervalli di indirizzi IP per l'area di Azure della sottoscrizione e per gli Stati Uniti occidentali (usati per il controllo di accesso e la gestione delle identità).<br/><br/> Consentire questo URL per il download di MySQL: http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi.
**Servizio Mobility** | Deve essere installato in ogni VM replicata.




## <a name="limitations"></a>Limitazioni

**Limitazione** | **Dettagli**
--- | ---
**Azure** | Gli account di archiviazione e di rete devono trovarsi nella stessa area dell'insieme di credenziali.<br/><br/> Se si usa un account di archiviazione Premium, è necessario anche un account di archiviazione Standard per archiviare i log di replica.<br/><br/> In India centrale e India meridionale non è possibile eseguire la replica in account Premium.
**Server di configurazione locale** | Le schede delle VM VMware devono essere di tipo VMXNET3. In caso contrario, [installare questo aggiornamento](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1).<br/><br/> Deve essere installato vSphere PowerCLI 6.0.<br/><br> Il computer non deve essere un controller di dominio. Il computer deve avere un indirizzo IP statico.<br/><br/> Il nome host può contenere al massimo 15 caratteri e il sistema operativo deve essere in inglese.
**VMware** | In vCenter 6.0 sono supportate solo le funzionalità della versione 5.5. Site Recovery non supporta le nuove funzionalità di vCenter e vSphere 6.0 come cross-vCenter vMotion, Virtual Volumes e Storage DRS.
**Macchine virtuali** | Verificare le [limitazioni delle VM di Azure](site-recovery-prereq.md#azure-requirements).<br/><br/> Non è possibile replicare VM con dischi crittografati o con avvio UEFI/EFI.<br/><br> I cluster di dischi condivisi non sono supportati. Se nella VM di origine è presente un gruppo NIC, questo viene convertito in una singola scheda di interfaccia di rete dopo il failover.<br/><br/> Se nelle VM è presente un disco iSCSI, Site Recovery lo converte in un file VHD dopo il failover. Se la destinazione iSCSI è raggiungibile dalla VM di Azure, questa vi si connette e saranno visibili sia tale destinazione che il disco rigido virtuale. In questo caso, disconnettere la destinazione iSCSI.<br/><br/> Se si vuole abilitare la coerenza tra più VM, che consente di ripristinare insieme le VM che eseguono lo stesso carico di lavoro in un punto dati coerente, aprire la porta 20004 sulla VM.<br/><br/> Windows deve essere installato nell'unità C. Il disco del sistema operativo deve essere di base e non dinamico. Il disco dati può essere dinamico.<br/><br/> I file /etc/hosts di Linux nelle VM devono contenere le voci che eseguono il mapping del nome host locale agli indirizzi IP associati a tutte le schede di rete. Il nome host, i punti di montaggio, i nomi dei dispositivi, i percorsi di sistema e i nomi file (/etc/, /usr) devono essere specificati solo in inglese.<br/><br/> Sono supportati tipi specifici di [archiviazione Linux](site-recovery-support-matrix-to-azure.md#support-for-storage).<br/><br/>Creare o impostare **disk.enableUUID=true** nelle impostazioni della VM. In questo modo viene fornito un valore UUID coerente al file VMDK, che viene così installato correttamente, e si garantisce che durante il failback vengano ritrasferite nell'ambiente locale solo le modifiche differenziali, senza replica completa.

## <a name="set-up-azure"></a>Configurare Azure

1. [Configurare una rete di Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md).
    - Le VM di Azure create dopo il failover verranno inserite in questa rete.
    - È possibile configurare una rete in modalità classica o [Resource Manager](../resource-manager-deployment-model.md).

2. Configurare un [account di archiviazione di Azure](../storage/storage-create-storage-account.md#create-a-storage-account) per i dati replicati.
    - L'account può essere Standard o [Premium](../storage/storage-premium-storage.md).
    - È possibile configurare un account in modalità classica o Resource Manager.

3. [Preparare un account](#prepare-for-automatic-discovery-and-push-installation) nel server vCenter o negli host vSphere per consentire a Site Recovery di rilevare automaticamente le VM VMware.

## <a name="prepare-the-configuration-server"></a>Preparare il server di configurazione

1. Installare Windows Server 2012 R2 o versione successiva in una VM VMware.
2. Verificare che la VM abbia accesso agli URL elencati in [Prerequisiti](#prerequisites).
3. Installare [VMware vSphere PowerCLI 6.0](https://developercenter.vmware.com/tool/vsphere_powercli/6.0).


## <a name="prepare-for-automatic-discovery-and-push-installation"></a>Preparare l'individuazione automatica e l'installazione push

- **Preparare un account per l'individuazione automatica**: il server di elaborazione di Site Recovery individua automaticamente le VM. A tale scopo, Site Recovery necessita di credenziali che consentano di accedere ai server vCenter o agli host vSphere ESXi.

    1. Per usare un account dedicato, creare un ruolo a livello di vCenter con queste [autorizzazioni](#vmware-account-permissions). Assegnare un nome all'account, ad esempio **Azure_Site_Recovery**.
    2. Creare quindi un utente nell'host vSphere o nel server vCenter e assegnare il ruolo all'utente. Questo account utente viene specificato durante la distribuzione di Site Recovery.

- **Preparare un account per il push del servizio Mobility**: se si vuole effettuare il push del servizio Mobility alle VM, è necessario un account che possa essere usato dal server di elaborazione per accedere alla VM. L'account viene usato solo per l'installazione push. È possibile usare un account di dominio o locale:

    - Per Windows, se non si usa un account di dominio è necessario disabilitare il Controllo dell'accesso utente remoto nel computer locale. A questo scopo, aggiungere la voce DWORD **LocalAccountTokenFilterPolicy** con un valore di 1 nel Registro di sistema in **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**.
    - Per aggiungere la voce del Registro di sistema per Windows da un'interfaccia della riga di comando, digitare:   ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
    - Per Linux, l'account deve essere radice nel server Linux di origine.


[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-the-protection-goal"></a>Selezionare l'obiettivo di protezione

Selezionare gli elementi da replicare e la posizione in cui eseguire la replica.

1. Fare clic su **Insiemi di credenziali dei servizi di ripristino** e quindi sull'insieme di credenziali.
2. Nel menu delle risorse fare clic su **Site Recovery** > **Passaggio 1: Preparare l'infrastruttura** > **Obiettivo di protezione**.

    ![Scegliere gli obiettivi](./media/site-recovery-vmware-to-azure/choose-goals.png)
3. In **Obiettivo di protezione** selezionare **In Azure** e scegliere **Sì, con VMware vSphere Hypervisor**.

    ![Scegliere gli obiettivi](./media/site-recovery-vmware-to-azure/choose-goals2.png)

## <a name="set-up-the-source-environment"></a>Configurare l'ambiente di origine

Configurare il server di configurazione, registrarlo nell'insieme di credenziali e individuare le VM.

1. Fare clic su **Site Recovery** > **Passaggio 1: Preparare l'infrastruttura** > **Origine**.
2. Se non è disponibile un server di configurazione, fare clic su **+Server di configurazione**.

    ![Impostare l'origine](./media/site-recovery-vmware-to-azure/set-source1.png)
3. In **Aggiungi server** verificare che **Tipo di server** contenga **Server di configurazione**.
4. Scaricare il file di installazione per l'Installazione unificata di Azure Site Recovery.
5. Scaricare la chiave di registrazione dell'insieme di credenziali, che sarà necessaria quando si esegue l'Installazione unificata. La chiave è valida per cinque giorni dal momento in cui viene generata.

   ![Impostare l'origine](./media/site-recovery-vmware-to-azure/set-source2.png)
6. Nella VM del server di configurazione, verificare che il clock di sistema sia sincronizzato con un [server di riferimento ora](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service) e quindi eseguire l'Installazione unificata per installare il server di configurazione, il server di elaborazione e il server di destinazione master.

## <a name="run-site-recovery-unified-setup"></a>Eseguire l'Installazione unificata di Site Recovery

Prima di iniziare:

- Verificare che l'ora nella VM sia uguale all'ora nel fuso orario locale. Deve corrispondere. Se è avanti o indietro di 15 minuti, l'installazione potrebbe avere esito negativo.
- Eseguire l'installazione come amministratore locale nella VM del server di configurazione.
- Verificare che nella VM sia abilitato TLS 1.0.

Eseguire quindi il file di installazione per l'Installazione unificata nel server di configurazione.


[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> Il server di configurazione può essere installato tramite la riga di comando. Per altre informazioni, vedere l'argomento relativo all'[installazione del server di configurazione con gli strumenti da riga di comando](http://aka.ms/installconfigsrv).

### <a name="add-the-account-for-automatic-discovery"></a>Aggiungere l'account per l'individuazione automatica

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

### <a name="connect-to-vmware-servers"></a>Connettersi ai server VMware

Connettersi agli host vSphere ESXi o ai server vCenter per individuare le VM VMware.

- Se si aggiungono gli host vSphere o il server vCenter con un account senza privilegi di amministratore per il server, per l'account devono essere abilitati i privilegi seguenti:
    - Datacenter (Data center), Datastore (Archivio dati), Folder (Cartella), Host, Network (Rete), Resource (Risorsa), Virtual machine (Macchina virtuale) e vSphere Distributed Switch (Switch distribuito vSphere).
    - Per il server vCenter è necessario il privilegio Storage views (Viste archiviazione).
- Quando si aggiungono server VMware, possono trascorrere 15 minuti o più prima che vengano visualizzati nel portale.
Per consentire ad Azure Site Recovery di individuare macchine virtuali in esecuzione nell'ambiente locale, è necessario connettere il server VMware vCenter o gli host vSphere ESXi a Site Recovery.

Selezionare **+vCenter** per avviare la connessione di un server VMware vCenter o di un host VMware vSphere ESXi.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

Site Recovery si connette ai server VMware usando le impostazioni specificate e individua le VM.

## <a name="set-up-the-target"></a>Configurare la destinazione

Prima di configurare l'ambiente di destinazione, verificare di avere [una rete e un account di archiviazione di Azure](#set-up-azure).

1. Fare clic su **Preparare l'infrastruttura** > **Destinazione** e selezionare la sottoscrizione di Azure da usare.
2. Specificare se per la destinazione deve essere usato il modello di distribuzione classica o Resource Manager.
3. Site Recovery verifica la disponibilità di uno o più account di archiviazione di Azure e reti compatibili.

   ![Destinazione](./media/site-recovery-vmware-to-azure/gs-target.png)
4. Se non si è creato un account di archiviazione o una rete, fare clic su **+Account di archiviazione** o **+Rete** per creare una rete o un account Resource Manager inline.

## <a name="set-up-replication-settings"></a>Configurare le impostazioni di replica

1. Per creare nuovi criteri di replica, fare clic su **Infrastruttura di Site Recovery** > **Criteri di replica** > **+Criteri di replica**.
2. In **Creare i criteri di replica** specificare un nome per i criteri.
3. In **Soglia RPO**specificare il limite per RPO. Questo valore specifica la frequenza con cui vengono creati punti di ripristino dei dati. Se la replica continua supera questo limite, viene generato un avviso.
4. In **Conservazione del punto di ripristino** specificare la durata in ore dell'intervallo di conservazione per ogni punto di ripristino. Le VM replicate possono essere ripristinate in qualsiasi punto all'interno di un intervallo. È supportata la conservazione fino a 24 ore per le macchine replicate in Archiviazione Premium e fino a 72 ore per Archiviazione Standard.
5. In **Frequenza snapshot coerenti con l'app**specificare la frequenza, in minuti, per la creazione di punti di ripristino contenenti snapshot coerenti con l'applicazione. Fare clic su **OK** per creare i criteri.

    ![Criteri di replica](./media/site-recovery-vmware-to-azure/gs-replication2.png)
8. Quando si creano nuovi criteri, questi vengono associati automaticamente al server di configurazione. Per impostazione predefinita vengono creati automaticamente criteri corrispondenti per il failback. Ad esempio se i criteri di replica sono **rep-policy** i criteri di failback saranno **rep-policy-failback**. Questi criteri non vengono usati fino a quando non si avvia un failback da Azure.  


## <a name="plan-capacity"></a>Pianificare la capacità

1. Dopo aver configurato l'infrastruttura di base è possibile passare alla pianificazione della capacità e valutare se sono necessarie altre risorse. [Altre informazioni](site-recovery-plan-capacity-vmware.md).
2. Dopo aver pianificato la capacità, scegliere **Sì** in **È stata completata la pianificazione della capacità?**

   ![Pianificazione della capacità](./media/site-recovery-vmware-to-azure/gs-capacity-planning.png)


## <a name="prepare-vms-for-replication"></a>Preparare le VM per la replica

In tutti i computer da replicare deve essere installo il servizio Mobility. È possibile installare il servizio Mobility in diversi modi:

1. Installazione push dal server di elaborazione. Per usare questo metodo è necessario preparare le VM.
2. Installazione con strumenti di distribuzione come System Center Configuration Manager o Automation DSC per Azure.
3.  Installazione manuale.

[Altre informazioni](site-recovery-vmware-to-azure-install-mob-svc.md)


## <a name="enable-replication"></a>Abilitare la replica

Prima di iniziare:

- Quando si aggiungono o si modificano VM, possono trascorrere 15 minuti o più prima che le modifiche diventino effettive e vengano visualizzate nel portale.
- È possibile controllare l'ora dell'ultima individuazione di VM in **Server di configurazione** > **Ora ultimo contatto**.
- Per aggiungere VM senza attendere l'individuazione pianificata, evidenziare il server di configurazione, senza selezionarlo, e fare clic su **Aggiorna**.
* Se una VM è pronta per l'installazione push, il server di elaborazione installa automaticamente il servizio Mobility quando si abilita la replica.


### <a name="exclude-disks-from-replication"></a>Escludere dischi dalla replica

Per impostazione predefinita, vengono replicati tutti i dischi in un computer. È possibile escludere dischi dalla replica. Ad esempio, è possibile evitare di replicare i dischi con dati temporanei o dati che vengono aggiornati ogni volta che un computer o un'applicazione viene riavviata, come pagefile.sys o tempdb di SQL Server.

### <a name="replicate-vms"></a>Replicare le VM

1. Fare clic su **Passaggio 2: Eseguire la replica dell'applicazione** > **Origine**.
2. In **Origine** selezionare il server di configurazione.
3. In **Tipo di computer** selezionare **Macchine virtuali**.
4. In **vCenter/vSphere Hypervisor** selezionare il server vCenter che gestisce l'host di vSphere oppure selezionare l'host.
5. Selezionare il server di elaborazione. Se non sono stati creati server di elaborazione aggiuntivi, questo sarà il server di configurazione. Fare quindi clic su **OK**.

    ![Abilitare la replica](./media/site-recovery-vmware-to-azure/enable-replication2.png)

6. In **Destinazione** selezionare la sottoscrizione e il gruppo di risorse in cui si vogliono creare le VM di cui viene effettuato il failover. Scegliere il modello di distribuzione (classica o Resource Manager) da usare in Azure per le VM di cui viene effettuato il failover.


7. Selezionare l'account di archiviazione di Azure da usare per la replica dei dati. Se non si vuole usare un account già configurato, è possibile crearne uno nuovo.

8. Selezionare la rete di Azure e la subnet a cui dovranno connettersi le VM di Azure create dopo il failover. Scegliere **Configurare ora per le macchine virtuali selezionate** per applicare le impostazioni di rete a tutti i computer selezionati per la protezione. Scegliere **Configurare in seguito** per selezionare la rete di Azure per ogni computer. Se non si vuole usare una rete esistente, è possibile crearne una.

    ![Abilitare la replica](./media/site-recovery-vmware-to-azure/enable-rep3.png)
9. In **Macchine virtuali** > **Seleziona macchine virtuali** fare clic per selezionare tutte le macchine virtuali da replicare. È possibile selezionare solo i computer per cui è possibile abilitare la replica. Fare quindi clic su **OK**.

    ![Abilitare la replica](./media/site-recovery-vmware-to-azure/enable-replication5.png)
10. In **Proprietà** > **Configura proprietà**selezionare l'account che verrà usato dal server di elaborazione per installare automaticamente il servizio Mobility nel computer.
11. Per impostazione predefinita, vengono replicati tutti i dischi. Fare clic su **Tutti i dischi** e deselezionare i dischi da non replicare. Fare quindi clic su **OK**. È possibile impostare proprietà aggiuntive delle VM in un secondo momento.

    ![Abilitare la replica](./media/site-recovery-vmware-to-azure/enable-replication6.png)
11. In **Impostazioni della replica** > **Configura impostazioni di replica** verificare che siano selezionati i criteri di replica corretti. Se si modificano i criteri, le modifiche verranno applicate al computer di replica e ai nuovi computer.
12. Abilitare la **Coerenza tra più VM** per raccogliere le macchine in un gruppo di replica e specificare un nome per il gruppo. Fare quindi clic su **OK**. Si noti che:

    * I computer in gruppi di replica vengono replicati insieme e hanno punti di ripristino condivisi coerenti con l'arresto anomalo del sistema e coerenti con l'app in caso di failover.
    * È consigliabile raggruppare le macchine virtuali e i server fisici in modo da rispecchiare i carichi di lavoro. L'abilitazione della coerenza tra più VM può influire sulle prestazioni del carico di lavoro e deve essere usata solo se i computer eseguono lo stesso carico di lavoro ed è necessaria la coerenza.

    ![Abilitare la replica](./media/site-recovery-vmware-to-azure/enable-replication7.png)
13. Fare clic su **Abilita la replica**. È possibile tenere traccia dello stato del processo **Abilita protezione** in **Impostazioni** > **Processi** > **Processi di Site Recovery**. Dopo l'esecuzione del processo **Finalizza protezione** la macchina virtuale è pronta per il failover.

Se si configura l'installazione push, dopo l'abilitazione della replica verrà installato il servizio Mobility. Dopo l'installazione push del servizio Mobility in una VM, verrà avviato un processo di protezione che avrà esito negativo. Dopo l'errore, è necessario riavviare manualmente tutti i computer. Il processo di protezione verrà quindi avviato nuovamente e verrà eseguita la replica iniziale.


### <a name="view-and-manage-vm-properties"></a>Visualizzare e gestire le proprietà della macchina virtuale

È consigliabile verificare le proprietà delle VM e apportare tutte le modifiche eventualmente necessarie.

1. Fare clic su **Elementi replicati** e selezionare il computer. Il pannello **Informazioni di base** visualizza informazioni sulle impostazioni e sullo stato dei computer.
2. In **Proprietà** sono disponibili le informazioni su replica e failover per la VM.
3. In **Calcolo e rete** > **Proprietà di calcolo** è possibile specificare le dimensioni di destinazione e il nome della VM di Azure. Se necessario, modificare il nome in modo che sia conforme ai [requisiti di Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) .
4. Modificare le impostazioni per la rete, la subnet e l'indirizzo IP di destinazione che verranno assegnati alla VM di Azure.

   - È possibile impostare l'indirizzo IP di destinazione.

    - Se non si specifica un indirizzo, il computer di cui è stato eseguito il failover usa DHCP.
    - Se si imposta un indirizzo che non è disponibile al momento del failover, il failover non riesce.
    - Lo stesso indirizzo IP di destinazione può essere usato per il failover di test, se è disponibile nella rete di failover di test.

   - Il numero di schede di rete dipende dalle dimensioni specificate per la macchina virtuale di destinazione:

     - Se il numero di schede di rete nel computer di origine è minore o uguale al numero di schede consentito per le dimensioni del computer di destinazione, la destinazione avrà lo stesso numero di schede dell'origine.
     - Se il numero di schede della macchina virtuale di origine supera il numero consentito per le dimensioni della destinazione, verranno usate le dimensioni massime della destinazione.
     - Ad esempio, se una macchina di origine ha due schede di rete e le dimensioni della macchina di destinazione ne supportano quattro, la macchina di destinazione avrà due schede. Se la macchina di origine dispone di due schede ma le dimensioni di destinazione supportate ne consentono solo una, la macchina di destinazione avrà una sola scheda.     
   - Se la macchina virtuale ha più schede di rete, si connetteranno tutte alla stessa rete.
   - Se la macchina virtuale ha più schede di rete, la prima nell'elenco diventa la scheda di rete *predefinita* nella macchina virtuale di Azure.
5. In **Dischi** vengono visualizzati il sistema operativo della VM e i dischi dati che verranno replicati.

## <a name="run-a-test-failover"></a>Eseguire un failover di test

Dopo aver completato la configurazione, eseguire un failover di test per verificare che tutti gli elementi funzionino come previsto.


1. Per eseguire il failover di una singola macchina, in **Impostazioni** > **Elementi replicati** fare clic sulla VM > sull'icona ** +Failover di test**.

    ![Failover di test](./media/site-recovery-vmware-to-azure/TestFailover.png)

1. Per eseguire il failover di un piano di ripristino, in **Impostazioni** > **Piani di ripristino** fare clic con il pulsante destro del mouse sul piano e quindi scegliere **Failover di test**. Per creare un piano di ripristino, [seguire queste istruzioni](site-recovery-create-recovery-plans.md).  

1. In **Failover di test** selezionare la rete di Azure a cui dovranno connettersi le VM di Azure dopo il failover.

1. Fare clic su **OK** per iniziare il failover. Per tenere traccia dello stato del processo, fare clic sulla VM per visualizzarne le proprietà oppure fare clic sul processo **Failover di test** nel nome dell'insieme di credenziali > **Impostazioni** > **Processi** > **Processi di Site Recovery**.

1. Al termine del failover sarà possibile visualizzare la macchina virtuale di Azure di replica in **Macchine virtuali** nel portale di Azure. Assicurarsi che la macchina virtuale sia delle dimensioni appropriate, che sia connessa alla rete giusta e che sia in esecuzione.

1. Se sono state [preparate le connessioni dopo il failover](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover), sarà possibile connettersi alla VM di Azure.

1. Al termine, fare clic su **Cleanup test failover** (Pulizia failover di test) nel piano di ripristino. Fare clic su **Note** per registrare e salvare eventuali osservazioni associate al failover di test. Verranno eliminate le macchine virtuali create durante il failover di test.

Per altri dettagli, vedere il documento relativo al [failover di test in Azure](site-recovery-test-failover-to-azure.md).
## <a name="vmware-account-permissions"></a>Autorizzazioni dell'account VMware

Site Recovery deve avere accesso a VMware per l'individuazione automatica delle VM da parte del server di elaborazione e per il failover e il failback delle VM.

- **Migrazione**: se si vuole eseguire solo la migrazione di VM VMware in Azure, senza failback, è possibile usare un account VMware con un ruolo di sola lettura. Un ruolo di questo tipo può eseguire il failover, ma non arrestare i computer di origine protetti. Ciò non è necessario per la migrazione.
- **Replica/ripristino**: se si vuole distribuire una replica completa (replica, failover e failback), l'account deve poter eseguire operazioni come la creazione e la rimozione di dischi, l'accensione di VM e così via.
- **Individuazione automatica**: è necessario almeno un account di sola lettura.


**Attività** | **Account/ruolo necessario** | **Autorizzazioni** | **Dettagli**
--- | --- | --- | ---
**Individuazione automatica delle VM VMware da parte del server di elaborazione** | È necessario almeno un utente di sola lettura | Data Center object (Oggetto data center)–> Propagate to Child Object (Propaga a oggetto figlio), role=Read-only (ruolo=Sola lettura) | L'utente viene assegnato a livello di data center e ha accesso a tutti gli oggetti nel data center.<br/><br/> Per limitare l'accesso, assegnare il ruolo **No access** (Nessun accesso) con **Propagate to child object** (Propaga a oggetto figlio) agli oggetti figlio (host vSphere, archivi dati, VM e reti).
**Failover** | È necessario almeno un utente di sola lettura | Data Center object (Oggetto data center)–> Propagate to Child Object (Propaga a oggetto figlio), role=Read-only (ruolo=Sola lettura) | L'utente viene assegnato a livello di data center e ha accesso a tutti gli oggetti nel data center.<br/><br/> Per limitare l'accesso, assegnare il ruolo **No access** (Nessun accesso) con **Propagate to child object** (Propaga a oggetto figlio) agli oggetti figlio (host vSphere, archivi dati, VM e reti).<br/><br/> È utile ai fini della migrazione, ma non per la replica completa, il failover e il failback.
**Failover e failback** | Suggeriamo di creare un ruolo (Azure_Site_Recovery) con le necessarie autorizzazioni e poi assegnare il ruolo a un utente o gruppo VMware | Data Center object (Oggetto data center) –> Propagate to Child Object (Propaga a oggetto figlio), role=Azure_Site_Recovery (ruolo=Azure_Site_Recovery)<br/><br/> Datastore (Archivio dati) -> Allocate space (Alloca spazio), Browse datastore (Sfoglia archivio dati), Low level file operations (Operazioni file di livello basso), Remove file (Rimuovi file), Update virtual machine files (Aggiorna file macchina virtuale)<br/><br/> Network (Rete) -> Network assign (Assegnazione rete)<br/><br/> Risorsa -> Assegnare VM al pool di risorse, migrare la VM spenta, migrare la VM accesa<br/><br/> Tasks (Attività) -> Create task (Crea attività), Update task (Aggiorna attività)<br/><br/> Virtual machine (Macchina virtuale) -> Configuration (Configurazione)<br/><br/> Virtual machine (Macchina virtuale) -> Interact (Interagisci) -> Answer question (Rispondi alla domanda), Device connection (Connessione dispositivo), Configure CD media (Configura supporto CD), Configure floppy media (Configura supporto floppy), Power off (Spegni), Power on (Accendi), VMware tools install (Installazione strumenti VMware)<br/><br/> Virtual machine (Macchina virtuale) -> Inventory (Inventario) -> Create (Crea), Register (Registra), Unregister (Annulla registrazione)<br/><br/> Virtual machine (Macchina virtuale) -> Provisioning -> Allow virtual machine download (Consenti download macchina virtuale), Allow virtual machine files upload (Consenti upload file macchina virtuale)<br/><br/> Macchina virtuale -> Snapshots -> Remove snapshots | L'utente viene assegnato a livello di data center e ha accesso a tutti gli oggetti nel data center.<br/><br/> Per limitare l'accesso, assegnare il ruolo **No access** (Nessun accesso) con **Propagate to child object** (Propaga a oggetto figlio) agli oggetti figlio (host vSphere, archivi dati, VM e reti).


## <a name="next-steps"></a>Passaggi successivi

Quando la replica è operativa, se si verifica un'interruzione viene eseguito il failover in Azure e vengono create VM di Azure dai dati replicati. È quindi possibile accedere a carichi di lavoro e app in Azure finché non viene effettuato il failback nella posizione primaria di nuovo operativa.

- [Altre informazioni](site-recovery-failover.md) sui diversi tipi di failover e su come eseguirli.
- [Altre informazioni](site-recovery-migrate-to-azure.md#migrate-on-premises-vms-and-physical-servers) per eseguire la migrazione di computer invece della replica e del failback.
- [Informazioni sul failback](site-recovery-failback-azure-to-vmware.md), per il failback e la replica di macchine virtuali di Azure al sito locale primario.

## <a name="third-party-software-notices-and-information"></a>Informazioni e comunicazioni sul software di terze parti
Do Not Translate or Localize

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, “Third Party Code”).  Microsoft is the not original author of the Third Party Code.  The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below. Such licenses and information are provided for informational purposes only.  This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.  

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms.

The complete file may be found on the [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.

