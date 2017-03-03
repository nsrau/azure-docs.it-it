---
title: Eseguire il failback di una macchina virtuale VMware da Azure al sito locale | Documentazione Microsoft
description: Informazioni sul failback nel sito locale dopo il failover dei server fisici e delle macchine virtuali VMware in Azure.
services: site-recovery
documentationcenter: 
author: ruturaj
manager: mkjain
editor: 
ms.assetid: 5a47337f-89a9-43e8-8fdc-7da373c0fb0f
ms.service: site-recovery
ms.devlang: na
ms.tgt_pltfrm: na
ms.topic: article
ms.workload: required
ms.date: 10/05/2016
ms.author: ruturajd
translationtype: Human Translation
ms.sourcegitcommit: 7688df2aac74d10de7c188ad46f8ab2ec38bbe86
ms.openlocfilehash: 34c335d17641e9df9b64a7882448afc268e4da7c
ms.lasthandoff: 02/03/2017


---
# <a name="fail-back-vmware-virtual-machines-and-physical-servers-to-the-on-premises-site"></a>Eseguire il failback di server fisici e macchine virtuali VMware nel sito locale
> [!div class="op_single_selector"]
> * [Portale di Azure](site-recovery-failback-azure-to-vmware.md)
> * [Portale di Azure classico](site-recovery-failback-azure-to-vmware-classic.md)
> * [Portale di Azure classico (legacy)](site-recovery-failback-azure-to-vmware-classic-legacy.md)

Questo articolo descrive come eseguire il failback di macchine virtuali di Azure da Azure al sito locale. Seguire le istruzioni riportate di seguito per eseguire il failback delle macchine virtuali VMware o dei server fisici Windows o Linux dopo aver eseguito il failover dal sito locale in Azure seguendo questa [esercitazione](site-recovery-vmware-to-azure-classic.md).

## <a name="overview"></a>Panoramica
I diagrammi riportati in questa sezione illustrano l'architettura di failback per questo scenario.

Quando il server di elaborazione è locale e si usa una connessione ExpressRoute di Azure, usare questa architettura:

