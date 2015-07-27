<properties 
	pageTitle="Guida dell'utente dell'agente Linux per Azure" 
	description="Informazioni su come installare e configurare l'agente Linux (waagent) per gestire l'interazione della macchina virtuale con il controller di infrastruttura di Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="szarkos" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/07/2015" 
	ms.author="szark"/>



#Guida dell'utente dell'agente Linux di Azure

##Introduzione

L’agente Linux di Azure (/usr/sbin/waagent) gestisce l’interazione tra una macchina virtuale e il controller di infrastruttura di Azure. Effettua le seguenti operazioni:

* **Provisioning dell'immagine**
  - Crea un account utente
  - Configura tipi di autenticazione SSH
  - Distribuisce coppie di chiavi e chiavi pubbliche SSH
  - Imposta il nome host
  - Pubblica il nome host nel DNS della piattaforma
  - Segnala l'ID digitale della chiave dell'host SSH alla piattaforma
  - Gestisce il disco risorse
  - Esegue la formattazione e il montaggio del disco risorse
  - Configura l'area di swap
* **Rete**
  - Gestisce i percorsi per migliorare la compatibilità con i server DHCP della piattaforma.
  - Garantisce la stabilità del nome dell'interfaccia di rete
* **Kernel**
  - Configura la piattaforma virtuale NUMA
  - Usa l'entropia Hyper-V per /dev/random
  - Configura i timeout SCSI per il dispositivo radice (che può essere remoto)
* **Diagnostica**
  - Reindirizza la console alla porta seriale
* **Distribuzioni SCVMM**
    - Rileva e avvia l'agente VMM per Linux durante l'esecuzione in un ambiente System Center Virtual Machine Manager 2012 R2
