<properties 
   pageTitle="Eseguire il failback di server fisici e macchine virtuali VMware nel sito locale | Microsoft Azure"
   description="Informazioni sul failback nel sito locale dopo il failover dei server fisici e delle macchine virtuali VMware in Azure." 
   services="site-recovery" 
   documentationCenter="" 
   authors="ruturaj" 
   manager="mkjain" 
   editor=""/>

<tags
   ms.service="site-recovery"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.workload="required" 
   ms.date="05/10/2016"
   ms.author="ruturajd"/>

# Eseguire il failback di server fisici e macchine virtuali VMware nel sito locale

> [AZURE.SELECTOR]
- [Portale di Azure](site-recovery-failback-azure-to-vmware.md)
- [Portale di Azure classico](site-recovery-failback-azure-to-vmware-classic.md)
- [Portale di Azure classico (legacy)](site-recovery-failback-azure-to-vmware-classic-legacy.md)


Questo articolo descrive come eseguire il failback di macchine virtuali di Azure da Azure al sito locale. Seguire le istruzioni riportate in questo articolo per eseguire il failback delle macchine virtuali VMware o dei server fisici Windows/Linux dopo aver eseguito il failover dal sito locale in Azure seguendo questa [esercitazione](site-recovery-vmware-to-azure-classic.md).



## Panoramica

Questo diagramma illustra l'architettura di failback per questo scenario.

Usare questa architettura quando il server di elaborazione è locale e si usa ExpressRoute.