![Diagramma dell'architettura per ExpressRoute](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)

Quando il server di elaborazione è in Azure e si dispone di una rete VPN o di una connessione ExpressRoute, usare questa architettura:

![Diagramma dell'architettura per VPN](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)

Per un elenco completo delle porte e il diagramma che illustra l'architettura di failback, fare riferimento all'immagine seguente:

![Failover-Failback - Elenco di tutte le porte](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

Dopo aver eseguito il failover in Azure, eseguire il failback nel sito locale con i tre passaggi seguenti:

* **Fase 1**: abilitare la riprotezione delle VM di Azure in modo che inizino la replica nelle macchine virtuali di VMware in esecuzione nel sito locale.
* **Fase 2**: dopo la replica delle VM di Azure nel sito locale, eseguire un failover per il failback da Azure.
* **Fase 3**: dopo aver eseguito il failback dei dati, abilitare la riprotezione delle macchine virtuali locali in cui è stato eseguito il failback, in modo che inizino la replica in Azure.

### <a name="fail-back-to-the-original-location-or-an-alternate-location"></a>Failback alla posizione originale o a una posizione alternativa
Dopo aver eseguito il failover di una macchina virtuale VMware è possibile eseguire il failback nella stessa macchina virtuale di origine, se è ancora presente in locale. In questo scenario viene eseguito il failback delle sole modifiche differenziali.

Se è stato eseguito il failover di server fisici, il failback viene sempre eseguito in una nuova macchina virtuale VMware. Prima di eseguire il failback di una computer fisico, ricordare che:
* Un computer fisico protetto viene restituito come macchina virtuale dopo aver eseguito il failover da Azure in VMware. Non è possibile eseguire il failback di un computer fisico Windows Server 2008 R2 SP1 protetto e sottoposto a failover in Azure. Potrà essere eseguito il failback di un computer Windows Server 2008 R2 SP1 avviato come macchina virtuale in locale.
* È necessario individuare almeno un server di destinazione master e gli host ESX/ESXi necessari per il failback.

Se si esegue il failback nella macchina virtuale originaria è necessario quanto segue:

* Se la macchina virtuale è gestita da un server vCenter, l'host ESX della destinazione master deve avere accesso all'archivio dati delle macchine virtuali.
* Se la macchina virtuale si trova in un host ESX ma non è gestita da vCenter, il disco rigido della macchina virtuale deve trovarsi in un archivio dati accessibile da parte dell'host della destinazione master.
* Se la macchina virtuale si trova in un host ESX e non usa vCenter, è necessario completare l'individuazione dell'host ESX della destinazione master prima di abilitare la riprotezione. Questa opzione è valida anche per il failback di server fisici.
* Se è presente una macchina virtuale locale, è anche possibile eliminarla prima di eseguire il failback. Il failback crea quindi una nuova macchina virtuale nello stesso host dell'host ESX di destinazione master.

Quando si esegue il failback in una posizione alternativa, i dati vengono ripristinati nello stesso archivio dati e nello stesso host ESX usati dal server di destinazione master locale.

## <a name="prerequisites"></a>Prerequisiti
* Per eseguire il failback di server fisici e macchine virtuali VMware, è necessario un ambiente VMware. Il failback in un server fisico non è supportato.
* Per eseguire il failback, è necessaria una rete di Azure creata al momento dell'impostazione iniziale della protezione. Il failback richiede una connessione VPN o ExpressRoute dalla rete di Azure in cui si trovano le macchine virtuali di Azure al sito locale.
* Se le macchine virtuali in cui si vuole eseguire il failback sono gestite da un server vCenter, assicurarsi di disporre delle autorizzazioni necessarie per l'individuazione di macchine virtuali nei server vCenter. Per altre informazioni, vedere [Eseguire la replica di macchine virtuali VMware e server fisici in Azure con Azure Site Recovery](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).
* Se in una macchina virtuale sono presenti snapshot, la riprotezione ha esito negativo. È possibile eliminare gli snapshot o i dischi.
* Prima di eseguire il failback, creare i componenti seguenti:
  * **Creare un server di elaborazione in Azure**. Questo componente è una macchina virtuale di Azure da creare e lasciare in esecuzione durante il failback. È possibile eliminare la macchina al termine del failback.
  * **Creare un server di destinazione master**: il server di destinazione master invia e riceve i dati di failback. Nel server di gestione creato in locale è installato un server di destinazione master per impostazione predefinita. Tuttavia, a seconda del volume di traffico sottoposto a failback, potrebbe essere necessario creare un server di destinazione master separato per il failback.
  * Per creare un server di destinazione master aggiuntivo in esecuzione su Linux, configurare la macchina virtuale Linux prima di installare il server di destinazione master, come descritto di seguito.
* Quando si esegue un failback, è necessario un server di configurazione locale. Durante il failback, la macchina virtuale deve esistere nel database del server di configurazione. Se il database del server di configurazione non contiene nessuna macchina virtuale, il failback ha esito negativo. Assicurarsi pertanto di pianificare backup regolari del server. In caso di emergenza, è necessario ripristinare il server con lo stesso indirizzo IP in modo che il failback funzioni.
* Assicurarsi di configurare l'impostazione disk.enableUUID=true nei **parametri di configurazione** della VM di destinazione master in VMware. Se questa riga non esiste, aggiungerla Questa impostazione è necessaria per fornire un identificatore univoco universale (UUID) coerente al file del disco della macchina virtuale (VMDK) perché venga installato correttamente.
* Prestare attenzione alla condizione "Master target server cannot be storage vMotioned" (Il server di destinazione master non può essere sottoposto a Storage vMotion) che può causare l'esito negativo del failback. La macchina virtuale non può avviarsi perché i dischi non sono stati resi disponibili per la macchina.
* Aggiungere un'unità, denominata unità di conservazione, nel server di destinazione master. Aggiungere un disco e formattare l'unità.

## <a name="failback-policy"></a>Criteri di failback
Per eseguire la replica locale, sono necessari criteri di failback. Il criterio viene creato automaticamente quando si crea un criterio di direzione incrementale, che viene automaticamente associato al server di configurazione. Non è modificabile. Il criterio presenta le impostazioni di replica seguenti:

* Soglia RPO: 15 minuti
* Conservazione del punto di ripristino: 24 ore
* Frequenza di snapshot coerenti con l'applicazione: 60 minuti

 ![Impostazioni di replica del criterio di failback](./media/site-recovery-failback-azure-to-vmware-new/failback-policy.png)

## <a name="set-up-a-process-server-in-azure"></a>Configurare un server di elaborazione in Azure
Installare un server di elaborazione in Azure per permettere alle macchine virtuali di Azure di rinviare i dati al server di destinazione master locale.

Se le macchine virtuali sono state protette come risorse classiche (ovvero la macchina virtuale ripristinata in Azure è una VM creata tramite il modello di distribuzione classica), è necessario un server di elaborazione in Azure. Se le macchine virtuali sono state ripristinate con il tipo di distribuzione di Azure Resource Manager, il server di elaborazione deve avere Resource Manager come tipo di distribuzione. Il tipo di distribuzione viene selezionato in base alla rete virtuale di Azure in cui si distribuisce il server di elaborazione.

1. Selezionare il server di configurazione in **Insieme di credenziali** > **Impostazioni** > **Manage Site Recovery Infrastructure (Gestisci infrastruttura di Site Recovery)** (in **Gestisci**) > **Server di configurazione** (sotto **For VMware and Physical Machines**(Per VMware e computer fisici)).
2. Fare clic su **Server di elaborazione**.

  ![Pulsante Server di elaborazione](./media/site-recovery-failback-azure-to-vmware-classic/add-processserver.png)
3. Per la distribuzione del server di elaborazione scegliere la **distribuzione di un server di elaborazione di failback in Azure**.
4. Selezionare la sottoscrizione su cui sono state ripristinate le macchine virtuali.
5. Selezionare la rete Azure su cui sono state ripristinate le macchine virtuali. Il server di elaborazione deve trovarsi nella stessa rete in modo che le VM ripristinate e il server di elaborazione possano comunicare.
6. Se è stata selezionata una rete del *modello di distribuzione classico*, creare una macchina virtuale tramite il marketplace di Azure e quindi installare il server di elaborazione su di essa.

 ![Finestra "Aggiungere il server di elaborazione"](./media/site-recovery-failback-azure-to-vmware-classic/add-classic.png)

 Durante la creazione del server di elaborazione, prestare attenzione a quanto segue:
 * Il nome dell'immagine è *Microsoft Azure Site Recovery Process Server V2*. Selezionare **Classica** come modello di distribuzione.

       ![Select "Classic" as the Process Server deployment model](./media/site-recovery-failback-azure-to-vmware-classic/templatename.png)
 * Installare il server di elaborazione seguendo le istruzioni in [Eseguire la replica di macchine virtuali VMware e server fisici in Azure con Azure Site Recovery](site-recovery-vmware-to-azure-classic.md#step-5-install-the-management-server).
7. Se si seleziona la rete di Azure *Resource Manager*, distribuire il server di elaborazione immettendo le informazioni seguenti:

  * Nome del gruppo di risorse a cui si vuole distribuire il server.
  * Nome host del server.
  * Nome utente e password per l'accesso al server.
  * Account di archiviazione in cui distribuire il server.
  * Subnet e interfaccia di rete a cui eseguire la connessione.
   >[!NOTE]
   >Nota: è necessario creare un'[interfaccia di rete](../virtual-network/virtual-networks-multiple-nics.md) (NIC) e selezionarla durante la distribuzione del server di elaborazione.

    ![Immettere le informazioni nella finestra di dialogo "Aggiungere il server di elaborazione"](./media/site-recovery-failback-azure-to-vmware-classic/psinputsadd.png)

8. Fare clic su **OK**. Questa operazione attiva un processo che crea una macchina virtuale del tipo di distribuzione Resource Manager durante l'installazione del server di elaborazione. Per registrare il server nel server di configurazione, eseguire il programma di installazione all'interno della macchina virtuale seguendo le istruzioni in [Eseguire la replica di macchine virtuali VMware e server fisici in Azure con Azure Site Recovery](site-recovery-vmware-to-azure-classic.md#step-5-install-the-management-server). Viene anche attivato un processo di distribuzione del server di elaborazione.

  Il server di elaborazione è riportato nella scheda **Server di configurazione** > **Server associati** > **Server di elaborazione**.

    ![](./media/site-recovery-failback-azure-to-vmware-new/pslistingincs.png)

    > [!NOTE]
    > Il server di elaborazione non è visibile in **VM properties** (Proprietà della macchina virtuale). È visibile solo nella scheda **Server** all'interno del server di gestione in cui è registrato. Possono essere necessari circa tra i 10 e i 15 minuti perché il server di elaborazione venga visualizzato.


## <a name="set-up-the-master-target-server-on-premises"></a>Configurare il server di destinazione master in locale
Il server di destinazione master riceve i dati di failback. Il server viene installato automaticamente nel server di gestione locale, ma se si esegue il failback di una quantità eccessiva di dati potrebbe essere necessario configurare un server di destinazione master aggiuntivo. Per configurare un server di destinazione master in locale, procedere come segue:

> [!NOTE]
> Per configurare un server di destinazione master in Linux, passare alla procedura successiva. Usare solo il sistema operativo di base CentOS 6.6 come sistema operativo di destinazione master.

1. Se si sta configurando il server di destinazione master su Windows, aprire la pagina di avvio rapido dalla macchina virtuale su cui si sta installando il server di destinazione master.
2. Scaricare il file di installazione per l'installazione guidata unificata di Azure Site Recovery.
3. Eseguire la configurazione, quindi in **Prima di iniziare** selezionare **Add additional process servers to scale out deployment** (Aggiungere server di elaborazione per aumentare le istanze di distribuzione).
4. Completare la procedura guidata come per la [configurazione del server di gestione](site-recovery-vmware-to-azure-classic.md#step-5-install-the-management-server). Nella pagina **Dettagli del server di configurazione** specificare l'indirizzo IP del server di destinazione master e immettere una passphrase per accedere alla macchina virtuale.

### <a name="set-up-a-linux-vm-as-the-master-target-server"></a>Configurare una macchina virtuale Linux come server di destinazione master
Per configurare il server di gestione che esegue il server di destinazione master come macchina virtuale Linux, installare il sistema operativo di base CentOS 6.6. Successivamente, recuperare gli ID SCSI per ogni disco rigido SCSI, installare alcuni pacchetti aggiuntivi e apportare alcune modifiche personalizzate.

#### <a name="install-centos-66"></a>Installare CentOS 6.6

1. Installare il sistema operativo minimo CentOS 6.6 nella macchina virtuale del server di gestione. Lasciare l'immagine ISO in un'unità DVD e avviare il sistema. Ignorare il test dei supporti. Selezionare **Inglese (Stati Uniti)** come lingua, selezionare **Dispositivi di archiviazione di base**, verificare che il disco rigido non contenga dati importanti e fare clic su **Sì**. Tutti i dati verranno rimossi. Immettere il nome host del server di gestione e selezionare la scheda di rete del server.  Nella finestra di dialogo **Modifica del sistema** selezionare **Connetti automaticamente** e aggiungere un indirizzo IP statico, una rete e le impostazioni DNS. Specificare un fuso orario. Per accedere al server di gestione, immettere la password radice.
2. Quando viene richiesto il tipo di installazione, selezionare **Crea layout personalizzato** come partizione. Fare clic su **Avanti**. Selezionare **Gratuito** e quindi fare clic su **Crea**. Creare **/**, **/var/crash** e **/home partitions** con **FS Type:** **ext4**. Creare la partizione di swapping con **FS Type: swap**.
3. Se vengono rilevati dispositivi preesistenti viene visualizzato un messaggio di avviso. Fare clic su **Formatta** per formattare l'unità con le impostazioni della partizione. Fare clic su **Scrivi modifica su disco** per applicare le modifiche della partizione.
4. Selezionare **Installa caricatore di avvio** > **Avanti** per installare il caricatore di avvio nella partizione radice.
5. Al termine dell'installazione, fare clic su **Riavvia**.

#### <a name="retrieve-the-scsi-ids"></a>Recuperare gli ID SCSI

1. Dopo l'installazione, recuperare gli ID SCSI per ogni disco rigido SCSI nella macchina virtuale. A tale scopo, arrestare la VM del server di gestione. Nelle proprietà della VM in VMware, fare clic con il pulsante destro del mouse sulla voce della VM > **Modifica impostazioni** > **Opzioni**.
2. Selezionare **Avanzate** > **Generale** e quindi fare clic su **Parametri di configurazione**. Quando il computer è in esecuzione questa opzione non è disponibile. Per rendere l'opzione disponibile, arrestare la macchina.
3. Eseguire una di queste operazioni:
 * Se la riga **disk.EnableUUID** esiste, verificare che il valore sia impostato su **True**, con distinzione tra maiuscole e minuscole. Se il valore è già impostato su True, è possibile annullare e testare il comando SCSI in un sistema operativo guest dopo l'avvio.
 * Se la riga **disk.EnableUUID** non esiste, fare clic su **Aggiungi riga** e aggiungerla impostando il valore su **True**. Non usare le virgolette doppie.

#### <a name="install-additional-packages"></a>Installare pacchetti aggiuntivi
Scaricare e installare pacchetti aggiuntivi.

1. Assicurarsi che il server di destinazione master sia connesso a Internet.
2. Per scaricare e installare i 15 pacchetti dal repository di CentOS, eseguire questo comando: `# yum install –y xfsprogs perl lsscsi rsync wget kexec-tools`.
3. Se le macchine di origine da proteggere eseguono Linux con il file system Reiser o XFS per il dispositivo di avvio o radice, scaricare e installare pacchetti aggiuntivi come indicato di seguito:

   * \# cd /usr/local
   * \# wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm)
   * \# wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm)
   * \# rpm –ivh kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm
   * \# wget [http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm](http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm)
   * \# rpm –ivh xfsprogs-3.1.1-16.el6.x86_64.rpm
   * \# yum install device-mapper-multipath (questa operazione è necessaria per abilitare i pacchetti Multipath sul server di destinazione master).

#### <a name="apply-custom-changes"></a>Applicare modifiche personalizzate
Dopo aver completato i passaggi successivi all'installazione e aver installato i pacchetti, applicare modifiche personalizzate eseguendo queste operazioni:

1. Copiare il file binario di RHEL 6-64 Unified Agent nella macchina virtuale. Eseguire questo comando per decomprimere il file binario: `tar –zxvf <file name>`.
2. Per concedere autorizzazioni, eseguire questo comando: `# chmod 755 ./ApplyCustomChanges.sh`.
3. Eseguire lo script seguente: `# ./ApplyCustomChanges.sh`. Eseguirlo una sola volta. Riavviare il server dopo l'esecuzione dello script.

## <a name="run-the-failback"></a>Eseguire il failback
### <a name="reprotect-the-azure-vms"></a>Abilitare la riprotezione delle macchine virtuali di Azure
1. In **Insieme di credenziali**, in **Elementi replicati** fare clic con il pulsante destro del mouse sulla VM di cui è stato eseguito il failover e selezionare **Riproteggi**.
2. Nel pannello, si può osservare che la direzione della protezione **Da Azure a locale** è già selezionata.
3. In **Server di destinazione master** e **Server di elaborazione** selezionare il server di destinazione master locale e il server di elaborazione della VM di Azure.
4. Selezionare l'archivio dati in cui ripristinare i dischi in locale. Usare questa opzione quando la VM locale viene eliminata e devono essere creati nuovi dischi. Ignorare questa opzione se i dischi esistono già, ma è necessario specificare un valore.
5. Usare un'unità di conservazione per arrestare i punti temporali in cui la VM viene replicata nel sito locale. Di seguito si elencano alcuni criteri di un'unità di conservazione. Senza questi criteri, l'unità non viene elencato per il server di destinazione master.

  * Il volume non deve essere in uso per altri scopi (destinazione di replica e così via).
  * Il volume non deve essere in modalità di blocco
  * Il volume non deve essere il volume della cache. Nel volume non deve esistere un'installazione di destinazione master. Il volume di installazione personalizzata del server di elaborazione e del server di destinazione master non è idoneo per il volume di conservazione. In questo caso, il volume del server di elaborazione e del server di destinazione master è il volume della cache di destinazione master.
  * Il file system del volume non deve essere di tipo FAT e FAT32.
  * La capacità del volume deve essere diversa da zero.
  * Il volume di conservazione predefinito per Windows è il volume R.
  * Il volume di conservazione predefinito per Linux è /mnt/retention/.

6. I criteri di failback vengono selezionati automaticamente.
7. Dopo aver fatto clic su **OK** per avviare la riprotezione, inizia un processo di replica della VM da Azure al sito locale. È possibile monitorare l'avanzamento nella scheda **Processi** .

Per eseguire il ripristino in un percorso alternativo, selezionare l'unità di conservazione e l'archivio dati configurati per il server di destinazione master. Quando si esegue il failback al sito locale, le macchine virtuali VMware nel piano di protezione di failback usano lo stesso archivio dati del server di destinazione master. Per ripristinare la macchina virtuale di Azure di replica nella stessa macchina virtuale locale, quest'ultima deve già trovarsi nello stesso archivio dati del server di destinazione master. Se non è presente alcuna macchina virtuale in locale, ne viene creata una nuova durante la riprotezione.

![Selezionare "Da Azure a locale" nel menu a discesa.](./media/site-recovery-failback-azure-to-vmware-new/reprotectinputs.png)

È anche possibile eseguire la riprotezione a livello di piano di ripristino. Se si dispone di un gruppo di replica, è possibile riproteggerlo solo usando un piano di ripristino. Per la riprotezione tramite piano di ripristino, usare i valori precedenti per ogni macchina protetta.

> [!NOTE]
> I gruppi di replica devono essere riprotetti con la stessa destinazione master. In caso di riprotezione con server di destinazione master diversi, non è possibile determinare un punto temporale.

### <a name="run-a-failover-to-the-on-premises-site"></a>Eseguire un failover nel sito locale
Dopo la riprotezione della macchina virtuale, è possibile avviare un failover da Azure nel sito locale.

1. Nella pagina degli elementi replicati fare clic con il pulsante destro del mouse sulla macchina virtuale e selezionare **Failover non pianificato**.
2. In **Conferma failover** verificare la direzione del failover (da Azure) e quindi selezionare il punto di ripristino da usare per il failover. Usare il più recente o quello coerente con l'app più recente. Il punto di ripristino coerente con l'applicazione si verifica prima del punto più recente nel tempo e provoca perdita di dati.
3. Durante il failover, Site Recovery arresta le macchine virtuali di Azure. Assicurarsi che il failback sia stato completato e verificare che le macchine virtuali di Azure siano state arrestate come previsto.

### <a name="reprotect-the-on-premises-site"></a>Riproteggere il sito locale
Al termine del failback, eseguire il commit della macchina virtuale per garantire che vengano eliminate le macchine virtuali ripristinate in Azure. A tale scopo, fare clic con il pulsante destro del mouse sull'elemento protetto e quindi su **Commit**. Viene attivato un processo che rimuove le macchine virtuali precedentemente ripristinate in Azure.

Al termine del commit i dati dovrebbero trovarsi nuovamente nel sito locale, ma non sono protetti. Per avviare nuovamente la replica in Azure, eseguire queste operazioni:

1. In **Insieme di credenziali**, **Impostazioni** > **Elementi replicati** selezionare le VM di cui è stato eseguito il failback e quindi fare clic su **Riproteggi**.
2. Specificare il valore del server di elaborazione da usare per inviare i dati ad Azure.
3. Fare clic su **OK**.

Al termine della riprotezione, la VM viene replicata in Azure ed è possibile eseguire il failover.

### <a name="resolve-common-failback-issues"></a>Risolvere i problemi comuni di failback
* Se si esegue l'individuazione di vCenter con utente di sola lettura e la protezione delle macchine virtuali, il processo funziona e il failover viene eseguito. Durante la riprotezione, failover ha esito negativo perché non è possibile individuare gli archivi dati. Non verranno quindi visualizzati gli archivi dati elencati durante la riprotezione. Per risolvere questo problema, è possibile aggiornare le credenziali di vCenter usando un account appropriato che abbia le autorizzazioni, quindi ripetere il processo. Per altre informazioni, vedere [Eseguire la replica di macchine virtuali VMware e server fisici in Azure con Azure Site Recovery](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).
* Quando si esegue il failback di una macchina virtuale Linux e l'esecuzione è locale, si nota che il pacchetto di gestione reti viene disinstallato dal computer. Tale installazione si verifica perché, quando la macchina virtuale viene ripristinata in Azure, il pacchetto di gestione reti viene rimosso.
* Se per la configurazione di una macchina virtuale viene usato un indirizzo IP statico e viene eseguito il failover in Azure, l'indirizzo IP viene acquisito tramite DHCP. Quando il failover viene eseguito di nuovo in locale, la macchina virtuale continua a usare il protocollo DHCP per acquisire l'indirizzo IP. Se necessario, accedere manualmente al computer e impostare l'indirizzo IP torna su un indirizzo statico.
* Se si usa l'edizione gratuita di ESXi 5.5 o di vSphere 6 Hypervisor, sarà possibile eseguire il failover, ma non eseguire il failback. Per abilitare il failback, scegliere l'aggiornamento alla licenza di valutazione del programma.
* Se non è possibile raggiungere il server di configurazione dal server di elaborazione, verificare la connettività al server di configurazione, da Telnet al computer del server di configurazione sulla porta 443. È anche possibile provare a eseguire il ping del server di configurazione dal computer del server di elaborazione. Un server di elaborazione deve avere anche un heartbeat quando è connesso al server di configurazione.
* Se si sta tentando di eseguire il failback a un vCenter alternativo, assicurarsi che il nuovo vCenter e il server di destinazione master vengano rilevati. Come sintomo tipico, gli archivi dati non sono accessibili o visibili nella finestra di dialogo **Riproteggi**.
* Non è possibile eseguire il failback da Azure a locale di un computer WS2008R2SP1 protetto come computer locale fisico.

## <a name="fail-back-with-expressroute"></a>Eseguire il failback con ExpressRoute
È possibile eseguire il failback usando una connessione VPN o ExpressRoute. Per usare una connessione ExpressRoute, tenere presente quanto segue:

* La connessione ExpressRoute deve essere configurata nella rete virtuale di Azure in cui viene eseguito il failover dei computer di origine e in cui si trovano le macchine virtuali di Azure dopo il failover.
* I dati vengono replicati in un account di archiviazione di Azure in un endpoint pubblico. Per consentire l'uso di ExpressRoute da parte della replica di Site Recovery, configurare il peering pubblico in ExpressRoute specificando il data center di destinazione.

