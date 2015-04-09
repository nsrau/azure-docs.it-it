<properties 
	pageTitle="Configurare la protezione tra macchine virtuali VMWare o server fisici locali e Azure" 
	description="Azure Site Recovery coordina la replica, il failover e il ripristino in Azure di macchine virtuali residenti su server VMWare locali e tra server fisici locali e Azure." 
	services="site-recovery" 
	documentationCenter="" 
	authors="raynew" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/26/2015" 
	ms.author="raynew"/>


# Configurare la protezione tra macchine virtuali VMWare o server fisici locali e Azure

Azure Site Recovery favorisce l'attuazione della strategia di continuità aziendale e ripristino di emergenza orchestrando le operazioni di replica, failover e ripristino delle macchine virtuali e dei server fisici. Per informazioni sugli scenari di distribuzione possibili, vedere l'articolo [Panoramica di Azure Site Recovery](hyper-v-recovery-manager-overview.md).

Questa procedura dettagliata descrive come distribuire Site Recovery per:

- **Proteggere le macchine virtuali VMWare locali in Azure**.
- **Protegger i server fisici Windows e Linux locali in Azure**

I vantaggi aziendali includono:

- Protezione di server fisici Windows o Linux.
- Replica, failover e ripristino in modo semplice tramite il portale di Azure Site Recovery.
- Replica dei dati su Internet, una connessione VPN da sito a sito oppure tramite Azure ExpressRoute.   
- Failback (ripristino) da Azure a un'infrastruttura VMWare locale. 
- Individuazione semplificata di macchine virtuali VMWare.
- Coerenza di più macchine virtuali, in modo che macchine virtuali e server fisici che eseguono carichi di lavoro specifici possano essere ripristinati insieme a un punto dati coerente.
- Piani di ripristino per il failover semplificato e il ripristino di carichi di lavoro suddivisi in livelli su più computer.