![Diagramma dell'architettura per Expressroute](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)

Usare questa architettura quando il server di elaborazione è in Azure e si dispone di una rete VPN o di una connessione ExpressRoute.

![Diagramma dell'architettura per VPN](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)

Per vedere l'elenco completo delle porte e il diagramma che illustra l'architettura di failback, fare riferimento all'immagine seguente.

![Failover-Failback - Tutte le porte](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

Funzionamento del failback:

- Dopo aver eseguito il failover in Azure, eseguire il failback nel sito locale procedendo come segue:
	- **Fase 1**: abilitare la riprotezione delle VM di Azure in modo che inizino la replica nelle VM di VMware in esecuzione nel sito locale. 
	- **Fase 2**: dopo la replica delle VM di Azure nel sito locale, effettuare un failover per il failback da Azure.
	- **Fase 3**: dopo aver eseguito il failback dei dati, abilitare la riprotezione delle VM locali in cui è stato effettuato il failback, in modo che inizino la replica in Azure.


### Eseguire il failback nella posizione originaria o in una posizione alternativa

Se è stato eseguito il failover di una macchina virtuale VMware è possibile eseguire il failback nella stessa macchina virtuale di origine, se è ancora presente in locale. In questo scenario viene eseguito il failback delle sole modifiche differenziali. Si noti che:

- Se è stato eseguito il failover di server fisici, il failback viene sempre eseguito in una nuova macchina virtuale VMware.
	- Prima di eseguire il failback di una computer fisico, ricordare che
	- Un computer fisico protetto viene restituito come macchina virtuale dopo aver effettuato il failover da Azure in VMware.
	- È necessario individuare almeno un server di destinazione master e gli host ESX/ESXi necessari per il failback.
- Se si esegue il failback nella macchina virtuale originaria è necessario quanto segue:
	- Se la macchina virtuale è gestita da un server vCenter, l'host ESX della destinazione master deve avere accesso all'archivio dati delle macchine virtuali.
	- Se la macchina virtuale si trova in un host ESX ma non è gestita da vCenter, il disco rigido della macchina virtuale deve trovarsi in un archivio dati accessibile da parte dell'host della destinazione master.
	- Se la macchina virtuale si trova in un host ESX e non usa vCenter, è necessario completare l'individuazione dell'host ESX della destinazione master prima di abilitare la riprotezione. Questa opzione è valida anche per il failback di server fisici.
	- Se è presente una macchina virtuale locale, è anche possibile eliminarla prima di eseguire il failback. Il failback crea quindi una nuova macchina virtuale nello stesso host dell'host ESX di destinazione master.
	
- Quando si esegue il failback in una posizione alternativa i dati vengono ripristinati nello stesso archivio dati e nello stesso host ESX usati dal server di destinazione master locale.


## Prerequisiti

- Per eseguire il failback di server fisici e macchine virtuali VMware, è necessario un ambiente VMware. Il failback in un server fisico non è supportato.
- Per eseguire il failback è necessaria una rete di Azure creata al momento dell'impostazione iniziale della protezione. Il failback richiede una connessione VPN o ExpressRoute dalla rete di Azure in cui si trovano le macchine virtuali di Azure al sito locale.
- Se le macchine virtuali in cui si vuole eseguire il failback sono gestite da un server vCenter, assicurarsi di avere le autorizzazioni necessarie per l'individuazione di macchine virtuali nei server vCenter. [Altre informazioni](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).
- Se in una macchina virtuale sono presenti snapshot la riprotezione avrà esito negativo. È possibile eliminare gli snapshot o i dischi. 
- Prima di eseguire il failback è necessario creare alcuni componenti:
	- **Creare un server di elaborazione in Azure**. Si tratta di una macchina virtuale di Azure che è necessario creare e lasciare in esecuzione durante il failback. È possibile eliminare la macchina al termine del failback.
	- **Creare un server di destinazione master**: il server di destinazione master invia e riceve i dati di failback. Nel server di gestione creato in locale è installato un server di destinazione master per impostazione predefinita. Tuttavia, a seconda del volume di traffico sottoposto a failback, potrebbe essere necessario creare un server di destinazione master separato per il failback.
	- Per creare un server di destinazione master aggiuntivo in esecuzione su Linux, è necessario configurare la macchina virtuale Linux prima di installare il server di destinazione master, come descritto di seguito.
- Quando si esegue un failback, è necessario un server di configurazione locale. Durante il failback, la macchina virtuale deve esistere nel database del server di configurazione, altrimenti il failback non avrà esito positivo. Assicurarsi pertanto di pianificare backup regolari del server. In caso di emergenza, è necessario ripristinare il server con lo stesso indirizzo IP in modo che il failback funzioni.

## Criteri di failback
Per eseguire la replica locale, sono necessari criteri di failback. Questi criteri vengono creati automaticamente quando si creano i criteri di direzione in avanti. Si noti che:

1. Questi criteri vengo associati automaticamente al server di configurazione durante la creazione.
2. Questi criteri non sono modificabili.
3. Ii valori impostati dei criteri sono: Soglia RPO = 15 minuti, Conservazione del punto di ripristino = 24 ore, Frequenza snapshot coerente con l’applicazione = 60 minuti) ![](./media/site-recovery-failback-azure-to-vmware-new/failback-policy.png)

## Configurare il server di elaborazione in Azure

È necessario installare un server di elaborazione in Azure per permettere alle macchine virtuali di Azure di rinviare i dati al server di destinazione master locale.

Se i computer sono stati protetti come risorse classiche (ovvero la macchina virtuale ripristinata in Azure è una VM classica), è necessario un server di elaborazione classico in Azure. Se i computer sono stati ripristinati con Resource Manager come tipo di distribuzione, è necessario un server di elaborazione di tipo di distribuzione Resource Manager. Il tipo viene selezionato in base alla rete virtuale di Azure in cui si distribuisce il server di elaborazione.

1.  Nell’Insieme di credenziali > Impostazioni > Gestisci infrastruttura di Site Recovery > **Server di configurazione**, sotto l'intestazione Per VMware o computer fisici, selezionare il server di configurazione. Fare clic su + Server di elaborazione

	![](./media/site-recovery-failback-azure-to-vmware-new/add-processserver.png)

2. Per la distribuzione del server di elaborazione scegliere la distribuzione di un server di elaborazione di failback in Azure

3. Selezionare la sottoscrizione in cui sono state ripristinati i computer.

4. Selezionare quindi la rete di Azure in cui si trovano i computer ripristinati. Il server di elaborazione deve trovarsi nella stessa rete in modo che le VM ripristinate e il server di elaborazione possano comunicare.

5. Se è stata selezionata una rete di *distribuzione classica*, verrà richiesto di creare una nuova VM tramite la raccolta di Azure e installare il server di elaborazione al suo interno.

	![](./media/site-recovery-failback-azure-to-vmware-new/add-classic.png)
	
	1. Il nome dell'immagine è *Microsoft Azure Site Recovery Process Server V2*. Assicurarsi di selezionare *Classico* come modello di distribuzione.
	
		![](./media/site-recovery-failback-azure-to-vmware-new/templatename.png)
	
	2. Installare il server di elaborazione seguendo la procedura [specificata qui](./site-recovery-vmware-to-azure-classicz.md#step-5-install-the-management-server),
	
6. Se si seleziona la rete di Azure *Resource Manager*, è necessario fornire gli input seguenti per distribuire il server.

    1. Il gruppo di risorse a cui si vuole distribuire il server
	
	2. Assegnare un nome al server
	
	3. Assegnare una password per il nome utente in modo che sia possibile eseguire l’accesso
	
	4. Scegliere l’account di archiviazione in cui distribuire il server
	
	5. Scegliere la subnet specifica e l'interfaccia di rete per la connessione. Nota: è necessario creare un’[interfaccia di rete](../virtual-network/virtual-networks-multiple-nics.md) (NIC) e selezionarla in fase di distribuzione.
	
		![](./media/site-recovery-failback-azure-to-vmware-new/psinputsadd.png)
	
	6. Fare clic su OK. Si attiverà quindi un processo che creerà una macchina virtuale di tipo di distribuzione Resource Manager con l’installazione del server di elaborazione. È necessario eseguire l’installazione all'interno della VM per registrare il server nel server di configurazione. A tale scopo, seguire [questa procedura](./site-recovery-vmware-to-azure-classic.md#step-5-install-the-management-server).

	7. Verrà attivato un processo di distribuzione del server di elaborazione.

7. Al termine, il server di elaborazione dovrebbe essere elencato nella pagina dei server di configurazione, nella sezione dei server associati della scheda dei server di elaborazione. ![](./media/site-recovery-failback-azure-to-vmware-new/pslistingincs.png)

		
	>[AZURE.NOTE] Il server non è visibile in **Proprietà della VM**, ma solo nella scheda **Server** del server di gestione in cui è stato registrato. Possono essere necessari circa 10-15 minuti perché il server di elaborazione venga visualizzato.


## Configurare il server di destinazione master in locale

Il server di destinazione master riceve i dati di failback. Un server di destinazione master viene installato automaticamente nel server di gestione locale, ma se si esegue il failback di una grande quantità di dati potrebbe essere necessario configurare un server di destinazione master aggiuntivo. Procedere come segue:

>[AZURE.NOTE] Per installare un server di destinazione master in Linux, seguire le istruzioni riportate nella procedura successiva.

1. Se si installa il server di destinazione master in Windows, aprire la pagina Avvio rapido dalla macchina virtuale in cui si sta installando il server di destinazione master e scaricare il file di installazione per l'installazione guidata unificata di Azure Site Recovery.
2. Eseguire la configurazione, quindi in **Prima di iniziare** selezionare **Aggiungere server di elaborazione per scalare la distribuzione**.
3. Completare la procedura guidata come per la [configurazione del server di gestione](site-recovery-vmware-to-azure-classic.md#step-5-install-the-management-server). Nella pagina dei **dettagli del server di configurazione** specificare l'indirizzo IP del server di destinazione master e una passphrase per accedere alla VM.

### Configurare una macchina virtuale Linux come server di destinazione master
Per configurare il server di gestione che esegue il server di destinazione master come una macchina virtuale Linux, è necessario installare il sistema operativo minimo CentOS 6.6, recuperare gli ID SCSI per ogni disco rigido SCSI, installare alcuni pacchetti aggiuntivi e applicare alcune modifiche personalizzate.

#### Installare CentOS 6.6

1.	Installare il sistema operativo minimo CentOS 6.6 nella macchina virtuale del server di gestione. Lasciare l'immagine ISO in un'unità DVD e avviare il sistema. Ignorare il test dei supporti, selezionare Inglese (Stati Uniti) come lingua, selezionare **Basic Storage Devices**, verificare che il disco rigido non contenga dati importanti e fare clic su **Yes**. Tutti i dati verranno rimossi. Immettere il nome host del server di gestione e selezionare la scheda di rete del server. Nella finestra di dialogo **Editing System** selezionare ** Connect automatically** e aggiungere un indirizzo IP statico, una rete e le impostazioni DNS. Specificare un fuso orario e una password radice per accedere al server di gestione. 
2.	Quando viene richiesto il tipo di installazione, selezionare **Create Custom Layout** come partizione. Dopo aver selezionato **Next**, selezionare **Free** e infine fare clic su Create. Creare partizioni **/**, **/var/crash** e **/home** con **FS Type:** **ext4**. Creare la partizione di scambio con **FS Type: swap**.
3.	Se vengono rilevati dispositivi preesistenti viene visualizzato un messaggio di avviso. Fare clic su **Format** per formattare l'unità con le impostazioni della partizione. Fare clic su **Write change to disk** per applicare le modifiche della partizione.
4.	Selezionare **Install boot loader** > **Next** per installare il caricatore d'avvio nella partizione radice.
5.	Quando l'installazione è completata, selezionare **Reboot**.


#### Recuperare gli ID SCSI

1. Dopo l'installazione, recuperare gli ID SCSI per ogni disco rigido SCSI nella macchina virtuale. A tale scopo, arrestare la macchina virtuale del server di gestione e nelle proprietà della macchina virtuale in VMware fare clic con il pulsante destro del mouse sulla voce della macchina virtuale > **Edit Settings** > **Options**.
2. Selezionare **Advanced** > **General item** e fare clic su **Configuration Parameters**. Quando il computer è in esecuzione questa opzione è disattivata. Per attivarla è necessario arrestare il computer.
3. Se la riga **disk.EnableUUID** esiste, assicurarsi che il valore sia impostato su **True**, con distinzione tra maiuscole/minuscole. Se il valore è già impostato su True, è possibile annullare e testare il comando SCSI in un sistema operativo guest dopo l'avvio. 
4.	Se la riga non esiste, fare clic su **Add Row** e aggiungerla impostando il valore su **True**. Non usare le virgolette doppie.

#### Installare pacchetti aggiuntivi

È necessario scaricare e installare alcuni pacchetti aggiuntivi.

1.	Assicurarsi che il server di destinazione master sia connesso a Internet.
2.	Eseguire questo comando per scaricare e installare 15 pacchetti dal repository di CentOS: **# yum install –y xfsprogs perl lsscsi rsync wget kexec-tools**.
3.	Se le macchine di origine da proteggere eseguono Linux con il file system Reiser o XFS per il dispositivo di avvio o radice, è necessario scaricare e installare pacchetti aggiuntivi come indicato di seguito:

	- # cd /usr/local
	- # wget [http://elrepo.org/linux/elrepo/el6/x86\_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86\_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm)
	- # wget [http://elrepo.org/linux/elrepo/el6/x86\_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86\_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm)
	- # rpm –ivh kmod-reiserfs-0.0-1.el6.elrepo.x86\_64.rpm reiserfs-utils-3.6.21-1.el6.elrepo.x86\_64.rpm
	- # wget [http://mirror.centos.org/centos/6.6/os/x86\_64/Packages/xfsprogs-3.1.1-16.el6.x86\_65.rpm](http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm)
	- # rpm –ivh xfsprogs-3.1.1-16.el6.x86\_64.rpm

#### Applicare modifiche personalizzate

Per applicare modifiche personalizzate dopo aver completato i passaggi successivi all'installazione e aver installato i pacchetti, eseguire queste operazioni:

1.	Copiare il file binario di RHEL 6-64 Unified Agent nella macchina virtuale. Eseguire questo comando per decomprimere il file binario: **tar –zxvf <file name>**
2.	Eseguire questo comando per concedere le autorizzazioni: **# chmod 755 ./ApplyCustomChanges.sh**
3.	Eseguire lo script: **# ./ApplyCustomChanges.sh**. Eseguire lo script solo una volta. Riavviare il server dopo l'esecuzione dello script.


## Eseguire il failback

### Abilitare la riprotezione delle macchine virtuali di Azure

1.	Nell'Insieme di credenziali > elementi replicati > selezionare la macchina virtuale di cui è stato eseguito il failover e fare clic con il pulsante destro su **Riproteggi**. È anche possibile fare clic sulla macchina virtuale e selezionare la riprotezione dai pulsanti di comando.
2.	Nel pannello, si può osservare che la direzione della protezione “da Azure al sito locale” è già selezionata.
3.  In **Server di destinazione master** e **Server di elaborazione** selezionare il server di destinazione master locale e il server di elaborazione della VM di Azure.
4.  Selezionare l’**archivio dati** in cui ripristinare i dischi in locale. Questa opzione viene utilizzata quando la VM locale viene eliminata e devono essere creati nuovi dischi. Questa opzione viene ignorata se i dischi esistono già, ma è necessario specificare un valore. 
5.	Per arrestare i punti temporali in cui la VM viene replicata nel sito locale viene usata un’unità di conservazione. Di seguito sono indicati alcuni criteri per un’unità di conservazione, senza i quali l’unità non sarà elencata per il server master di destinazione. a. Il volume non deve essere in uso per altri scopi (destinazione di replica e così via). b. Il volume non deve essere in modalità di blocco. c. Il volume non deve essere il volume della cache. Nel volume non deve esistere un’installazione MT. Il volume di installazione personalizzata PS+MT non è idoneo per il volume di conservazione. Qui il volume PS-MT installato è il volume della cache di MT. d. Il file system del volume non deve essere di tipo FAT e FAT32. e. La capacità del volume deve essere diversa da zero. e. Il volume di conservazione predefinito per Windows è il volume R. f. Il volume di conservazione predefinito per Linux è /mnt/retention/.

6.  I criteri di failback verranno selezionati automaticamente.

7.	Dopo aver fatto clic su **OK** per avviare la riprotezione, inizia un processo di replica della macchina virtuale da Azure nel sito locale. È possibile monitorare l'avanzamento nella scheda **Processi**.

Per eseguire il ripristino in un percorso alternativo, selezionare l'unità di conservazione e l'archivio dati configurati per il server di destinazione master. Quando si esegue il failback al sito locale, le macchine virtuali VMware nel piano di protezione di failback usano lo stesso archivio dati del server di destinazione master. Per ripristinare la macchina virtuale di Azure di replica nella stessa macchina virtuale locale, quest'ultima deve già trovarsi nello stesso archivio dati del server di destinazione master. Se non è presente alcuna macchina virtuale in locale, ne viene creata una nuova durante la riprotezione. ![](./media/site-recovery-failback-azure-to-vmware-new/reprotectinputs.png)



È anche possibile eseguire la riprotezione a livello di piano di ripristino. Se si dispone di un gruppo di replica, può essere riprotetto solo usando un piano di ripristino. Durante la riprotezione tramite un piano di ripristino, è necessario assegnare i valori sopra riportati per ogni computer protetto.

>[AZURE.NOTE] Un gruppo di replica deve essere protetto con la stessa destinazione master. In caso di protezione con un server di destinazione master diverso, non è possibile accettare una temporizzazione comune.

### Eseguire un failover nel sito locale

Dopo la riprotezione della macchina virtuale è possibile avviare un failover da Azure nel sito locale.

1.	Nella pagine degli elementi replicati selezionare la macchina virtuale contenente il menu **Failover non pianificato**.
2.	In **Conferma failover** verificare la direzione del failover (da Azure) e selezionare il punto di ripristino da usare per il failover. Usare il più recente o il più recente coerente con l’app). Il punto coerente con l’app deve essere antecedente all’ultimo punto di temporizzazione e causerà la perdita di alcuni dati.
3.	Durante il failover, Site Recovery arresta le macchine virtuali di Azure. Assicurarsi che il failback sia stato completato e quindi verificare che le macchine virtuali di Azure siano state arrestate come previsto.

### Riproteggere il sito locale

Al termine del failback, è necessario eseguire il commit della macchina virtuale per garantire che vengano eliminate le macchine virtuali ripristinate in Azure.

1. Fare clic con il pulsante destro del mouse sull'elemento protetto e quindi su Commit. Verrà attivato un processo che rimuoverà le macchine virtuali precedentemente ripristinate in Azure.

Al termine del commit i dati si trovano nuovamente nel sito locale, ma non sono protetti. Per avviare nuovamente la replica in Azure, eseguire queste operazioni:

1.	Nell’Insieme di credenziali > Impostazioni > Elementi replicati selezionare le VM di cui è stato eseguito il failback e fare clic su **Riproteggi**. 
2.  Specificare il valore del server di elaborazione da usare per inviare i dati ad Azure.
3.	Fare clic su OK per avviare il processo di riprotezione.

Al termine della riprotezione, la VM verrà replicata in Azure e sarà possibile eseguire il failover.


### Problemi comuni di failback

1. Se si esegue l'individuazione di vCenter con utente di sola lettura e la protezione delle macchine virtuali, il processo funziona e il failover viene eseguito. Al momento della riprotezione, si verificherà un errore poiché non è stato possibile individuare gli archivi dati. Non verranno quindi visualizzati gli archivi dati elencati durante la riprotezione. Per risolvere questo problema, è possibile aggiornare le credenziali di vCenter usando un account appropriato che abbia le autorizzazioni, quindi ripetere il processo. [Altre informazioni](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access)
2. Quando si esegue il failback di una macchina virtuale Linux e l'esecuzione è locale, si noti che il pacchetto di gestione reti viene disinstallato dal computer. Quando la macchina virtuale viene infatti ripristinata in Azure, il pacchetto di gestione reti viene rimosso.
3. Se per la configurazione di una macchina virtuale viene usato un indirizzo IP statico e viene effettuato il failover in Azure, l'indirizzo IP viene acquisito tramite DHCP. Quando il failover viene effettuato di nuovo in locale, la macchina virtuale continua a usare il protocollo DHCP per acquisire l'indirizzo IP. Se necessario, accedere manualmente al computer e impostare l'indirizzo IP nell'indirizzo statico.
4. Se si usa l'edizione gratuita di ESXi 5.5 o di vSphere 6 Hypervisor, sarà possibile effettuare il failover, ma non eseguire il failback. Sarà necessario scegliere l'aggiornamento alla licenza di valutazione per abilitare il failback.

## Eseguire il failback con ExpressRoute

È possibile eseguire il failback usando una connessione VPN o Azure ExpressRoute. Per usare ExpressRoute occorre tenere presente quanto segue:

- ExpressRoute deve essere configurato nella rete virtuale di Azure in cui viene eseguito il failover dei computer di origine e in cui si trovano le macchine virtuali di Azure dopo il failover.
- I dati vengono replicati in un account di archiviazione di Azure in un endpoint pubblico. È necessario configurare il peering pubblico in ExpressRoute specificando il data center di destinazione per consentire l'uso di ExpressRoute da parte della replica di Site Recovery.

<!---HONumber=AcomDC_0518_2016-->