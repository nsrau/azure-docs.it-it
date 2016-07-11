<properties 
   pageTitle="Eseguire il failback di server fisici e macchine virtuali VMware da Azure a VMware (legacy) | Microsoft Azure" 
   description="Questo articolo descrive come eseguire il failback di una macchina virtuale VMware replicata in Azure con Azure Site Recovery." 
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
   ms.workload="storage-backup-recovery" 
   ms.date="03/06/2016"
   ms.author="ruturajd@microsoft.com"/>

# Failback di server fisici e macchine virtuali VMware da Azure a VMware con Azure Site Recovery (legacy)

> [AZURE.SELECTOR]
- [Portale di Azure](site-recovery-failback-azure-to-vmware.md)
- [Portale di Azure classico](site-recovery-failback-azure-to-vmware-classic.md)
- [Portale di Azure classico (Legacy)](site-recovery-failback-azure-to-vmware-classic-legacy.md)


Il servizio Azure Site Recovery favorisce l'attuazione della strategia di continuità aziendale e ripristino di emergenza (BCDR) orchestrando le operazioni di replica, failover e ripristino delle macchine virtuali e dei server fisici. È possibile replicare i computer in Azure o in un data center locale secondario. Per una rapida panoramica, leggere [Che cos'è Azure Site Recovery?](site-recovery-overview.md)

## Panoramica

Questo articolo descrive come eseguire il failback di macchine virtuali VMware e server fisici Windows/Linux da Azure al sito locale dopo aver eseguito la replica dal sito locale ad Azure.

>[AZURE.NOTE] Questo articolo descrive uno scenario legacy. Se è stata eseguita la replica ad Azure tramite [queste istruzioni legacy](site-recovery-vmware-to-azure-classic-legacy.md), è consigliabile usare solo le istruzioni in questo articolo. Se si configura la replica tramite la [distribuzione avanzata](site-recovery-vmware-to-azure-classic-legacy.md), seguire le istruzioni in [questo articolo](site-recovery-failback-azure-to-vmware-classic.md) per eseguire il failback.


## Architettura

Questo diagramma rappresenta lo scenario di failover e di failback. Le linee blu sono le connessioni utilizzate durante il failover. Le linee rosse indicano le connessioni utilizzate durante il failback. Le righe con frecce indicano la connessione a internet.

![](./media/site-recovery-failback-azure-to-vmware/vconports.png)

## Prima di iniziare 

- È necessario aver eseguito il failover delle VM VMware o dei server fisici e che questi vengano eseguiti in Azure.
- Si noti che è possibile eseguire il failback solo di macchine virtuali VMware e di server fisici Windows/Linux da Azure a macchine virtuali VMware nel sito primario locale. Se si esegue il failback di un computer fisico, il failover in Azure lo convertirà in una VM di Azure e il failback in VMware lo convertirà in una VM VMware.

Ecco come configurare il failback:

1. **Configurare i componenti di failback**: è necessario configurare un server vContinuum locale e associarlo alla VM del server di configurazione in Azure. Deve essere configurato anche un server di elaborazione come VM di Azure per inviare dati al server di destinazione master locale. Registrare il server di elaborazione con il server di configurazione che ha gestito il failover e installare un server di destinazione master locale. Se è necessario un server di destinazione master Windows, questo viene configurato automaticamente durante l'installazione di vContinuum. Se è necessario Linux, configurarlo manualmente in un server separato.
2. **Abilitare la protezione e il failback**: dopo aver configurato i componenti, in vContinuum è necessario abilitare la protezione per le VM di Azure sottoposte a failover. È necessario eseguire una verifica di conformità sulle VM e un failover da Azure al sito locale. Al termine del failback riproteggere i computer locali in modo che inizino la replica in Azure.



## Passaggio 1: Installare vContinuum in locale

Sarà necessario installare un server vContinuum in locale e associarlo al server di configurazione.

