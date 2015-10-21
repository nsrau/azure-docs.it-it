<properties 
   pageTitle="Passaggi per il failback da Azure a VMware" 
   description="In questo articolo viene descritto come utilizzare lo strumento di ripristino del sito di Azure e vContinuum può essere utilizzato per il failback una macchina virtuale VMware." 
   services="site-recovery" 
   documentationCenter="" 
   authors="ruturaj" 
   manager="mkjain" 
   editor=""/>

<tags
   ms.service="site-recovery"
   ms.devlang="powershell"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.workload="required" 
   ms.date="10/07/2015"
   ms.author="ruturajd@microsoft.com"/>

# Passaggi per il failback da Azure a VMware

In questo documento verrà illustrata la procedura necessaria per eseguire per il failback da Azure al sito di VMware. È necessario aver già completato la procedura indicata in questa esercitazione relativa [alla protezione e al ripristino di VMware in Azure](site-recovery-vmware-to-azure.md).

Dopo un failover eseguito correttamente in Azure, le macchine virtuali saranno riportate nella relativa scheda. Se si decide di eseguire il failback, di seguito sono riportati i passaggi da seguire.

Si noti che quando si esegue il failback da Azure al sito di VMware, il ripristino può essere eseguito solo in una macchina virtuale. Anche se l'origine iniziale in VMware era un computer fisico, con un failover in Azure seguito da un failback in VMware il PC verrà convertito in una macchina virtuale.

## Panoramica

1.  Installare un server vContinuum in locale

    a. Configurarlo in modo che punti Server di configurazione

2.  Effettuare la distribuzione di un Server di elaborazione in Azure

3.  Installare un MT locale in locale

4.  Passaggi per proteggere il trasferimento nuovamente in locale delle macchine virtuali di cui è stato eseguito il failover

    a. Considerazioni sulla configurazione

5.  Monitoraggio della protezione delle macchine virtuali

6.  Eseguire nuovamente il failover delle macchine virtuali

Di seguito è riportata la panoramica dell'installazione che si otterrà nei passaggi seguenti. Parte dell'installazione è già stata completata durante il failover.

-   Le linee blu sono le connessioni utilizzate durante il failover.

-   Le linee rosse indicano le connessioni utilizzate durante il failback.

-   Le righe con frecce indicano la connessione a internet.

![](./media/site-recovery-failback-azure-to-vmware/vconports.png)

## Installare vContinuum in locale