Questa funzionalità è attualmente in anteprima. Vedere le [condizioni per l'utilizzo supplementari per le anteprime](preview-supplemental-terms).

## Informazioni sulla guida

Questa guida include una panoramica e i prerequisiti per la distribuzione. Descrive in dettaglio come configurare tutti i componenti della distribuzione e come abilitare la protezione per le macchine virtuali e i server. Si conclude con il test del failover, per accertarsi che tutti gli elementi funzionino come previsto.

Nel caso di problemi, inviare domande al [forum sui Servizi di ripristino di Azure](http://go.microsoft.com/fwlink/?LinkId=313628).

## Informazioni generali

Questo diagramma illustra i componenti della distribuzione.

![New vault](./media/site-recovery-vmware-to-azure/ASRVMWare_Arch.png)

### Componenti della distribuzione

- **Computer locali**: nel sito locale sono disponibili computer da proteggere. Si tratta di macchine virtuali in esecuzione su un hypervisor VMWare oppure server fisici che eseguono Windows o Linux.

- **Server di elaborazione locali**: i computer protetti inviano dati di replica al server di elaborazione locale. Il server di elaborazione esegue un certo numero di azioni sui dati e li ottimizza prima di inviarli al server di destinazione master in Azure. Dispone di una cache basata su disco per la memorizzazione dei dati di replica ricevuti. Gestisce anche l'installazione push del servizio Mobility, che deve essere installato in ogni macchina virtuale o server fisico da proteggere, ed esegue l'individuazione automatica dei server VMWare vCenter. Il server di elaborazione è un server virtuale o fisico che esegue Windows Server 2012 R2. È consigliabile collocarlo nella stessa rete e nello stesso segmento di LAN in cui risiedono i computer da proteggere, ma è possibile eseguirlo in una rete diversa, a condizione che i computer protetti possano avere la visibilità di rete L3 per tale server. Durante la distribuzione si configurerà il server di elaborazione e lo si registrerà nel server di configurazione.

- **Insieme di credenziali di Azure Site Recovery**: l'insieme di credenziali coordina e orchestra la replica, il failover e il ripristino dei dati tra il sito locale e Azure.

- **Server di configurazione di Azure**: il server di configurazione coordina la comunicazione tra i computer protetti, il server di elaborazione e i server di destinazione master in Azure. Configura la replica e coordina il ripristino in Azure quando si verifica il failover. Il server di configurazione viene eseguito in una macchina virtuale A3 standard di Azure nella sottoscrizione di Azure. Durante la distribuzione si configurerà il server e lo si registrerà nell'insieme di credenziali di Azure Site Recovery.

- **Server di destinazione master**: il server di destinazione master in Azure contiene i dati replicati dai computer protetti tramite VHD collegati e creati nell'archivio BLOB dell'account di archiviazione di Azure. Viene distribuito come macchina virtuale di Azure, come server Windows basato su un'immagine della raccolta di Windows Server 2012 R2 (per proteggere i computer Windows) o come server Linux basato su un'immagine della raccolta di OpenLogic CentOS 6.6 (per proteggere i computer Linux). Per le dimensioni sono disponibili due opzioni: standard A3 e standard D14. Il server è connesso alla stessa rete di Azure in cui risiede il server di configurazione. Durante la distribuzione si creerà il server e lo si registrerà nel server di configurazione.

- **Servizio Mobility**: il servizio Mobility deve essere installato in ogni macchina virtuale VMWare o server fisico Windows/Linux da proteggere. Il servizio invia i dati di replica al server di elaborazione, che a sua volta li invia al server di destinazione master in Azure. Il server di elaborazione può installare automaticamente il servizio Mobility nei computer protetti oppure è possibile distribuire manualmente il servizio usando il processo di distribuzione software interno.

- **Comunicazione dati e canale di replica**: sono disponibili due opzioni. Si noti che nessuna delle due richiede l'apertura delle porte di rete in ingresso sui computer protetti. Tutte le comunicazioni di rete vengono avviate dal sito locale.
	- **Tramite Internet**: comunica e replica i dati dai server locali protetti e Azure tramite una connessione Internet pubblica e sicura. Questa è l'opzione predefinita.
	- **VPN/ExpressRoute**: comunica e replica i dati tra i server lovali e Azure tramite una connessione VPN. Sarà necessario configurare una VPN da sito a sito o una connessione [ExpressRoute]expressroute) tra il sito locale e la rete di Azure. 

 
## Pianificazione della capacità

- Per ottenere prestazioni ottimali e sfruttare la funzionalità di coerenza tra più macchine virtuali, che ripristina un punto dati coerente per più computer protetti, è consigliabile raccogliere le macchine virtuali in gruppi di protezione in base al carico di lavoro.
- Non è possibile proteggere un singolo computer in più server di destinazione master. Infatti, quando i dischi vengono replicati, nell'archivio BLOB di Azure viene creato un VHD che rispecchia le dimensioni del disco e che viene collegato come disco dati al server di destinazione master. Naturalmente è possibile proteggere più computer con un singolo server di destinazione master.
- La macchina virtuale nel server di destinazione target può essere di tipo A4 o D14 standard di Azure:
	- Con un disco A4 standard è possibile aggiungere 16 dischi dati (con un massimo di 1023 GB per disco dati) a ogni macchina virtuale.
	- Con un disco D14 standard è possibile aggiungere 32 dischi dati (con un massimo di 1023 GB per disco dati) a ogni macchina virtuale.
- Si noti che un disco collegato al server di destinazione master viene riservato come unità di conservazione. Azure Site Recovery permette di definire gli intervalli di conservazione e ripristinare i computer protetti in qualsiasi momento all'interno di tale intervallo. L'unità di conservazione mantiene un journal delle modifiche del disco per la durata degli intervalli.  In questo modo si riduce il numero massimo dei dischi a 15 per A4 e a 31 per D14. 
- Per ridimensionare la distribuzione, aggiungere server di elaborazione e server di destinazione master. Se in un server di destinazione master esistente non vi sono dischi disponibili sufficienti, distribuire un secondo server di destinazione master. Se la frequenza di modifica dei dati dei computer protetti supera la capacità di un server di elaborazione esistente, è consigliabile distribuirne uno aggiuntivo. Si noti che i server di elaborazione e i server di destinazione master non richiedono il mapping uno-a-uno. È possibile distribuire il primo server di elaborazione con il secondo server di destinazione master e così via.
Il server di elaborazione usa una cache basata su disco. Assicurarsi che su C:/ sia disponibile spazio sufficiente per la cache. La frequenza di modifica dei dati dei computer protetti influirà sulle dimensioni della cache. In genere, per una directory cache sono consigliabili 600 GB per distribuzioni di medie dimensioni, ma è possibile usare le linee guida seguenti.

	![Cache guidelines](./media/site-recovery-vmware-to-azure/ASRVMWare_ProcessServerSize.png)


## Prima di iniziare

### Prerequisiti di Azure

- È necessario un account [Microsoft Azure](http://azure.microsoft.com/). È possibile iniziare con una [versione di valutazione gratuita](http://aka.ms/try-azure). 
- Per archiviare i dati replicati, sarà necessario un account di archiviazione di Azure. Nell'account deve essere abilitata la replica geografica. Dovrà trovarsi nella stessa area dell'insieme di credenziali di Azure Site Recovery ed essere associato alla stessa sottoscrizione. Per altre informazioni, vedere [Introduzione ad Archiviazione di Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=398704).
- Sarà necessaria una rete virtuale di Azure in cui saranno distribuiti il server di configurazione e il server di destinazione master. Dovrà trovarsi nella stessa sottoscrizione e nella stessa area dell'insieme di credenziali di Azure Site Recovery.
- Accertarsi di avere risorse di Azure sufficienti per distribuire tutti i componenti. Per altre informazioni, vedere l'articolo relativo ai [limiti della sottoscrizione di Azure](azure-subscription-service-limits).
- Verificare che i computer da proteggere soddisfino i requisiti delle macchine virtuali di Azure. 

	- **Numero di dischi**: in un singolo server protetto è possibile supportare un massimo di 31 dischi.
	- **Dimensioni del disco**: la capacità dei singoli dischi non deve superare 1023 GB.
	- **Clustering**: i server di cluster non sono supportati.
	- **Avvio**: l'avvio UEFI (Unified Extensible Firmware Interface)/EFI (Extensible Firmware Interface) non è supportato.
	- **Volumi**: i volumi crittografati con Bitlocker non sono supportati.
	- **Nomi dei server**: i nomi devono contenere da 1 a 63 caratteri (lettere, numeri e trattini). Il nome deve iniziare e terminare con una lettera o un numero. Dopo aver protetto un computer, è possibile modificare il nome di Azure.	

	Per altre informazioni sui requisiti di Azure, vedere [Supporto di macchine virtuali](https://msdn.microsoft.com/library/azure/dn469078.aspx#BKMK_E2A).

### Prerequisiti dei componenti dello scenario

- Server di elaborazione:
	- È possibile distribuire il server di elaborazione in macchine virtuali o computer fisici che eseguono Windows Server 2012 R2 con gli aggiornamenti più recenti. Eseguire l'installazione in C:/.
	- Il server richiede almeno 8 core processore, 64 GB di RAM e 300 GB di spazio disponibile consigliato su C:.
	- Collocare il server nella stessa rete e subnet dei computer da proteggere.
	- Installare VMware vSphere CLI 5.1 nel server per permettere l'individuazione automatica dei server VMWare vCenter.
- Il percorso di installazione per il server di configurazione, il server di destinazione master, il server di elaborazione e i server di failback deve essere specificato solo con caratteri dell'alfabeto latino. Ad esempio, il percorso per un server di destinazione master che esegue Linux dovrà essere **/usr/local/ASR**.

### Prerequisiti di VMWare

- Server VMWare vCenter per la gestione degli hypervisor VMware vSphere. Dovrà eseguire vCenter versione 5.0 o successiva con gli aggiornamenti più recenti. 
- Uno o più hypervisor vSphere contenenti le macchine virtuali VMWare da proteggere. L'hypervisor dovrà eseguire ESX/ESXi versione 5.0 o successiva con gli aggiornamenti più recenti.
- Nelle macchine virtuali VMWare individuate tramite un server vCenter dovranno essere installati e in esecuzione gli strumenti VMware.

### Prerequisiti dei computer Windows protetti

Per i server fisici o le macchine virtuali VMWare protette che eseguono Windows dovranno essere disponibili:

- Sistema operativo a 64 bit supportato, quale Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2 con SP1 o successivo.
- Nome host, punti di montaggio, nomi dispositivo, percorso di sistema di Windows (ad esempio, C:\Windows) dovranno essere specificati solo con caratteri dell'alfabeto latino.
- Il sistema operativo dovrà essere installato nell'unità C:\.
- Sono supportate le normali opzioni di archiviazione per i server Windows.
- Le regole del firewall nei computer protetti devono consentire a tali computer di raggiungere i server di configurazione e di destinazione master in Azure. 
- Dopo il failover, per connettersi alle macchine virtuali Windows in Azure con Desktop remoto, accertarsi che per il computer locale sia abilitato Desktop remoto. Se non si effettuano connessioni tramite VPN, le regole del firewall dovranno permettere l'esecuzione di connessioni Desktop remoto tramite Internet.

### Prerequisiti dei computer Linux protetti

Per i server fisici o le macchine virtuali VMWare protette che eseguono Linux dovranno essere disponibili:

- Sistema operativo supportato, quale Centos 6.4, 6.5, 6.6 (32 o 64 bit), Oracle Enterprise Linux 6.4, 6.5 (32 o 64 bit) che esegue il kernel compatibile con Red Hat o Unbreakable Enterprise Kern versione 3 (UEK3), SUSE Linux Enterprise Server 11 SP3 (32 o 64 bit)
- Nome host, punti di montaggio, nomi dispositivo e percorsi di sistema di Linux e nomi file (ad esempio /etc/, /usr) dovranno essere specificati solo con caratteri dell'alfabeto latino.
- La protezione può essere abilitata per i computer locali con le risorse di archiviazione indicate di seguito.
	- File system: EXT3, ETX4, ReiserFS, XFS
	- Software con percorsi multipli: Device Mapper - con percorsi multipli
	- Archiviazione volumi: LVM2
	- I server fisici con archiviazione del controller HP CCISS non sono supportati.
- Le regole del firewall nei computer protetti devono consentire a tali computer di raggiungere i server di configurazione e di destinazione master in Azure.
- Per connettersi a una macchina virtuale di Azure che esegue Linux dopo il failover usando un client Secure Shell (SSH), accertarsi che il servizio Secure Shell nel computer protetto sia impostato per l'avvio automatico all'avvio del sistema e che le regole del firewall permettano una connessione SSH a tale computer.  

### Prerequisiti di terze parti

Alcuni componenti della distribuzione in questo scenario dipendono da software di terze parti per funzionare correttamente. Per un elenco completo, vedere <a href="#thirdparty">Informazioni e comunicazioni sul software di terze parti</a>. 

## Passaggio 1: Creare un insieme di credenziali

1. Accedere al [portale di gestione](https://portal.azure.com).


2. Espandere **Servizi dati** > **Servizi di ripristino** e fare clic su **Insieme di credenziali di Site Recovery**.


3. Fare clic su **Crea nuovo** > **Creazione rapida**.
	
4. In **Nome** immettere un nome descrittivo per identificare l'insieme di credenziali.

5. In **Area** selezionare l'area geografica per l'insieme di credenziali. Per verificare le aree geografiche supportate, vedere la sezione Disponibilità a livello geografico nell'articolo [Prezzi di Azure Site Recovery](href="http://go.microsoft.com/fwlink/?LinkId=389880).

6. Fare clic su **Crea insieme di credenziali**. 

	![New vault](./media/site-recovery-vmware-to-azure/ASRVMWare_CreateVault.png)

Controllare la barra di stato per verificare che l'insieme di credenziali sia stato creato correttamente. L'insieme di credenziali verrà elencato come **Attivo** nella pagina principale di **Servizi di ripristino**.

## Passaggio 2: Distribuire un server di configurazione


1. Nella pagina **Servizi di ripristino** fare clic sull'insieme di credenziali per aprire la pagina Avvio rapido. La pagina Avvio rapido può anche essere aperta in qualsiasi momento tramite l'icona.

	![Quick Start Icon](./media/site-recovery-vmware-to-azure/ASRVMWare_QuickStartIcon.png)

2. Nell'elenco a discesa selezionare **Tra un sito locale con server VMware/fisici e Azure**.
3. In **Preparare le risorse (Azure) di destinazione** fare clic su **Distribuisci server di configurazione**.

	![Deploy configuration server](./media/site-recovery-vmware-to-azure/ASRVMWare_DeployCS.png)

4. Specificare i dettagli del server di configurazione e le credenziali per connettersi al server. Selezionare la rete di Azure in cui dovrà essere collocato il server. Specificare l'indirizzo IP interno e la subnet da assegnare al server. Quando si fa clic su **OK**, nella sottoscrizione del server di configurazione verrà creata una macchina virtuale A3 standard basata su un'immagine della raccolta di Windows Server 2012 R2 con Azure Site Recovery. Viene creata come prima istanza in un nuovo servizio cloud con un indirizzo IP pubblico riservato.

	![Configuration server settings](./media/site-recovery-vmware-to-azure/ASRVMWare_CSDetails.png)

5. Lo stato di avanzamento può essere monitorato nella scheda **Processi**.

	![Monitor progress](./media/site-recovery-vmware-to-azure/ASRVMWare_MonitorConfigServer.png)

8.  Dopo la distribuzione del server di configurazione, annotare l'indirizzo IP pubblico assegnato al server nella pagina **Macchine virtuali** nel portale di Azure. Nella scheda **Endpoint** annotare quindi la porta HTTPS pubblica mappata alla porta privata 443. Queste informazioni saranno necessarie in seguito per la registrazione dei server di destinazione master e di elaborazione con il server di configurazione. Il server di configurazione viene distribuito con i 4 endpoint pubblici indicati di seguito.

	- 443: canale HTTPS usato per coordinare la comunicazione tra i server dei componenti e Azure.
	- 9443: usato per lo strumento e la comunicazione di failback.
	- Desktop remoto
	- PowerShell


## Passaggio 3: Registrare il server di configurazione nell'insieme di credenziali

1. In **Preparare le risorse (Azure) di destinazione** fare clic su **Scaricare una chiave di registrazione**. Il file di chiave viene generato automaticamente. È valido 5 giorni dopo essere stato generato. Copiare il file nel server di configurazione.
2. Nella pagina **Dashboard** della macchina virtuale, fare clic su **Connetti**. Per accedere al server di configurazione con Desktop remoto, usare il file RDP scaricato.  Quando si accede per la prima volta, viene eseguita automaticamente l'installazione e registrazione guidata di Azure Site Recovery.

	![Registration](./media/site-recovery-vmware-to-azure/ASRVMWareRegister1.png)

3. Seguire le istruzioni della procedura guidata. Sarà necessario scaricare e installare MySQL Server e specificare le relative credenziali. Nella pagina di **registrazione di Azure Site Recovery** passare al file di chiave copiato nel server.

	![Registration key](./media/site-recovery-vmware-to-azure/ASRVMWareRegister2.png)

4. Al termine della registrazione verrà generata una passphrase. Copiarla in una posizione sicura. Sarà necessaria per autenticare e registrare i server di elaborazione e di destinazione master con il server di configurazione. Verrà usata anche per assicurare l'integrità del canale nelle comunicazioni del server di configurazione. La passphrase può essere rigenerata, ma si dovrà registrare di nuovo i server di elaborazione e di destinazione master con la nuova passphrase.

	![Passphrase](./media/site-recovery-vmware-to-azure/ASRVMWareRegister2.png)

Dopo la registrazione, il server di configurazione verrà elencato nella pagina **Server di configurazione** nell'insieme di credenziali.

## Passaggio 4: Configurare una connessione VPN
 
È possibile connettersi al server di configurazione tramite Internet o usando una connessione VPN o ExpressRoute. Una connessione Internet usa gli endpoint della macchina virtuale insieme all'indirizzo IP virtuale pubblico del server. VPN usa l'indirizzo IP interno del server insieme alle porte private dell'endpoint.
 
È possibile configurare una connessione VPN al server come indicato di seguito:

1. Se non è configurata una connessione da sito a sito o Azure ExpressRoute, per altre informazioni vedere:

	- [Blog relativo alla scelta tra connessione ExpressRoute e VPN](http://azure.microsoft.com/blog/2014/06/10/expressroute-or-virtual-network-vpn-whats-right-for-me/) 
	- [Configurare una connessione da sito a sito a una macchina virtuale di Azure](https://msdn.microsoft.com/library/azure/dn133795.aspx)
	- [Configurare una connessione ExpressRoute](https://msdn.microsoft.com/library/azure/dn606306.aspx) 
	
2. Nell'insieme di credenziali fare clic su **Server** > **Server di configurazione** > Server di configurazione > **Configura**.
3. In **Impostazioni di connettività** impostare **Tipo di connettività** su **VPN**. Se è configurata una VPN e nessun accesso Internet dal sito locale, accertarsi di selezionare l'opzione VPN. In caso contrario, il server di elaborazione non sarà in grado di inviare dati di replica al server di destinazione master nei relativi endpoint pubblici.

	![Enable VPN](./media/site-recovery-vmware-to-azure/ASRVMWare_EnableVPN.png)

## Passaggio 5: Distribuire il server di destinazione master

1. In **Preparare le risorse (Azure) di destinazione** fare clic su **Distribuisci server di destinazione master**.
2. Specificare i dettagli e le credenziali del server di destinazione master. Il server sarà distribuito nella stessa rete di Azure in cui risiede il server di configurazione con il quale è stata effettuata la registrazione. Quando si fa clic per confermare, verrà creata una macchina virtuale di Azure con un'immagine della raccolta di Windows o Linux. 

	![Target server settings](./media/site-recovery-vmware-to-azure/ASRVMWare_TSDetails.png)

3. Viene creata una macchina virtuale del server master di Windows con gli endpoint TCP pubblici indicati di seguito.

	- Personalizzato: la porta pubblica viene usata per inviare dati di replica tramite Internet. La porta privata 9443 viene usata dal server di elaborazione per inviare dati al server di destinazione master tramite VPN.
	- Personalizzato1: la porta privata 9080 viene usata dal server di elaborazione per inviare dati al server di destinazione tramite VPN.
	- PowerShell: porta privata 5986
	- Desktop remoto: porta privata 3389

4. Viene creata una macchina virtuale nel server master Linux con gli endpoint indicati di seguito.

	- Personalizzato: la porta pubblica viene usata per inviare dati di replica tramite Internet. La porta privata 9443 viene usata dal server di elaborazione per inviare dati al server di destinazione master tramite VPN.
	- Personalizzato1: la porta privata 9080 viene usata dal server di elaborazione per inviare dati al server di destinazione master tramite VPN.
	- SSH: porta privata 22

5. In **Macchine virtuali** attendere l'avvio della macchina virtuale. 

	- Se il server è stato configurato con Windows, annotare i dettagli relativi a Desktop remoto.
	- Se è stato configurato con Linux e si stabilisce la connessione tramite VPN, annotare l'indirizzo IP interno della macchina virtuale. Se si stabilisce la connessione tramite Internet, annotare l'indirizzo IP pubblico.
6.  Accedere al server per completare l'installazione ed eseguirne la registrazione con il server di configurazione. Se si usa Windows:

	1. Avviare una connessione Desktop remoto alla macchina virtuale. La prima volta che si accede, verrà eseguito uno script nella finestra di PowerShell. Non chiuderla. Al termine, viene aperto automaticamente lo strumento di configurazione dell'agente host (Host Agent Config) per registrare il server.
	2. Nella finestra **Host Agent Config** specificare l'indirizzo IP interno del server di configurazione e la porta 443. È possibile usare l'indirizzo interno e la porta privata 443 anche se non si usa la modalità VPN per la connessione, perché la macchina virtuale è collegata alla stessa rete di Azure in cui risiede il server di configurazione. Lasciare abilitata l'opzione **Usa HTTPS**. Immettere la passphrase per il server di configurazione annotata in precedenza. Fare clic su **OK** per registrare il server. È possibile ignorare le opzioni NAT presenti nella pagina, in quanto non vengono usate.

	![Windows master target server](./media/site-recovery-vmware-to-azure/ASRVMWare_TSRegister.png)

6. Se si usa Linux:
	1. Copiare il [file TAR del programma di installazione del server](http://go.microsoft.com/fwlink/?LinkID=529757&clcid=0x409) nella macchina virtuale usando un client FTP sicuro. In alternativa, è possibile accedere e usare wget per scaricare il file dal collegamento disponibile nella pagina Avvio rapido. 
	2. Accedere al server con un client Secure Shell. Se si è connessi alla rete di Azure tramite VPN, usare l'indirizzo IP interno. In caso contrario, usare l'indirizzo IP esterno e l'endpoint pubblico SSH.
	3. Estrarre i file dal programma di installazione compresso con gzip eseguendo: **tar -xvzf Microsoft-ASR_UA_8.2.0.0_RHEL6-64***."   

		![Linux master target server](./media/site-recovery-vmware-to-azure/ASRVMWare_TSLinuxTar.png)

	4. Accertarsi di essere nella directory nella quale è stato estratto il contenuto del file TAR ed eseguire il comando "**sudo ./install.sh**". Selezionare l'opzione **2. Master Target**. Lasciare invariate le impostazioni predefinite delle altre opzioni.

		![Register target server](./media/site-recovery-vmware-to-azure/ASRVMWare_TSLinux.png)

	5. Al termine dell'installazione, viene visualizzata la riga di comando **Host Config Interface**. Non ridimensionare la finestra. 
	6. Usare i tasti di direzione per selezionare **Global** e premere INVIO.
	7. In **Enter the IP address** immettere l'indirizzo interno del server di configurazione e la porta 22.
	8.  Specificare la passphrase del server di configurazione annotata in precedenza e premere INVIO. Selezionare **Quit** per completare l'installazione. Se si usa il client PuTTY per l'accesso SSH alla macchina virtuale, è possibile usare MAIUSC+INS per incollare. 
	9.  Usare il tasto freccia DESTRA per uscire e premere INVIO.

		![Master target server settings](./media/site-recovery-vmware-to-azure/ASRVMWare_TSLinux2.png)

7. Attendere 5 minuti (da 5 a 10), quindi nella pagina **Server** > **Server di configurazione** verificare che il server di destinazione master sia elencato come registrato nella scheda **Dettagli server**. Se si usa Linux e il server non è stato registrato, eseguire di nuovo lo strumento di configurazione dell'host da /usr/local/ASR/Vx/bin/hostconfigcli. Sarà necessario impostare le autorizzazioni di accesso eseguendo chmod come utente con privilegi avanzati.

	![Verify target server](./media/site-recovery-vmware-to-azure/ASRVMWare_TSList.png)

## Passaggio 6: Distribuire il server di elaborazione locale

1. Fare clic su Avvio rapido > **Installare il server di elaborazione locale** > **Scaricare e installare il server di elaborazione**.

	![Install process server](./media/site-recovery-vmware-to-azure/ASRVMWare_PSDeploy.png)

2. Copiare il file ZIP scaricato nel server in cui si installerà il server di elaborazione. Il file ZIP contiene due file di installazione:

	- Microsoft-ASR_CX_TP_8.2.0.0_Windows*
	- Microsoft-ASR_CX_8.2.0.0_Windows*

3. Decomprimere il file compresso e copiare i file di installazione in un percorso del server.
4. Eseguire il file di installazione **Microsoft-ASR_CX_TP_8.2.0.0_Windows*** e seguire le istruzioni. Verranno installati i componenti di terze parti necessari per la distribuzione.
5. Eseguire quindi **Microsoft-ASR_CX_8.2.0.0_Windows***.
6. Nella pagina **Modalità server** selezionare **Server di elaborazione**.
7.	In **Dettagli del server di configurazione**, se si stabilisce la connessione tramite VPN, specificare l'indirizzo IP interno del server di configurazione e la porta 443. In caso contrario, specificare l'indirizzo IP virtuale e l'endpoint HTTP pubblico mappato.
8.	Per disabilitare la verifica quando si usa il push automatico per installare il servizio, deselezionare **Verifica firma del software del servizio Mobility**. La verifica della firma richiede la connettività Internet dal server di elaborazione.
9.	Digitare la passphrase del server di configurazione.

	![Register configuration server](./media/site-recovery-vmware-to-azure/ASRVMWare_CSRegister.png)

8. Completare l'installazione del server. Tenere presente che sarà necessario installare VMware vSphere CLI 5.1 nel server per individuare i server vCenter.

	![Register process server](./media/site-recovery-vmware-to-azure/ASRVMWare_PSRegister2.png)

Verificare che il server di elaborazione sia stato registrato correttamente nell'insieme di credenziali > **Server di configurazione** > **Dettagli server**.

![Validate process server](./media/site-recovery-vmware-to-azure/ASRVMWare_ProcessServerRegister.png)

Se la verifica della firma per il servizio Mobility non è stata disabilitata al momento della registrazione del server di elaborazione, è possibile farlo in un secondo momento come descritto di seguito:

1. Accedere al server di elaborazione come amministratore e aprire il file C:\pushinstallsvc\pushinstaller.conf per la modifica. Nella sezione **[PushInstaller.transport]** aggiungere la riga seguente: **SignatureVerificationChecks="0"**. Salvare e chiudere il file.
1. Riavviare il servizio InMage PushInstall.


## Passaggio 7: Aggiungere i server vCenter

1. Nella scheda **Server** > **Server di configurazione** selezionare il server di configurazione e fare clic per aggiungere un server vCenter.

	![Select vCenter server](./media/site-recovery-vmware-to-azure/ASRVMWare_AddVCenter.png)

2. Specificare i dettagli del server vCenter e selezionare il server di elaborazione da usare per individuarlo.  È necessario che il server di elaborazione si trovi nella stessa rete del server vCenter e che sia installato VMware vSphere CLI 5.1.
3. Dopo aver completato l'individuazione, il server vCenter sarà elencato nel dettagli del server di configurazione.
	
	![vCenter server settings](./media/site-recovery-vmware-to-azure/ASRVMWare_AddVCenter2.png)


## Passaggio 8: Creare un gruppo di protezione

1. Aprire **Elementi protetti** > **Gruppo di protezione** e fare clic per aggiungere un gruppo di protezione.

	![Create protection group](./media/site-recovery-vmware-to-azure/ASRVMWare_CreatePG1.png)

2. Nella pagina **Specificare le impostazioni del gruppo di protezione** immettere un nome per il gruppo e selezionare il server di configurazione in cui creare il gruppo.

	![Protection group settings](./media/site-recovery-vmware-to-azure/ASRVMWare_CreatePG2.png)

3. Nella pagina **Specifica le impostazioni di replica** configurare le impostazioni di replica da usare per tutti i computer nel gruppo.   

	![Protection group replication](./media/site-recovery-vmware-to-azure/ASRVMWare_CreatePG3.png)

4. Impostazioni:
	- **Coerenza di più macchine virtuali**: se si attiva questa opzione, vengono creati punti di ripristino coerenti con l'applicazione condivisi tra i computer nel gruppo di protezione. Questa impostazione è importante quando tutti i computer nel gruppo di protezione eseguono lo stesso carico di lavoro. Tutti i computer saranno ripristinati nello stesso punto dati. Disponibile solo per i server Windows. 
	- **Soglia RPO**: se il valore RPO della replica per la protezione dati continua supera la soglia RPO configurata, vengono generati avvisi.
	- **Conservazione del punto di ripristino**: specifica l'intervallo di conservazione. I computer protetti possono essere ripristinati in qualsiasi punto all'interno di questo intervallo.
	- **Frequenza di snapshot coerenti con l'applicazione**: specifica con quale frequenza verranno creati i punti di ripristino contenenti snapshot coerenti con l'applicazione.

È possibile monitorare il gruppo di protezione in quanto la creazione avviene nella pagina **Elementi protetti**. 

## Passaggio 9: Effettuare il push del servizio Mobility

Quando si aggiungono computer a un gruppo di protezione, il server di elaborazione effettua automaticamente il push e l'installazione del servizio Mobility in ogni computer. Per usare questo meccanismo di push automatico per i computer protetti che eseguono Windows, è necessario eseguire le operazioni seguenti in ogni computer:

1. Configurare Windows Firewall per abilitare **Condivisione file e stampanti** e **Strumentazione gestione Windows**. Per i computer appartenenti a un dominio, è possibile configurare i criteri del firewall con un oggetto Criteri di gruppo.
2. L'account usato per eseguire l'installazione push deve appartenere al gruppo Administrators nel computer da proteggere. Queste credenziali vengono usate solo per l'installazione push. Non vengono in alcun modo archiviate dal servizio Mobility e vengono rimosse dopo la protezione del server. Si dovranno fornire queste credenziali quando si aggiunge un computer al gruppo di protezione.

	![Mobility credentials](./media/site-recovery-vmware-to-azure/ASRVMWare_PushCredentials.png)

3. Se l'account amministratore non è un account di dominio, si dovrà disabilitare il Controllo dell'accesso utente remoto nel computer locale. A questo scopo, in HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System creare la voce LocalAccountTokenFilterPolicy, se non esiste, e assegnare un valore DWORD 

Per proteggere i computer che eseguono Linux, eseguire le operazioni seguenti:

1. Verificare che l'account sia un utente ROOT nel server Linux di origine.
1. Installare i pacchetti openssh, openssh-server, openssl più recenti nel computer da proteggere.
1. Abilitare la porta SSH 22.
2. Abilitare il sottosistema Sftp e l'autenticazione della password nel file di configurazione SSHD:
	1. Accedere con l'account utente ROOT.
	2. Nel file /etc/ssh/sshd_config trovare la riga che inizia con "PasswordAuthentication". Rimuovere il commento dalla riga e modificare il valore da "no" in "yes". 

		![Linux mobility](./media/site-recovery-vmware-to-azure/ASRVMWare_LinuxPushMobility1.png)

	4. Trovare la riga che inizia con "Subsystem" e rimuovere il commento. 

		![Linux push mobility](./media/site-recovery-vmware-to-azure/ASRVMWare_LinuxPushMobility1.png)

## Passaggio 10: Aggiungere computer a un gruppo di protezione

1. Aprire la scheda **Elementi protetti** > **Gruppo di protezione** > **Computer** e aggiungere computer fisici o macchine virtuali gestite da un server vCenter individuato. I gruppi di protezione dovranno riflettere i carichi di lavoro, in modo da aggiungere allo stesso gruppo i computer che eseguono un'applicazione specifica.

	![Add machines](./media/site-recovery-vmware-to-azure/ASRVMWare_PushCredentials.png)

2. Nella pagina **Seleziona macchine virtuali** in **Aggiungi macchina virtuale** selezionare un server VCenter e quindi selezionare i relativi computer.

	![Add V-Center server](./media/site-recovery-vmware-to-azure/ASRVMWare_SelectVMs.png)

3. Quando si aggiungono computer a un gruppo di protezione, viene installato automaticamente il servizio Mobility dal server di elaborazione locale. Per il funzionamento del meccanismo di push automatico, accertarsi di avere configurato i computer protetti come descritto nel passaggio precedente.
4. In **Seleziona macchine virtuali** selezionare il server vCenter che gestisce i computer da proteggere e quindi selezionare le macchine virtuali.

4. Selezionare i server e le risorse di archiviazione da usare per la replica. 

	![vCenter server](./media/site-recovery-vmware-to-azure/ASRVMWare_MachinesResources.png)

5. Specificare le credenziali utente per il server di origine. Questo passaggio è necessario per installare automaticamente il servizio Mobility nei computer di origine. Per i server Windows è richiesto un account con privilegi di amministratore nel server di origine. Per Linux è richiesto un account utente con privilegi avanzati nel server.

	![Linux credentials](./media/site-recovery-vmware-to-azure/ASRVMWare_VMMobilityInstall.png)

6. Fare clic sul segno di spunta per completare l'aggiunta dei computer al gruppo di protezione e avviare la replica iniziale per ogni computer. Lo stato può essere monitorato nella pagina **Processi**.

	![Add V-Center server](./media/site-recovery-vmware-to-azure/ASRVMWare_PGJobs.png)

7. Fare inoltre clic su **Elementi protetti** > <gruppo di protezione> > **Macchine virtuali** per monitorare lo stato di protezione. Dopo il completamento della replica iniziale e la sincronizzazione dei dati nei computer, verrà visualizzato lo stato **Protetto**.

	![Virtual machine jobs](./media/site-recovery-vmware-to-azure/ASRVMWare_PGJobs.png)

## Passaggio 11: Impostare le proprietà dei computer protetti

1. Dopo che per un computer viene visualizzato lo stato **Protetto**, è possibile configurarne le proprietà di failover. Nei dettagli del gruppo di protezione selezionare il computer e aprire la scheda **Configura**.
2. È possibile modificare il nome assegnato al computer in Azure dopo il failover e le dimensioni di Azure. È anche possibile selezionare la rete di Azure alla quale verrà connesso il computer dopo il failover. Si noti che:

	- Il nome del computer in Azure deve rispettare i requisiti di Azure descritti nella sezione corrispondente.
	- Per impostazione predefinita, le macchine virtuali replicate in Azure non sono connesse a una rete di Azure. Per permettere la comunicazione delle macchine virtuali, accertarsi di impostare la stessa rete di Azure.

	![Set virtual machine properties](./media/site-recovery-vmware-to-azure/ASRVMWare_VMProperties.png)

## Passaggio 12: Eseguire un failover

1. Nella pagina **Piani di ripristino** aggiungere un piano di ripristino. Specificare i dettagli per il piano e selezionare **Azure** come destinazione.

	![Configure recovery plan](./media/site-recovery-vmware-to-azure/ASRVMWare_RP1.png)

2. In **Seleziona macchine virtuali** selezionare un gruppo di protezione e quindi i computer nel gruppo da aggiungere al piano di ripristino.

	![Add virtual machines](./media/site-recovery-vmware-to-azure/ASRVMWare_RP2.png)

3. Se necessario, è possibile personalizzare il piano per creare gruppi e una sequenza dell'ordine in cui verrà eseguito il failover dei computer nel piano di ripristino. È anche possibile aggiungere istruzioni per azioni manuali e script.

	![Customize recovery plan](./media/site-recovery-vmware-to-azure/ASRVMWare_RP2.png)

5. Nella pagina **Piani di ripristino** selezionare il piano e fare clic su **Failover di test**.
6. In **Conferma failover** verificare la direzione del failover (in Azure) e selezionare il punto di ripristino in cui eseguire il failover. 
7. Attendere il completamento del processo di failover e quindi verificare che sia stato eseguito nel modo previsto e che le macchine virtuali replicate vengano avviate in Azure.

<a name="thirdparty"></a><h2>INFORMAZIONI E COMUNICAZIONI SUL SOFTWARE DI TERZE PARTI</h2>

Do Not Translate or Localize

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, "Third Party Code").  Microsoft is the not original author of the Third Party Code.  The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below. Such licenses and information are provided for informational purposes only.  This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.  

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms. 

The complete file may be found on the [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=530254)  h. Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.


<!--HONumber=49-->