1.  [Scaricare vContinuum](http://go.microsoft.com/fwlink/?linkid=526305).
2.  Quindi scaricare la versione [di aggiornamento di vContinuum](http://go.microsoft.com/fwlink/?LinkID=533813).
3. Installare la versione più recente di vContinuum. Nella pagina **Welcome** fare clic su **Next**. ![](./media/site-recovery-failback-azure-to-vmware/image2.png)
4.  Nella prima pagina della procedura guidata specificare l'indirizzo IP e la porta del server CX. Selezionare **Use HTTPS**.

	![](./media/site-recovery-failback-azure-to-vmware/image3.png)

5.  Trovare l'indirizzo IP del server di configurazione nella scheda **Dashboard** della VM server di configurazione in Azure. ![](./media/site-recovery-failback-azure-to-vmware/image4.png)

6.  Trovare la porta pubblica HTTPS del server di configurazione nella scheda **Endpoint** della VM server di configurazione in Azure.

	![](./media/site-recovery-failback-azure-to-vmware/image5.png)

7. Nella pagina **CS Passphrase Details** specificare la passphrase annotata quando si è registrato il server di configurazione. Se non la si ricorda, cercarla in **C:\\Programmi (x86)\\InMage Systems\\private\\connection.passphrase** nella VM server di configurazione.

	![](./media/site-recovery-failback-azure-to-vmware/image6.png)

8.  Nella pagina **Select Destination Location** specificare dove si vuole installare il server vContinuum e fare clic su **Install**.

	![](./media/site-recovery-failback-azure-to-vmware/image7.png)

9. Al termine dell'installazione, è possibile avviare vContinuum. ![](./media/site-recovery-failback-azure-to-vmware/image8.png)


## Passaggio 2: Installare un server di elaborazione in Azure 

È necessario installare un server di elaborazione in Azure in modo che le VM in Azure possano inviare i dati a un server di destinazione master locale.

1.  Nella pagina **Server di configurazione** in Azure, effettuare una selezione per aggiungere un nuovo server di elaborazione.

	![](./media/site-recovery-failback-azure-to-vmware/image9.png)

2.  Specificare un nome per il server di elaborazione e un nome e una password per connettersi alla macchina virtuale come amministratore. Selezionare il server di configurazione in cui si vuole registrare il server di elaborazione. Questo deve essere lo stesso server utilizzato per la protezione e il failover delle macchine virtuali.
3.  Specificare la rete di Azure in cui distribuire il server di elaborazione. Deve essere la stessa rete del server di configurazione. Specificare un indirizzo IP univoco dalla subnet selezionata e avviare la distribuzione.

	![](./media/site-recovery-failback-azure-to-vmware/image10.png)

4.  Viene attivato un processo di distribuzione del server di elaborazione.

	![](./media/site-recovery-failback-azure-to-vmware/image11.png)

5.  Dopo che il server di elaborazione è stato distribuito in Azure, è possibile accedere al server usando le credenziali specificate. Registrare il server di elaborazione nello stesso modo in cui si è registrato il server di elaborazione locale.

	![](./media/site-recovery-failback-azure-to-vmware/image12.png)

>[AZURE.NOTE] I server registrati durante il failback non saranno visibili nelle proprietà della VM in Site Recovery. Sono visibili solo nella scheda **Server** del server di configurazione in cui sono stati registrati. La visualizzazione del server di elaborazione nella scheda può richiedere circa 10-15 minuti.


## Passaggio 3: Installare un server di destinazione master in locale

A seconda del sistema operativo delle macchine virtuali di origine, sarà necessario installare un server di destinazione master Linux o Windows in locale.

### Distribuire un server di destinazione master Windows

Una destinazione master Windows è già in bundle nel programma di installazione di vContinuum. Quando si installa vContinuum, nello stesso computer viene distribuito anche un server master che viene registrato nel server di configurazione.

1.  Per iniziare la distribuzione, creare un computer vuoto in locale nell'host ESX in cui si vuole ripristinare le VM da Azure.

2.  Assicurarsi che ci siano almeno due dischi collegati alla VM: uno viene usato per il sistema operativo e il secondo viene usato per l'unità di conservazione.

3.  Installare il sistema operativo.

4.  Installare vContinuum nel server. Questa operazione completa l'installazione del server di destinazione master.

### Distribuire un server di destinazione master Linux

1.  Per iniziare la distribuzione, creare un computer vuoto in locale nell'host ESX in cui si vuole ripristinare le VM da Azure.

2.  Assicurarsi che ci siano almeno due dischi collegati alla VM: uno viene usato per il sistema operativo e il secondo viene usato per l'unità di conservazione.

3.  Installare il sistema operativo Linux. Il sistema di destinazione master Linux non deve usare LVM per gli spazi di archiviazione radice o di conservazione. Il server di destinazione master Linux è configurato per impostazione predefinita in modo da evitare l'individuazione di partizioni e dischi LVM.
4.  Partizioni che è possibile creare:

	![](./media/site-recovery-failback-azure-to-vmware/image13.png)

5.  Eseguire la procedura seguente successiva all'installazione prima di iniziare l'installazione della destinazione master.


#### Procedure di successive all’installazione del sistema operativo

Per ottenere l'ID SCSI per ogni disco rigido SCSI in una macchina virtuale Linux, abilitare il parametro "disk.EnableUUID = TRUE", come segue:

1. Arrestare la macchina virtuale.
2. Fare clic con il pulsante destro del mouse sulla voce della VM nel pannello sinistro, quindi selezionare **Edit Settings**.
3. Scegliere la scheda **Options (Opzioni)**. Selezionare **Advanced>General item** > **Configuration Parameters**. L'opzione **Configuration Parameters** è disponibile solo quando il computer viene arrestato.

	![](./media/site-recovery-failback-azure-to-vmware/image14.png)

4. Controllare se esiste una riga con il valore **disk.EnableUUID**. Se esiste ed è impostata su **False**, impostarla su **True** (senza distinzione maiuscole/minuscole). Se il valore esiste ed è impostato su True, fare clic su **Cancel** e testare il comando SCSI nel sistema operativo guest dopo l'avvio. Se non esiste, fare clic su **Add Row** (Aggiungi riga).
5. Aggiungere disk.EnableUUID nella colonna **Name**. Impostare il relativo valore come TRUE. Non aggiungere i valori precedenti insieme alle virgolette doppie.

	![](./media/site-recovery-failback-azure-to-vmware/image15.png)

#### Scaricare e installare i pacchetti aggiuntivi

NOTA: verificare che il sistema abbia la connettività Internet prima di scaricare e installare i pacchetti aggiuntivi.

\# yum install -y xfsprogs perl lsscsi rsync wget kexec-tools

Il comando scarica questi 15 pacchetti dal repository CentOS 6.6 e li installa:

bc-1.06.95-1.el6.x86\_64.rpm

busybox-1.15.1-20.el6.x86\_64.rpm

elfutils-libs-0.158-3.2.el6.x86\_64.rpm

kexec-tools-2.0.0-280.el6.x86\_64.rpm

lsscsi-0.23-2.el6.x86\_64.rpm

lzo-2.03-3.1.el6\_5.1.x86\_64.rpm

perl-5.10.1-136.el6\_6.1.x86\_64.rpm

perl-Module-Pluggable-3.90-136.el6\_6.1.x86\_64.rpm

perl-Pod-Escapes-1.04-136.el6\_6.1.x86\_64.rpm
	
perl-Pod-Simple-3.13-136.el6\_6.1.x86\_64.rpm

perl-libs-5.10.1-136.el6\_6.1.x86\_64.rpm

perl-version-0.77-136.el6\_6.1.x86\_64.rpm

rsync-3.0.6-12.el6.x86\_64.rpm

snappy-1.1.0-1.el6.x86\_64.rpm

1\.12-wget-5.el6\_6.1.x86\_64.rpm

NOTA: se il computer di origine usa il file system Reiser o XFS per il dispositivo di avvio o radice, i pacchetti seguenti devono essere scaricati e installati nella destinazione master Linux prima della protezione.

\# cd /usr/local

\# wget <http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm>

\# wget <http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm>

\# rpm -ivh kmod-reiserfs-0.0-1.el6.elrepo.x86\_64.rpm reiserfs-utils-3.6.21-1.el6.elrepo.x86\_64.rpm

\# wget <http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_64.rpm>

\# rpm -ivh xfsprogs-3.1.1-16.el6.x86\_64.rpm

#### Applicare le modifiche di configurazione personalizzate

Prima di applicare queste modifiche, verificare di avere completato la sezione precedente, quindi seguire questa procedura:


1. Copiare il file binario di RHEL 6-64 Unified Agent nel sistema operativo appena creato.

2. Eseguire questo comando per decomprimere il file binario: **tar -zxvf <nome file>**

3. Eseguire questo comando per concedere le autorizzazioni: # **chmod 755 ./ApplyCustomChanges.sh**

4. Eseguire lo script: **# ./ApplyCustomChanges.sh**. Eseguire lo script solo una volta sul server. Riavviare il server dopo l'esecuzione dello script.



### Installare il server Linux


1. [Scaricare](http://go.microsoft.com/fwlink/?LinkID=529757) il file di installazione.
2. Copiare il file nella macchina virtuale di destinazione master Linux usando un'utilità client sftp di propria scelta. In alternativa, è possibile accedere alla macchina virtuale di destinazione master Linux e usare wget per scaricare il pacchetto di installazione dal collegamento specificato.
3. Accedere alla macchina virtuale del server di destinazione master Linux con un client ssh di propria scelta.
4. Se si è connessi alla rete di Azure in cui è stato distribuito il server di destinazione master Linux tramite una connessione VPN, usare l'indirizzo IP interno del server che è possibile trovare nella scheda **Dashboard** della macchina virtuale e la porta 22 per connettersi al server di destinazione master Linux usando Secure Shell.
5. Se ci si connette al server di destinazione master Linux tramite una connessione Internet pubblica, usare l'indirizzo IP virtuale pubblico del server di destinazione master Linux (dalla scheda **Dashboard** delle macchine virtuali) e l'endpoint pubblico creato per ssh per accedere al server Linux.
6. Estrarre i file dall'archivio tar del programma di installazione del server di destinazione master Linux compresso eseguendo: *"tar –xvzf Microsoft-ASR\_UA\_8.2.0.0\_RHEL6-64\"* dalla directory contenente il file del programma di installazione.

	![](./media/site-recovery-failback-azure-to-vmware/image16.png)

7. Se sono stati estratti i file del programma di installazione da una directory diversa, passare alla directory in cui sono stati estratti i contenuti dell'archivio tar. Da questo percorso di directory eseguire "sudo ./install.sh".

	![](./media/site-recovery-failback-azure-to-vmware/image17.png)

8. Quando viene richiesto di scegliere un ruolo primario, selezionare **2 (Master Target)**. Lasciare le altre opzioni di installazione interattiva sui rispettivi valori predefiniti.
9. Attendere che l'installazione continui e venga visualizzata l'interfaccia di configurazione host. L'utilità di configurazione host per il server di destinazione master Linux è un'utilità della riga di comando. Non ridimensionare la finestra dell'utilità client ssh. Usare i tasti di direzione per selezionare l'opzione **Global** e premere INVIO sulla tastiera.

	![](./media/site-recovery-failback-azure-to-vmware/image18.png)

	![](./media/site-recovery-failback-azure-to-vmware/image19.png)


10. Nel campo **IP** immettere l'indirizzo IP interno del server di configurazione (è possibile trovarlo nella scheda **Dashboard** della VM del server di configurazione) e premere INVIO. Nel campo **Port** immettere **22** e premere INVIO.
11.  Lasciare **Use HTTPS** impostato su **Yes** e premere INVIO.
12.  Immettere la passphrase che è stata generata nel server di configurazione. Se si usa un client PUTTY client da un computer Windows per comunicare tramite ssh con la macchina virtuale Linux, è possibile usare MAIUSC+INS per incollare il contenuto degli Appunti. Copiare la passphrase locale negli Appunti utilizzando CTRL+C e utilizzare MAIUSC+INS per incollare il codice. Premere INVIO.
13.  Usare il tasto freccia DESTRA per uscire e premere INVIO. Attendere il completamento dell'installazione.

	![](./media/site-recovery-failback-azure-to-vmware/image20.png)

Se per qualche motivo non è stato possibile registrare il server di destinazione master Linux nel server di configurazione, è possibile ripetere l'operazione eseguendo l'utilità di configurazione host /usr/local/ASR/Vx/bin/hostconfigcli. Prima sarà necessario impostare le autorizzazioni di accesso alla directory eseguendo chmod come utente con privilegi avanzati.


#### Convalidare la registrazione di destinazione master

È possibile convalidare la registrazione del server di destinazione master nel server di configurazione nell'insieme di credenziali di Azure Site Recovery > **Server di configurazione** > **Dettagli server**.

>[AZURE.NOTE] Dopo avere registrato il server di destinazione master, se vengono visualizzati errori di configurazione che informano che la macchina virtuale potrebbe essere stata eliminata da Azure o che gli endpoint non sono configurati correttamente, il motivo è che, anche se la configurazione di destinazione master viene rilevata dagli endpoint di Azure quando la destinazione master viene distribuita in Azure, questo non vale per un server di destinazione master locale. Dato che non ci saranno conseguenze per il failback, è possibile ignorare questi errori.



## Passaggio 4: Proteggere le macchine virtuali nel sito locale

È necessario proteggere le VM nel sito locale prima di eseguire il failback.

### Prima di iniziare

Quando viene eseguito il failover di una macchina virtuale in Azure, viene aggiunge un'unità temp extra per file di paging. Si tratta di un'unità aggiuntiva che non è in genere necessaria per le macchine virtuali di cui è stato effettuato il failover poiché dispone già di un'unità dedicata per il file di paging. Prima di avviare una protezione inversa delle macchine virtuali, è necessario assicurarsi che questa unità sia offline in modo che non venga protetta. Procedere come segue:

1.  Aprire Gestione computer e selezionare Gestione archiviazione in modo che vengano elencati i dischi online collegati al computer.
2.  Selezionare il disco temporaneo collegato alla macchina e scegliere di attivare la modalità offline.

### Proteggere le VM

1. Nel portale di Azure controllare gli stati della macchina virtuale per assicurarsi che ne venga eseguito il failover.

	![](./media/site-recovery-failback-azure-to-vmware/image21.png)

2. Avviare vContinuum sul computer.

	![](./media/site-recovery-failback-azure-to-vmware/image8.png)

3. Fare clic su **New Protection** e selezionare il tipo di sistema operativo.

4.  Nella nuova finestra aperta selezionare **OS type** > **Get Details** per le VM di cui si vuole eseguire il failback. In **Primary server details** identificare e selezionare le macchine virtuali da proteggere. Le VM sono elencate sotto il nome host vCenter in cui si trovavano prima del failover.
5.  Quando si seleziona una macchina virtuale per la protezione (che ha già eseguito il failover in Azure), una finestra popup fornisce due voci relative alla macchina virtuale. Ciò accade perché il server di configurazione rileva due istanze delle macchine virtuali registrate. È necessario rimuovere la voce per la macchina virtuale locale in modo che sia possibile proteggere la macchina virtuale corretta. Per identificare qui la voce relativa alla macchina virtuale di Azure, è possibile accedere alla macchina virtuale di Azure e passare a C:\\Programmi (x86)\\Microsoft Azure Site Recovery\\Application Data\\etc. Nel file drscout.conf identificare l'ID host. Nella finestra di dialogo vContinuum mantenere la voce per cui è stato trovato l'ID host nella VM. Eliminare tutte le altre voci. Per selezionare la VM corretta, è possibile fare riferimento al relativo indirizzo IP. L'intervallo locale degli indirizzi IP corrisponderà alla VM locale.

	![](./media/site-recovery-failback-azure-to-vmware/image22.png)

	![](./media/site-recovery-failback-azure-to-vmware/image23.png)

6. Nel server vCenter arrestare la macchina virtuale. È anche possibile eliminare le VM in locale.
7. Specificare quindi il server di destinazione master locale in cui si vuole proteggere le VM. A tale scopo, connettersi al server vCenter nel quale eseguire il failback.

	![](./media/site-recovery-failback-azure-to-vmware/image24.png)

8. Selezionare il server di destinazione master basato sull'host in cui si vuole ripristinare le VM.

	![](./media/site-recovery-failback-azure-to-vmware/image24.png)

9. Fornire l'opzione di replica per ognuna delle macchine virtuali. È necessario selezionare l'archivio dati lato ripristino in cui le VM verranno ripristinate. La tabella riepiloga le diverse opzioni che è necessario specificare per ogni VM.

	![](./media/site-recovery-failback-azure-to-vmware/image25.png)

	**Opzione** | **Opzione consigliata**
	---|---
	Process server IP address | Selezionare il server di elaborazione distribuito in Azure
	Dimensioni di memorizzazione in MB| 
	Valore di memorizzazione | 1
	Giorni o ore | Days
	Consistency interval | 1
	Select target datastore | Archivio dati disponibile nel sito di ripristino. L'archivio di dati deve avere spazio sufficiente ed essere disponibile per l'host ESX in cui si vuole ripristinare la macchina virtuale.


10. Configurare le proprietà che verranno acquisite dalla macchina virtuale dopo avere eseguito il failover nel sito locale. Le proprietà sono riepilogate nella tabella seguente.

	![](./media/site-recovery-failback-azure-to-vmware/image26.png)

	**Proprietà** | **Dettagli**
	---|---
	Network configuration| Selezionare ogni scheda di rete rilevata e fare clic su **Change** per configurare l'indirizzo IP di failback per la macchina virtuale. 
	Configurazione hardware| Specificare la CPU e la memoria per la VM. Le impostazioni possono essere applicate a tutte le VM che si cerca di proteggere. Per identificare i valori corretti per la CPU e la memoria, è possibile fare riferimento alla dimensione del ruolo delle VM IAAS e visualizzare il numero di core e la memoria assegnata.
	Nome visualizzato | Dopo il failback in locale, è possibile rinominare le macchine virtuali quando verranno visualizzate nell'inventario vCenter. Il nome predefinito corrisponde al nome host della macchina virtuale. Per identificare il nome della macchina virtuale, è possibile fare riferimento all'elenco di macchine virtuali del gruppo protezione dati.
	Configurazione NAT | Illustrato in dettaglio di seguito

	![](./media/site-recovery-failback-azure-to-vmware/image27.png)

#### Configurare le impostazioni NAT

1. Per abilitare la protezione delle macchine virtuali, è necessario stabilire due canali di comunicazione. Il primo canale è tra la macchina virtuale e il server di elaborazione. Questo canale raccoglie i dati dalla VM e li invia al server di elaborazione che quindi invia i dati al server di destinazione master. Se il server di elaborazione e la macchina virtuale da proteggere sono nella stessa rete virtuale di Azure, non è necessario usare le impostazioni NAT. In caso contrario, specificare le impostazioni NAT. Visualizzare l'indirizzo IP pubblico del server di elaborazione in Azure.

	![](./media/site-recovery-failback-azure-to-vmware/image28.png)

2. Il secondo canale è tra il server di elaborazione e il server di destinazione master. L'opzione per usare NAT o meno dipende dal fatto che si usi una connessione basata su VPN o si comunichi via Internet. Non selezionare NAT se si usa una VPN, ma solo se si usa una connessione Internet.

	![](./media/site-recovery-failback-azure-to-vmware/image29.png)

	![](./media/site-recovery-failback-azure-to-vmware/image30.png)

3. Se non sono state eliminate le macchine virtuali in locale come specificato e se l'archivio dati di cui si sta eseguendo il failback contiene ancora i file VMDK precedenti, sarà necessario assicurarsi che la VM di failback venga creata in una nuova posizione. A questo scopo, selezionare le impostazioni **Advanced** e specificare una cartella alternativa per il ripristino in **Folder Name Settings**. Lasciare invariate le impostazioni predefinite delle altre opzioni. Applicare le impostazioni del nome di cartella a tutti i server.

	![](./media/site-recovery-failback-azure-to-vmware/image31.png)

4. Eseguire un controllo dello stato di preparazione per assicurarsi che le macchine virtuali siano pronte per essere protette in locale.

	![](./media/site-recovery-failback-azure-to-vmware/image32.png)


5. Attendere il completamento. Se l'operazione è riuscita su tutte le VM, è possibile specificare un nome per il piano di protezione. Fare quindi clic su **Protect** per iniziare.

	![](./media/site-recovery-failback-azure-to-vmware/image33.png)


6. È possibile monitorare l'avanzamento in vContinuum.

	![](./media/site-recovery-failback-azure-to-vmware/image34.png)

7. Al termine del passaggio **Activating Protection Plan**, è possibile monitorare la protezione delle VM nel portale di Site Recovery.

	![](./media/site-recovery-failback-azure-to-vmware/image35.png)

8. Per visualizzare lo stato esatto, fare clic sulla VM e monitorarne l'avanzamento.

	![](./media/site-recovery-failback-azure-to-vmware/image36.png)

## Preparare il piano di failback

È possibile preparare un piano di failback usando vContinuum in modo che sia possibile eseguire il failback dell'applicazione nel sito locale in qualsiasi momento. Questi piani di ripristino sono molto simili a quelli di Site Recovery.

1.  Avviare vContinuum e selezionare **Manage plans** > **Recover**. È possibile visualizzare un elenco di tutti i piani usati per il failover delle VM. È possibile usare gli stessi piani per eseguire il ripristino.

	![](./media/site-recovery-failback-azure-to-vmware/image37.png)

2. Selezionare il piano di protezione e tutte le VM in cui si vuole eseguire il ripristino. Selezionando ogni VM, è possibile visualizzare altri dettagli, inclusi il server ESX di destinazione e il disco della VM di origine. Fare clic su **Next** per iniziare la procedura guidata Recover Wizard e selezionare le VM da ripristinare.

	![](./media/site-recovery-failback-azure-to-vmware/image38.png)

3. Esistono più opzioni per eseguire il ripristino, ma è consigliabile usare **Latest Tag** e selezionare **Apply for All VMs** per assicurarsi che vengano usati i dati più recenti della macchina virtuale.
4. Eseguire il **controllo di conformità.** Verrà controllato se sono stati configurati i parametri corretti per abilitare il ripristino delle VM. Fare clic su **Next** se tutti i controlli hanno esito positivo. In caso contrario, controllare il log e correggere gli errori.

	![](./media/site-recovery-failback-azure-to-vmware/image39.png)

8.  In **VM Configuration** assicurarsi che le impostazioni di ripristino siano impostate correttamente. È possibile modificare le impostazioni delle VM, se necessario.

	![](./media/site-recovery-failback-azure-to-vmware/image40.png)

9. Esaminare l'elenco di macchine virtuali che verranno ripristinate e specificare un ordine di ripristino. Si noti che le VM vengono elencate usando il nome host del computer. Potrebbe essere difficile eseguire il mapping del nome host del computer alla macchina virtuale. Per eseguire il mapping dei nomi, andare al **Dashboard** delle macchine virtuali in Azure e controllare il nome host delle VM.

	![](./media/site-recovery-failback-azure-to-vmware/image41.png)

10. Specificare un nome per il piano e selezionare **Recover later**. È consigliabile eseguire il ripristino più tardi perché è possibile che la protezione iniziale non sia completa.
11. Fare clic su **Recover** per salvare il piano o attivare il ripristino se si è scelto di eseguire il ripristino subito e non più tardi. È possibile controllare lo stato del ripristino per verificare se il piano è stato salvato.

	![](./media/site-recovery-failback-azure-to-vmware/image42.png)

	![](./media/site-recovery-failback-azure-to-vmware/image43.png)

## Ripristinare le macchine virtuali

Dopo che il piano è stato creato, è possibile ripristinare le macchine virtuali. Prima di procedere, controllare che le macchine virtuali abbiano completato la sincronizzazione. Se lo stato di replica è OK, significa che la protezione è stata completata e la soglia RPO è stata soddisfatta. È possibile verificare l'integrità delle proprietà delle VM.

![](./media/site-recovery-failback-azure-to-vmware/image44.png)

Disattivare le macchine virtuali di Azure prima di avviare il ripristino, per evitare lo split brain e assicurarsi che gli utenti accedano solo a una copia dell'applicazione.


1.  Avviare il piano salvato. In vContinuum selezionare **Monitor** per monitorare i piani. Verranno elencati tutti i piani eseguiti.

	![](./media/site-recovery-failback-azure-to-vmware/image45.png)

2.  Selezionare il piano in **Recovery** e fare clic su **Start**. È possibile monitorare il ripristino. Dopo che le VM sono state attivate, è possibile connettersi alle VM in vCenter.

	![](./media/site-recovery-failback-azure-to-vmware/image46.png)

## Proteggere di nuovo in Azure dopo il failback

Dopo avere completato il failback, è opportuno proteggere ancora le macchine virtuali. Procedere come segue:

1.  Verificare che le macchine virtuali in locale funzionino e che le applicazioni siano raggiungibili.
2.  Nel portale di Azure Site Recovery selezionare le macchine virtuali ed eliminarle. Selezionare questa opzione per disabilitare la protezione delle macchine virtuali. Ciò garantisce che le VM non siano più protette.
3.  In Azure eliminare le macchine virtuali di Azure di cui è stato eseguito il failover.
4.  Eliminare la vecchia macchina virtuale in vSphere. Si tratta di VM di cui in precedenza è stato eseguito il failover in Azure.
5.  Nel portale di Site Recovery proteggere le VM di cui è stato eseguito il failover di recente. Una volta che sono protette, è possibile aggiungerle a un piano di ripristino.
 
## Passaggi successivi



- [Informazioni](site-recovery-vmware-to-azure-classic.md) sulla replica di server fisici e macchine virtuali VMware in Azure tramite la distribuzione avanzata.

 

<!---HONumber=AcomDC_0629_2016-->