* **Estensione VM**
    - Inserire il componente creato da Microsoft e partner in VM Linux (IaaS) per attivare il software e l'automazione di configurazione
    - Implementazione di riferimento di estensione VM in [https://github.com/Azure/azure-linux-extensions](https://github.com/Azure/azure-linux-extensions)


##Comunicazione

Il flusso di informazioni dalla piattaforma all'agente avviene tramite due canali:

* Un DVD collegato in fase di avvio per le distribuzioni IaaS. Nel DVD è incluso un file di configurazione conforme a OVF che include tutte le informazioni di provisioning diverse dalle coppie di chiavi SSH effettive.

* Un endpoint TCP che espone un'API REST usata per ottenere la configurazione della distribuzione e della topologia.

###Come ottenere l'agente Linux
È possibile ottenere l'agente Linux più recente direttamente da:

- [I vari provider di distribuzione che supportano Linux in Azure](http://support.microsoft.com/kb/2805216)
- o dal [repository open source Github per l'agente Linux di Azure](https://github.com/Azure/WALinuxAgent)


## Requisiti
I seguenti sistemi sono stati testati e funzionano con l'agente Linux di Azure. **Si noti che questo elenco può differire dall'elenco ufficiale dei sistemi supportati sulla piattaforma Microsoft Azure**, come descritto di seguito: [http://support.microsoft.com/kb/2805216](http://support.microsoft.com/kb/2805216)

###Distribuzioni Linux supportate

* CoreOS
* CentOS 6.2+
* Debian 7.0+
* Ubuntu 12.04+
* openSUSE 12.3+
* SLES 11 SP2+
* Oracle Linux 6.4+

Altri sistemi supportati:

* FreeBSD 9 + (agente Linux di Azure v2.0.10 +)


Per il corretto funzionamento dell'agente Linux sono necessari alcuni package di sistema:

* Python 2.6+
* Openssl 1.0+
* Openssh 5.3+
* Utilità file system: sfdisk, fdisk, mkfs, separate
* Strumenti password: chpasswd, sudo
* Strumenti di elaborazione testo: sed, grep
* Strumenti di rete: ip-route


##Installare

Il metodo preferito per l'installazione e l'aggiornamento dell'agente Linux di Azure prevede l'installazione tramite un pacchetto RPM o DEB dal repository di pacchetti della distribuzione.

Se si opta per l'installazione manuale, è necessario copiare lo script ’waagent’ in /usr/sbin/waagent e installarlo mediante l'esecuzione del comando seguente:

	# sudo chmod 755 /usr/sbin/waagent
	# sudo /usr/sbin/waagent -install -verbose

Il file di log dell'agente viene mantenuto in /var/log/waagent.log.


##Opzioni da riga di comando

###Flag

- verbose: aumenta il livello di dettaglio del comando specificato
- force: ignora la conferma interattiva per determinati comandi

###Comandi:

- help: elenca i flag e i comandi supportati.

- install: consente di installare manualmente l'agente
 * Verifica il sistema per le dipendenze necessarie

 * Crea lo script di inizializzazione SysV (/etc/init.d/waagent), il file di configurazione logrotate (/etc/logrotate.d/waagent) e configura l'immagine per l'esecuzione dello script di inizializzazione all'avvio

 * Scrive il file di configurazione di esempio in /etc/waagent.conf

 * Tutti i file di configurazione esistenti vengono spostati in /etc/waagent.conf.old

 * Rileva la versione del kernel e applica la soluzione alternativa VNUMA, se necessario

 * Sposta le regole udev che possono interferire con la rete (/lib/udev/rules.d/75-persistent-net-generator.rules, /etc/udev/rules.d/70-persistent-net.rules) in /var/lib/waagent/

- uninstall: rimuove waagent e i file associati
 * Annulla la registrazione dello script di inizializzazione dal sistema e lo elimina

 * Elimina la configurazione logrotate e il file di configurazione waagent in /etc/waagent.conf

 * Ripristina le eventuali regole undev spostate durante l'installazione

 * Il ripristino automatico della soluzione alternativa VNUMA non è supportato, pertanto è necessario modificare i file di configurazione GRUB manualmente per riabilitare NUMA, se necessario.

- deprovision: tenta di pulire il sistema per renderlo idoneo per un nuovo provisioning. Questa operazione comporta l'eliminazione di quanto segue:
 * Tutte le chiavi host (se Provisioning.RegenerateSshHostKeyPair è 'y' nel file di configurazione)

 * Configurazione NameServer in /etc/resolv.conf

 * Password radice da /etc/shadow (se Provisioning.DeleteRootPassword è 'y' nel file di configurazione)

 * Lease client DHCP memorizzati nella cache

 * Ripristina il nome host su localhost.localdomain

 **Avviso:** il deprovisioning non garantisce che dall'immagine vengano cancellate tutte le informazioni sensibili e che sia adatta per la ridistribuzione.

- deprovision+user: esegue tutte le operazioni in -deprovision (sopra) ed elimina l'ultimo account utente sottoposto a provisioning (ottenuto da /var/lib/waagent) e i dati associati. Questo parametro viene usato per il deprovisioning di un'immagine precedentemente sottoposta a provisioning in Azure in modo che possa essere acquisita e riutilizzata.

- version: visualizza la versione di waagent

- serialconsole: configura GRUB affinché contrassegni ttyS0 (la prima porta seriale) come console di avvio. Questo garantisce che i log di avvio del kernel vengano inviati alla porta seriale e resi disponibili per il debug.

- daemon: esegue waagent come daemon per gestire l'interazione con la piattaforma. Questo argomento è specificato per waagent nello script di inizializzazione di waagent.

##Configurazione

Un file di configurazione (/etc/waagent.conf) controlla le azioni dell'agente waagent. Di seguito è riportato un file di configurazione di esempio:
	
	#
	# Azure Linux Agent Configuration	
	#
	Role.StateConsumer=None 
	Role.ConfigurationConsumer=None 
	Role.TopologyConsumer=None
	Provisioning.Enabled=y
	Provisioning.DeleteRootPassword=n
	Provisioning.RegenerateSshHostKeyPair=y
	Provisioning.SshHostKeyPairType=rsa
	Provisioning.MonitorHostName=y
	ResourceDisk.Format=y
	ResourceDisk.Filesystem=ext4
	ResourceDisk.MountPoint=/mnt/resource 
	ResourceDisk.EnableSwap=n 
	ResourceDisk.SwapSizeMB=0
	LBProbeResponder=y
	Logs.Verbose=n
	OS.RootDeviceScsiTimeout=300
	OS.OpensslPath=None

Di seguito sono descritte le opzioni di configurazione disponibili in modo dettagliato. Le opzioni di configurazione sono di tre tipi: Boolean, String o Integer. Le opzioni di configurazione booleane possono essere specificate come "y" o "n". È possibile usare la parola chiave speciale "None" per alcune voci di configurazione di tipo stringa, come indicato di seguito.

**Role.StateConsumer:**

Tipo: String Predefinito: nessuno

Se è specificato un percorso a un programma eseguibile, viene richiamato dopo che waagent ha effettuato il provisioning dell'immagine e lo stato "Ready" è pronto per essere segnalato all'infrastruttura. L'argomento specificato al programma sarà "Ready". L'agente non attenderà la restituzione del programma prima di continuare.

**Role.ConfigurationConsumer:**

Tipo: String Predefinito: nessuno

Se è specificato un percorso a un programma eseguibile, il programma viene richiamato quando l'infrastruttura indica che è disponibile un file di configurazione per una macchina virtuale. Il percorso al file di configurazione XML è fornito come argomento all'eseguibile. Può essere richiamato più volte, ogni volta che il file di configurazione subisce modifiche. Nell'appendice viene fornito un file di esempio. Il percorso corrente di questo file è /var/lib/waagent/HostingEnvironmentConfig.xml.

**Role.TopologyConsumer:**

Tipo: String Predefinito: nessuno

Se è specificato un percorso a un programma eseguibile, il programma viene richiamato quando l'infrastruttura indica che è disponibile un nuovo layout di topologia di rete per la macchina virtuale. Il percorso al file di configurazione XML è fornito come argomento all'eseguibile. Può essere richiamato più volte ogni, ogni volta che la topologia di rete subisce modifiche, ad esempio a seguito della correzione del servizio. Nell'appendice viene fornito un file di esempio. Il percorso corrente di questo file è /var/lib/waagent/SharedConfig.xml.

**Provisioning.Enabled:**

Tipo: Boolean Predefinito: y

Consente all'utente di attivare o disattivare la funzionalità di provisioning nell'agente. I valori validi sono "y" o "n". Se il provisioning è disabilitato, le chiavi utente e host SSH nell'immagine vengono mantenute e qualsiasi configurazione specificata nell'API di provisioning di Azure viene ignorata.

	Note that this parameter defaults to "n" on Ubuntu Cloud Images that use cloud-init for provisioning.

**Provisioning.DeleteRootPassword:**

Tipo: Boolean Predefinito: n

Se questa voce è impostata, la password radice nel file /etc/shadow viene cancellata durante il processo di provisioning.

**Provisioning.RegenerateSshHostKeyPair:**

Tipo: Boolean Predefinito: y

Se questa voce è impostata, tutte le coppie di chiavi host SSH (ecdsa, dsa e rsa) vengono eliminate da /etc/ssh/ durante il processo di provisioning e viene generata un'unica coppia di chiavi aggiornata.

Il tipo di crittografia per la coppia di chiavi aggiornata è configurabile dalla voce Provisioning.SshHostKeyPairType. Si noti che alcune distribuzioni ricreeranno le coppie di chiavi SSH per qualsiasi tipo di crittografia mancante al riavvio del daemon SSH, ad esempio in caso di riavvio.

**Provisioning.SshHostKeyPairType:**

Tipo: String Predefinito: rsa

È possibile impostare questa voce su un tipo di algoritmo di crittografia supportato dal daemon SSH nella macchina virtuale. I valori supportati sono in genere "rsa", "dsa" e "ecdsa". Si noti che "putty.exe" in Windows non supporta il tipo "ecdsa". Se pertanto si intende usare putty.exe in Windows per connettersi a una distribuzione Linux, usare "rsa" o "dsa".

**Provisioning.MonitorHostName:**

Tipo: Boolean Predefinito: y

Se questa voce è impostata, waagent monitorerà la macchina virtuale Linux per rilevare modifiche del nome host (come restituite dal comando "hostname") e aggiornerà automaticamente la configurazione di rete nell'immagine per riflettere la modifica. Per effettuare il push della modifica del nome ai server DNS, la funzionalità di rete nella macchina virtuale verrà riavviata, causando una breve interruzione nella connettività Internet.

**ResourceDisk.Format:**

Tipo: Boolean Predefinito: y

Se questa voce è impostata, il disco risorse fornito dalla piattaforma verrà formattato e montato da waagent se il tipo di file system richiesto dall'utente in "ResourceDisk.Filesystem" è diverso da "ntfs". Una singola partizione di tipo Linux (83) verrà resa disponibile nel disco. Si noti che la partizione non verrà formattata se è possibile montarla correttamente.

**ResourceDisk.Filesystem:**

Tipo: String Predefinito: ext4

Specifica il tipo di file system per il disco risorse. I valori supportati variano in base alla distribuzione Linux. Se la stringa è X, è necessario che mkfs.X sia presente nell'immagine Linux. Le immagini SLES 11 in genere utilizzano il valore 'ext3'. Le immagini FreeBSD in questo caso devono usare il valore 'ufs2'.

**ResourceDisk.MountPoint:**

Tipo: String Predefinito: /mnt/resourse

Specifica il percorso in cui è montato il disco risorse. Si noti che il disco risorse è un disco *temporaneo* e potrebbe essere svuotato in seguito al deprovisioning della macchina virtuale.

**ResourceDisk.EnableSwap:**

Tipo: Boolean Predefinito: n

Se questa voce è impostata, viene creato un file di scambio (/swapfile) nel disco risorse e aggiunto all'area di swap del sistema.

**ResourceDisk.SwapSizeMB:**

Tipo: Integer Predefinito: 0

Dimensione del file di scambio in megabyte.

**LBProbeResponder:**

Tipo: Boolean Predefinito: y

Se questa voce è impostata, waagent risponderà ai probe di bilanciamento del carico dalla piattaforma (se presente).

**Logs.Verbose:**

Tipo: Boolean Predefinito: n

Se questa voce è impostata, viene incrementato il livello di dettaglio del log. Waagent registra in /var/log/waagent.log e usufruisce della funzionalità logrotate del sistema per la rotazione dei log.

**OS.RootDeviceScsiTimeout:**

Tipo: Integer Predefinito: 300

Consente di configurare il timeout SCSI in secondi nel disco del sistema operativo e nelle unità dati. Se questa voce non è impostata, vengono usate le impostazioni predefinite del sistema.

**OS.OpensslPath:**

Tipo: String Predefinito: nessuno

È possibile aggiungere questa voce per specificare un percorso alternativo per il file binario openssl da usare per le operazioni di crittografia.



##Immagini di Ubuntu Cloud

Si noti che le immagini di Ubuntu Cloud utilizzano [cloud-init](https://launchpad.net/ubuntu/+source/cloud-init) per eseguire molte attività di configurazione che verrebbero altrimenti gestite dall’agente Linux di Azure. Tenere presenti le differenze seguenti:


- Il valore predefinito di **Provisioning.Enabled** è "n" nelle immagini di Ubuntu Cloud che utilizzano cloud-init per eseguire attività di provisioning.

- I seguenti parametri di configurazione non hanno alcun effetto sulle immagini di Ubuntu Cloud che utilizzano cloud-init per gestire disco risorse e spazio di scambio:

 - **ResourceDisk.Format**
 - **ResourceDisk.Filesystem**
 - **ResourceDisk.MountPoint**
 - **ResourceDisk.EnableSwap**
 - **ResourceDisk.SwapSizeMB**

- Vedere le risorse seguenti per configurare il punto di montaggio del disco di risorsa e scambiare spazio nelle immagini di Ubuntu Cloud durante il provisioning:

 - [Ubuntu Wiki: Configurare partizioni di scambio](http://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409)
 - [Inserimento di dati personalizzati in una macchina virtuale di Azure](virtual-machines-how-to-inject-custom-data.md)

 

<!---HONumber=July15_HO3-->