Il programma di installazione di vContinuum sarà nel [percorso di download](http://go.microsoft.com/fwlink/?linkid=526305). Inoltre, installare la patch specificata in vContinuum disponibile al [percorso di download](http://go.microsoft.com/fwlink/?LinkID=533813) seguente.

1.  Avviare il programma di installazione per iniziare l'installazione di vContinuum. Fare clic su **Avanti**. ![](./media/site-recovery-failback-azure-to-vmware/image2.png)
2.  Specificare l'indirizzo IP e la porta del server CX. Selezionare HTTPS.

	![](./media/site-recovery-failback-azure-to-vmware/image3.png)

3.  Per individuare l'indirizzo IP CX andare alla distribuzione CS in Azure e visualizzare il relativo dashboard.

	![](./media/site-recovery-failback-azure-to-vmware/image4.png)

4.  Per individuare la porta pubblica CX, passare alla scheda endpoint nella pagina della VM e identificare la porta pubblica di endpoint HTTPs.

	![](./media/site-recovery-failback-azure-to-vmware/image5.png)

5.  Specificare la Passphrase del server di configurazione. È necessario avere preso nota della passphrase durante la registrazione del server di configurazione. È necessario utilizzare la passphrase durante le distribuzioni di MT e PS. Qualora non si ricordi la passphrase, è possibile passare al server CS Azure e individuare la passphrase archiviata nel percorso: C:\\Programmi (x86)\\InMage Systems\\private\\connection.passphrase

	![](./media/site-recovery-failback-azure-to-vmware/image6.png)

6.  Specificare il percorso per installare il server vContinuum e avviare l'installazione

	![](./media/site-recovery-failback-azure-to-vmware/image7.png)

7.  Una volta completata l'installazione, è possibile avviare vContinuum per verificarne il funzionamento. ![](./media/site-recovery-failback-azure-to-vmware/image8.png)


## Installare un Server di elaborazione in Azure

Un Server di elaborazione deve essere installato in Azure in modo che le macchine virtuali in Azure per inviare i dati al sistema MT. locale È necessario distribuire il Server di elaborazione in Azure nella stessa rete del server di configurazione.

1.  Nella pagina **Server di configurazione** in Azure, effettuare una selezione per aggiungere un nuovo server di elaborazione

	![](./media/site-recovery-failback-azure-to-vmware/image9.png)

2.  Specificare un nome per il server di elaborazione e immettere un nome e una password per connettersi alla macchina virtuale come amministratore. Selezionare il server di configurazione in cui si sta registrando il server di elaborazione. Questo deve essere lo stesso server utilizzato per la protezione e il failover delle macchine virtuali. Specificare la rete di Azure in cui distribuire il server di elaborazione. Deve essere la stessa rete del server di configurazione. Specificare un indirizzo IP univoco dalla subnet selezionata e avviare la distribuzione.

	![](./media/site-recovery-failback-azure-to-vmware/image10.png)


Verrà attivato un processo di distribuzione del server di elaborazione.

![](./media/site-recovery-failback-azure-to-vmware/image11.png)

Una volta che il server di elaborazione viene distribuito in Azure, è possibile accedere al server utilizzando le credenziali specificate. Utilizzare le stesse procedure da utilizzare durante l'inoltro direzione di protezione per registrare il PS.

![](./media/site-recovery-failback-azure-to-vmware/image12.png)

I server registrati durante il failback non saranno visibili nelle proprietà della VM. Saranno solo visibili nella scheda server a cui sono stati registrati sul server di configurazione. Possono essere necessari circa 10-15 minuti prima che PS venga inserito nell'elenco SC.

## Installare un server MT locale

A seconda delle macchine virtuali presenti sul lato di origine, sarà necessario installare un server Linux o un Windows Master Target in locale.

### Distribuire Windows MT

Un MT windows è già in dotazione con il programma di installazione di vContinuum. Quando si installa il vContinuum, un MT anche è distribuito nello stesso computer e registrato nel server di configurazione.

1.  Per iniziare la distribuzione, creare un oggetto vuoto del computer locale nell'host ESX in cui si desidera ripristinare le macchine virtuali di Azure.

2.  Assicurarsi che non vi siano almeno due dischi collegati alla macchina virtuale: uno viene utilizzato per il sistema operativo e il secondo viene utilizzato per l'unità di memorizzazione.

3.  Installare il sistema operativo.

4.  Installare vContinuum nel server. Questo sarebbe inoltre completare l'installazione del MT.

### Distribuzione Linux MT

1.  Per iniziare la distribuzione, creare un oggetto vuoto del computer locale nell'host ESX in cui si desidera ripristinare le macchine virtuali di Azure.

2.  Assicurarsi che non vi siano almeno due dischi collegati alla macchina virtuale: uno viene utilizzato per il sistema operativo e il secondo viene utilizzato per l'unità di memorizzazione.

3.  Installare il sistema operativo Linux. Nota: il sistema del server di destinazione master Linux (MT) non deve utilizzare LVM per il spazi di archiviazione radice o di conservazione. Linux MT è configurato per impostazione predefinita in modo da evitare l'individuazione di partizioni e dischi LVM.
4.  Le partizioni che è possibile creare sono

	![](./media/site-recovery-failback-azure-to-vmware/image13.png)

5.  Eseguire la procedura successiva all’installazione seguente prima di iniziare l'installazione di MT.


#### Procedure di successive all’installazione del sistema operativo

Per ottenere l'ID SCSI per ogni disco rigido SCSI in una macchina virtuale Linux, è necessario abilitare il parametro "disk.EnableUUID = TRUE ". Per attivare questo parametro, attenersi alla procedura seguente:

1. Arrestare la macchina virtuale.
2. Fare clic con il pulsante destro del mouse sulla voce della VM nel pannello sinistro, quindi selezionare l'opzione per **modificare le impostazioni.**
3. Scegliere la scheda **Options (Opzioni)**. Selezionare l’opzione **Advanced>General item (Avanzate>Elemento generale)** sulla sinistra e fare clic sui **parametri di configurazione** visualizzati a destra. L’opzione relativa ai "parametri di configurazione" è disattivata quando il computer è in esecuzione. Per rendere attiva la scheda, arrestare il computer.

	![](./media/site-recovery-failback-azure-to-vmware/image14.png)

4. Esiste già una riga con il valore **disk.EnableUUID**? Se è presente e se il valore è impostato su False, modificare il valore in True (valori True e False sono sensibili in maiuscole). Se il valore esiste ed è impostato su True, fare clic su Annulla e testare il comando SCSI all'interno del sistema operativo guest dopo il suo avvio. Se non esiste fare clic su **Add Row (Aggiungi riga).**
5. Aggiungere il disk.EnableUUID nella colonna del nome. Impostare il relativo valore come TRUE. Non aggiungere i valori precedenti insieme alle virgolette doppie.

	![](./media/site-recovery-failback-azure-to-vmware/image15.png)

#### Scaricare e installare i pacchetti aggiuntivi

Nota: assicurarsi che il sistema disponga della connettività Internet prima di scaricare e installare pacchetti aggiuntivi.

#yum install -y xfsprogs perl lsscsi rsync wget kexec-tools

Con il comando sopra verranno scaricati i 15 pacchetti menzionati di seguito dal repository di CentOS 6.6 e installarli.

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

Nota: se la macchina di origine utilizza il filesystem Reiser o XFS per il dispositivo di avvio o radice, i pacchetti seguenti devono essere scaricati e installati nel server master Linux di destinazione prima della protezione.

# cd /usr/local

# wget 
<http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm>

# wget 
<http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm>

# rpm -ivh kmod-reiserfs-0.0-1.el6.elrepo.x86\_64.rpm reiserfs-utils-3.6.21-1.el6.elrepo.x86\_64.rpm

# wget 
<http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_64.rpm>

# rpm -ivh xfsprogs-3.1.1-16.el6.x86\_64.rpm

#### Applicare le modifiche di configurazione personalizzate

Prima di applicare le modifiche di configurazione personalizzate assicurarsi di aver completato le operazioni successive all'installazione

Per applicare le modifiche di configurazione personalizzate, attenersi alla procedura seguente:

1. Copiare il file binario di RHEL 6-64 Unified Agent nel sistema operativo appena creato.

2. Eseguire il seguente comando per decomprimere il file binario.

** tar - zxvf < nome file >**

3. Eseguire il seguente comando per concedere l'autorizzazione.

# **chmod 755 ./ApplyCustomChanges.sh**

4. Eseguire il comando per eseguire lo script seguente.

**# ./ApplyCustomChanges.sh**

Nota: eseguire lo script solo una volta sul server. **Riavviare** il server dopo la corretta esecuzione dello script precedente.

### Iniziare l’installazione di MT

[Download](http://go.microsoft.com/fwlink/?LinkID=529757) il file di installazione del server Linux Master Target.

Copiare il programma di installazione del server Linux Master Target scaricato nella macchina virtuale Linux Master Target utilizzando un'utilità client FTP sicuro di propria scelta. In alternativa, è possibile accedere alla macchina virtuale Linux Master Target e utilizzare wget per scaricare il pacchetto di installazione dal collegamento specificato.

Accedere alla macchina virtuale del server Linux Master Target con un client ssh desiderato.

Se si è connessi alla rete di Azure in cui è stato distribuito il server Linux Master Target tramite una connessione VPN, quindi utilizzare che l'indirizzo IP interno del server Linux Master Target ottenuto dal dashboard della macchina virtuale e la porta 22 per connettersi al server Linux Master Target utilizzando Secure Shell.

Se ci si connette al server Master Target Server tramite una connessione Internet pubblica, utilizzare l’indirizzo IP virtuale pubblico del server Master Target (dalla pagina del dashboard delle macchine virtuali) e l'endpoint pubblico per consentire all’utilità ssh di accedere al server Master Target.

Estrarre i file dall'archivio tar compresso del programma di installazione del server Linux eseguendo:

*“tar –xvzf Microsoft-ASR\_UA\_8.2.0.0\_RHEL6-64*”* dalla directory in cui era stato copiato il programma di installazione del server di Linux Master Target.

![](./media/site-recovery-failback-azure-to-vmware/image16.png)

Se sono stati estratti i file di programma di installazione da una directory diversa, passare alla directory in cui viene archiviato il contenuto dell'archivio tar sono stati estratti. Da questo percorso di directory, eseguire "sudo. / install.sh"

![](./media/site-recovery-failback-azure-to-vmware/image17.png)

Quando viene richiesto di selezionare un ruolo principale di questo agente selezionare 2 (schema di destinazione)

Lasciare le altre opzioni di installazione interattiva sui rispettivi valori predefiniti.

Attendere il resto dell'installazione prima di proseguire l'installazione e l'interfaccia di configurazione host venga visualizzato. L'utilità di configurazione dell'host per il server di destinazione Linux Master Target è un'utilità della riga di comando. Non ridimensionare la finestra dell'utilità client ssh. Utilizzare i tasti di direzione per selezionare l'opzione globale e premere INVIO sulla tastiera.

![](./media/site-recovery-failback-azure-to-vmware/image18.png)

![](./media/site-recovery-failback-azure-to-vmware/image19.png)

1.  Nel campo IP immettere l'indirizzo IP interno del server di configurazione ottenuto dal dashboard delle macchine virtuali, quindi premere INVIO.

2.  Nel campo porta immettere 22 e premere INVIO.

3.  Lasciare Use https: su Yes. Premere INVIO ancora una volta.

4.  Immettere la passphrase che è stata generata nel server di configurazione. Se si utilizza il client PuTTY client da un computer Windows per comunicare tramite ssh con la macchina virtuale Linux Master Target, è possibile utilizzare MAIUSC+INS per incollare il contenuto degli Appunti. Copiare la passphrase locale negli Appunti utilizzando CTRL+C e utilizzare MAIUSC+INS per incollare il codice. Premere INVIO

5.  Utilizzare il tasto freccia destra per uscire e premere INVIO.

Attendere il completamento dell'installazione

![](./media/site-recovery-failback-azure-to-vmware/image20.png)

Se per qualche motivo non è stato possibile registrare il server Linux Master Target nel server di configurazione, è possibile risolvere il problema anche in questo caso eseguendo l'utilità di configurazione host /usr/local/ASR/Vx/bin/hostconfigcli (innanzitutto occorre impostare le autorizzazioni di accesso alla directory eseguendo chmod come utente con privilegi avanzati)


#### Convalidare la registrazione del server Master Target nel server di configurazione.

È possibile verificare che il server Master Target sia sta registrato correttamente con il Server di configurazione, visitando la pagina relativa ai dettagli del server nella pagina relativa al server di configurazione in un insieme di credenziali di Azure Site Recovery.

Nota: Dopo la registrazione di Mt, è possibile che in MT si sono verificati errori di configurazione con le possibili cause: la Macchina virtuale potrebbe essere stata eliminata da Azure o gli endpoint non sono configurati correttamente. Questo avviene perché la configurazione di MT viene rilevata da endpoint di Azure quando MT viene distribuito in Azure. Tuttavia ciò non ha valore true per MT locale e l'errore può essere ignorato. Il failback non avrò problemi relazionati a questo motivo.


## Iniziare a proteggere le macchine virtuali in locale

Prima di eseguire il failback delle VM locali, è necessario proteggere le macchine virtuali in locale. Attenersi alla procedura seguente per proteggere le macchine virtuali di un'applicazione.

### Nota sull'unità temp

Quando viene eseguito il failover di una macchina virtuale in Azure, viene aggiunge un'unità temp extra per file di paging. Si tratta di un'unità aggiuntiva che non è in genere necessaria per le macchine virtuali di cui è stato effettuato il failover poiché dispone già di un'unità dedicata per il file di paging.

Prima di avviare una protezione inversa delle macchine virtuali, è necessario assicurarsi che l'unità sia offline in modo che non venga protetto.

A tale scopo,

1.  Aprire Gestione Computer (tramite lo strumento amministrativo del Pannello di controllo o fare clic con il pulsante destro del mouse su questo computer nella finestra di Esplora risorse e selezionare Gestisci).

2.  Selezionare Gestione archiviazione in modo che vengano elencati i dischi online collegati al computer.

3.  Selezionare il disco temporaneo collegato alla macchina e scegliere di attivare la modalità offline.

4.  Dopo che è stata correttamente disattivata, sarà possibile procedere con la macchina virtuale nella direzione inversa di protezione.

### Piano di protezione per le macchine virtuali

Nel portale di Azure, osservare gli stati della macchina virtuale e assicurarsi che ne venga eseguito il failover.

![](./media/site-recovery-failback-azure-to-vmware/image21.png)

Nota: durante il failover da Azure all'ambiente locale, la macchina virtuale di Azure viene considerata simile a una macchina virtuale fisica. Il failover verso l'ambiente locale è verso una macchina virtuale.

1.  Avviare vContinuum sul computer

![](./media/site-recovery-failback-azure-to-vmware/image8.png)

2.  Nell'impostazione relativa alla **scelta dell'applicazione**, selezionare **P2V**

3.  Fare clic sui **nuovo gruppo protezione dati** opzione per iniziare

4.  Nella nuova finestra visualizzata si inizierà a proteggere le macchine virtuali verso l'ambiente locale.

    a. Selezionare il **tipo di sistema operativo** in base alle macchine virtuali di cui si desidera effettuare il failback e **ottenere i dettagli+**

    b. Nell'impostazione relativa ai **Dettagli del server primario**, è necessario identificare le macchine virtuali da proteggere+.

    c. Sono elencate le macchine virtuali per i nomi host dei computer e non sono visibili in vCenter o Azure

    d. Le macchine virtuali sono elencate sotto il nome host di vCenter in cui si trovavano le macchine virtuali prima del failover.+

    e. Dopo aver identificato le macchine virtuali che si desidera proteggere, selezionarne una alla volta.

5.  Quando si seleziona una macchina virtuale per la protezione (che ha già eseguito il failover in Azure) verrà visualizzata una finestra popup con due voci relative alla macchina virtuale. Ciò accade perché sono state rilevate due istanze di macchine virtuali registrate. È necessario rimuovere la voce per la macchina virtuale locale in modo che sia possibile proteggere la macchina virtuale corretta. Si noti che verranno visualizzate le voci dal relativo nome host del computer. Per identificare qui la voce relativa alla macchina virtuale di Azure, è possibile accedere alla macchina virtuale di Azure e passare a C:\\Programmi (x86)\\Microsoft Azure Site Recovery\\Application Data\\etc. Nel file drscout.conf identificare l'ID dell'host. Nella finestra di dialogo vContinuum mantenere la voce con cui l'ID host è stato trovato nella macchina virtuale. Eliminare tutte le altre voci.

![](./media/site-recovery-failback-azure-to-vmware/image22.png)

6.  Per selezionare la VM corretta, è possibile fare riferimento al relativo indirizzo IP. L'intervallo locale degli indirizzi IP corrisponderà alla VM locale.
7.  Fare clic su**Remove**per eliminare la voce.

![](./media/site-recovery-failback-azure-to-vmware/image23.png)

8.  Passare a vCenter e arrestare la macchina virtuale in vCenter
9.  In seguito, è possibile anche eliminare le macchine virtuali locali
10.  Successivamente è necessario specificare il server di MT locale di cui si desidera proteggere le macchine virtuali.
11.  A tale scopo, connettersi a vCenter nel quale eseguire il failback

![](./media/site-recovery-failback-azure-to-vmware/image24.png)

12.  Selezionare il server MT basato sull'host in cui si desidera ripristinare le macchine virtuali

![](./media/site-recovery-failback-azure-to-vmware/image24.png)

13.  Successivamente fornire l'opzione di replica per ognuna delle macchine virtuali

![](./media/site-recovery-failback-azure-to-vmware/image25.png)

14.  È necessario selezionare l'**archivio dati** lato ripristino (si tratta dell'archivio dati verso cui le macchine virtuali verranno ripristinate)

Sono le diverse opzioni che è necessario fornire per ogni macchina virtuale

**Opzione** | **Opzione consigliata**
---|---
IP del server di collaborazione | Selezionare il PS in cui è stato distribuito in Azure
Dimensioni di memorizzazione in MB| 
Valore di memorizzazione | 1
Giorni o ore | Days
Intervallo di coerenza | 1
Selezionare l'archivio dati di destinazione | L'archivio dati disponibile sul lato di ripristino. Questo archivio di dati deve disporre di spazio sufficiente e inoltre essere disponibile per l'host ESX in cui si desidera realizzare la macchina virtuale.
15.  Successivamente è possibile configurare le proprietà che verranno acquisite dalle macchine virtuali dopo aver eseguito il failover verso l'ambiente locale. È possibile configurare diverse proprietà nel modo seguente

![](./media/site-recovery-failback-azure-to-vmware/image26.png)


**Proprietà** | **Come configurare**
---|---
Configurazione di rete|Per ogni scheda di rete rilevata, configurare l'indirizzo IP di failback per la macchina virtuale. Selezionare la scheda di rete e fare clic sull'opzione **Modifica** per specificare i dettagli dell'indirizzo IP.
Configurazione hardware|È possibile specificare la CPU e i valori di memoria per la macchina virtuale. Questa impostazione può essere applicata a tutte le macchine virtuali che si desidera proteggere.
Nome visualizzato|Per identificare i valori corretti per la CPU e memoria, è possibile fare riferimento alla dimensione del ruolo di macchine virtuali IAAS e visualizzare il numero di core e memoria assegnata.
Nome visualizzato|Dopo il failover verso l'ambiente locale, è possibile rinominare le macchine virtuali, come verrà visualizzato nell'inventario di vCenter. Si noti che il valore predefinito visualizzato qui corrisponde al nome host della macchina virtuale. Per identificare il nome della macchina virtuale, è possibile fare riferimento all'elenco di macchine virtuali del gruppo protezione dati.
Configurazione NAT|Illustrato in dettaglio di seguito

![](./media/site-recovery-failback-azure-to-vmware/image27.png)



> ** Scelte le impostazioni di PS NAT**
>
> Per abilitare la protezione delle macchine virtuali, è necessario stabilire due canali di comunicazione.
>
> Il primo canale è tra la macchina virtuale e il Server di processo. Questo canale raccoglie i dati dalla macchina virtuale e lo invia il PS. PS invierà quindi questi dati alla destinazione principale. Se il server di processo e la macchina virtuale da proteggere siano nella stessa rete virtuale Azure, non è necessario utilizzare le impostazioni NAT. Se il PS e la macchina virtuale da proteggere è in due reti virtuali diverse, è necessario specificare le impostazioni NAT per PS e controllare la prima opzione.
>
> ![](./media/site-recovery-failback-azure-to-vmware/image28.png)
>
> Per identificare il server di processo IP pubblico, è possibile passare alla distribuzione in Azure PS e vedere il relativo indirizzo IP pubblico.
>
> Il secondo canale è tra il server di processo e lo schema di destinazione. L'opzione per utilizzare NAT o meno dipende da se si utilizza connessione VPN basata tra MT e PS o protezione su internet. Se PS comunica con il MT tramite una VPN, è consigliabile non selezionare questa opzione. Se la destinazione Master deve comunicare al server di processo su internet, quindi specificare le impostazioni NAT per il PS.
>
> ![](./media/site-recovery-failback-azure-to-vmware/image29.png)
>
> Per identificare il server di processo IP pubblico, è possibile passare alla distribuzione in Azure PS e vedere il relativo indirizzo IP pubblico.
>
> ![](./media/site-recovery-failback-azure-to-vmware/image30.png)

1.  Se non sono state eliminate le macchine virtuali in locale come specificato nel passaggio 5.d e, se l'archivio dati riescono a, come selezionato nel passaggio 7.a contiene ancora il vecchio file VMDK quindi è necessario anche verificare che il failback macchina virtuale viene creato in una nuova posizione. Ciò è possibile selezionare le impostazioni avanzate e specificare una cartella alternativa per ripristinare nel **le impostazioni relative ai nomi di cartella** sezione di impostazioni avanzate.

![](./media/site-recovery-failback-azure-to-vmware/image31.png)

Le altre opzioni nelle impostazioni avanzate possono essere lasciate come predefinito. Verificare che si applicano le impostazioni del nome di cartella a tutti i server.

2.  Spostare quindi la fase finale della protezione. Di seguito è necessario eseguire un controllo di conformità per assicurarsi che le macchine virtuali sono pronte per essere protetto al locale.

![](./media/site-recovery-failback-azure-to-vmware/image32.png)

	a.  Click on the readiness check and wait for it to complete.

	b.  The Readiness Report tab will give the information if all the
    virtual machines are ready.

	c.  If the Readiness Report is successful on all the virtual machines it
    will allow you to specify a name to the Protection plan

![](./media/site-recovery-failback-azure-to-vmware/image33.png)

	d.  Give the plan a new Name and begin Protect by clicking the button
    below.


3.  A questo punto inizia la protezione.

    a. È possibile visualizzare lo stato di avanzamento della protezione il vContinuum

![](./media/site-recovery-failback-azure-to-vmware/image34.png)

	b.  Once the step **Activating Protection Plan** is completed, you can
    monitor the protection of the virtual machines via the ASR Portal.

	c.  You can also monitor the protection via Azure Site Recovery.

![](./media/site-recovery-failback-azure-to-vmware/image35.png)

È inoltre possibile visualizzare lo stato esatto delle coppie facendo clic nella macchina virtuale e il monitoraggio dello stato di avanzamento nella sezione di replica del volume.

![](./media/site-recovery-failback-azure-to-vmware/image36.png)

## Preparazione del piano di failback

È possibile preparare un piano di failback utilizzando vContinuum in modo che l'applicazione possa eseguire il failover verso l'ambiente locale in qualsiasi momento. Questi piani di ripristino sono molto simili ai piani di ripristino ASR.

1.  Avviare il vContinuum e selezionare l'opzione per **gestire piani**.

2.  Selezionano le opzioni secondarie di **ripristino**

![](./media/site-recovery-failback-azure-to-vmware/image37.png)

3.  È possibile visualizzare l'elenco di tutti i piani che sono stati utilizzati per proteggere le macchine virtuali. Questi sono i piani stessi che è possibile utilizzare per eseguire il ripristino.

4.  Selezionare il piano di protezione e scegliere tutte le macchine virtuali che si desidera ripristinare.

    a. Sulla selezione di ogni macchina virtuale è possibile visualizzare ulteriori dettagli sull'origine macchina virtuale, server ESX di destinazione a cui verrà ripristinata la macchina virtuale e il disco di macchina virtuale di origine

5.  Fare clic su Avanti per iniziare il **ripristinare** guidata

6.  Selezionare le macchine virtuali che si desidera ripristinare

    a. Visualizzare l'elenco di tutte le macchine virtuali che è possibile ripristinare

![](./media/site-recovery-failback-azure-to-vmware/image38.png)

	b.  You can **recover based on** multiple options however we recommend
    the **Latest Tag.** This will ensure that the latest data from the
    virtual machine will be used.

	c.  Select **Apply for All VMs** to ensure that the latest tag will be
    chosen for all the virtual machines.


7.  Eseguire il **controllo di conformità.** Che informa se i parametri corretti sono configurati per consentire il ripristino di tag più recente della macchina virtuale. Se tutti i controlli sono riuscito else esaminare il log e risolvere gli errori, fare clic su Avanti.

![](./media/site-recovery-failback-azure-to-vmware/image39.png)

8.  Nel passaggio di configurazione della macchina virtuale della procedura guidata, assicurarsi che le impostazioni di ripristino siano impostate correttamente. Nel caso in cui le impostazioni della macchina virtuale sono diverse da quello desiderato, è possibile modificarle. Avendo già completato questa operazione durante la protezione, è possibile ignorare questo momento.

![](./media/site-recovery-failback-azure-to-vmware/image40.png)

9.  Infine, esaminare l'elenco di macchine virtuali che verranno ripristinati.

    a. Specificare un ordine di ripristino per le macchine virtuali.

Nota: le macchine virtuali sono elencate utilizzando il nome host del Computer. Potrebbe essere difficile eseguire il mapping al nome di computer host alla macchina virtuale. Per mappare i nomi, è possibile passare al dashboard di macchine virtuali in Azure IAAS e osservare il nome host della macchina virtuale.

![](./media/site-recovery-failback-azure-to-vmware/image41.png)

10.  Assegnare il **nome del piano di ripristino** e selezionare **ripristinare in seguito** in **Opzioni di ripristino.**

    a. Nel caso in cui si desidera ripristinare subito è possibile scegliere di **Ripristina adesso** nel **Opzioni di ripristino**.

    b. È consigliabile ripristinare successivamente poiché la replica iniziale di protezione non siano state completate.

    c. Infine fare clic sul **ripristinare** pulsante per salvare il piano o per attivare il ripristino basato sul **Opzioni di ripristino**.

11.  È possibile vedere lo stato di ripristino e se che il piano viene salvato.

![](./media/site-recovery-failback-azure-to-vmware/image42.png)

12.  Se si è scelto di ripristinare in seguito, si verrà informati che il piano viene creato e che è possibile ripristinare in un secondo momento.

![](./media/site-recovery-failback-azure-to-vmware/image43.png)

## Ripristino delle macchine virtuali

Dopo aver creato il piano, è possibile ripristinare le macchine virtuali. Come prerequisito, è necessario assicurarsi che le macchine virtuali hanno completato la sincronizzazione.

![](./media/site-recovery-failback-azure-to-vmware/image44.png)

Se lo stato di replica OK indica che la protezione viene completata e la soglia RPO è stata soddisfatta. Per verificare l'integrità della coppia di replica, è possibile visualizzare le proprietà della macchina virtuale e visualizzare l'integrità della replica.

**Disattivare le macchine virtuali di Azure prima di avviare il ripristino. In questo modo che non esiste alcun "split Brain" e verranno forniti ai clienti finali da una copia dell'applicazione. Solo quando correttamente dopo aver verificato che le macchine virtuali di Azure disattivazione di continuare a quanto riportato di seguito.**

Per iniziare a ripristinare che le macchine virtuali consente di eseguire in locale, è necessario avviare il piano viene salvato.

1.  Scegliere per il vContinuum **Monitor** i piani.

2.  La procedura guidata elencherà i piani che sono stati eseguiti fino ad ora.

![](./media/site-recovery-failback-azure-to-vmware/image45.png)

3.  Selezionare il **ripristino** nodo e selezionare il piano che si desidera ripristinare.

    a. Indica che il piano non ha ancora iniziato.

4.  Fare clic su **avviare** di avviare il ripristino.

5.  È possibile monitorare il ripristino delle macchine virtuali


![](./media/site-recovery-failback-azure-to-vmware/image46.png)

6. Una volta stato acceso le macchine virtuali, è possibile connettersi alle macchine virtuali sul vCenter.

## Proteggere di nuovo in Azure dopo il failback

Dopo aver completato il failback è possibile proteggere le macchine virtuali ancora una volta. Di seguito sarà possibile riconfigurare la protezione

1.  Verificare che funzionino con le macchine virtuali in locale e che l’applicazione sia in grado di raggiungere i clienti finali.

2.  Nel portale di Azure Site Recovery, selezionare le macchine virtuali ed eliminarle.

    a. Selezionare questa opzione per disabilitare la protezione delle macchine virtuali. Ciò garantisce che le macchine virtuali sono più protetti.

3.  Passare alle macchine virtuali IAAS di Azure ed eliminare la non riuscita su macchine virtuali.

4.  Eliminare le macchine virtuali precedenti in vSpehere: si tratta di macchine virtuali di cui in precedenza è stato eseguito il failover in Azure.

5.  Il ripristino automatico di sistema portale scegliere per proteggere le macchine virtuali recentemente eseguite il failover.

6.  Una volta protette, è possibile aggiungere le macchine virtuali a un piano di ripristino e garantirne una protezione costante.


 

<!----HONumber=Oct15_HO2-->