<properties
	pageTitle="Uso dell'estensione della VM Docker per Linux in Azure"
	description="Descrive Docker e le estensioni di Macchine virtuali di Azure e mostra come creare a livello di codice le macchine virtuali in Azure che siano host Docker dalla riga di comando usando l'interfaccia della riga di comando di Azure."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="08/29/2016"
	ms.author="rasquill"/>

# Uso dell’estensione della VM Docker dall’interfaccia della riga di comando di Azure (Azure CLI)

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]



Questo argomento descrive come creare una VM con l'estensione della VM Docker dalla modalità di gestione dei servizi (asm) nell'interfaccia della riga di comando di Azure su qualsiasi piattaforma. [Docker](https://www.docker.com/) è uno dei più popolari approcci alla virtualizzazione che usa [contenitori Linux](http://en.wikipedia.org/wiki/LXC) invece di macchine virtuali allo scopo di isolare i dati ed eseguire i calcoli su risorse condivise. È possibile usare l'estensione della macchina virtuale Docker per l'[Agente Linux di Azure](virtual-machines-linux-agent-user-guide.md) per creare una VM Docker che ospiti un numero qualsiasi di contenitori per le applicazioni su Azure. Per assistere a una discussione di alto livello sui contenitori e i relativi vantaggi, guardare questa [sessione con lavagna condivisa relativa a Docker](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).


##Come usare l'estensione della VM Docker con Azure
Per usare l'estensione della VM Docker con Azure è necessario installare una versione dell'[interfaccia della riga di comando di Azure](https://github.com/Azure/azure-sdk-tools-xplat) successiva alla versione 0.8.6. Al momento della stesura di questo articolo, la versione corrente è la 0.10.0. È possibile installare l’interfaccia della riga di comando di Azure su Mac, Linux e Windows.


Il processo completo di utilizzo di Docker su Azure è semplice:

+ Installare l’interfaccia della riga di comando di Azure e le relative dipendenze sul computer dal quale si intende controllare Azure (su Windows, si tratterà di una distribuzione Linux in esecuzione come macchina virtuale)
+ Usare i comandi di Docker per l’interfaccia della riga di comando di Azure per creare un host Docker della VM in Azure
+ Usare i comandi locali di Docker per gestire i contenitori Docker nella VM Docker in Azure.


### Installare l'interfaccia della riga di comando di Azure

Per installare e configurare l’interfaccia della riga di comando di Azure, vedere [Come installare e configurare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md). Per confermare l'installazione, digitare `azure` nel prompt dei comandi. Dopo pochi secondi verrà visualizzata la grafica ASCII dell’interfaccia della riga di comando di Azure, in cui sono elencati i comandi di base disponibili. Se l'installazione è andata a buon fine, sarà possibile digitare `azure help vm` e verificare che uno dei comandi elencati corrisponda a "docker".

> [AZURE.NOTE] Docker include strumenti per Windows, ovvero [Docker Machine](https://docs.docker.com/installation/windows/), che consentono anche di automatizzare la creazione di un client Docker da usare per gestire macchine virtuali di Azure come host Docker.

### Connettere l'interfaccia della riga di comando di Azure al proprio account Azure
Prima di poter usare l'interfaccia della riga di comando di Azure è necessario associare alla stessa le credenziali del proprio account Azure sulla propria piattaforma. La sezione [Come connettersi alla sottoscrizione di Azure](../xplat-cli-connect.md) spiega come scaricare e importare il file **.publishsettings** o associare l'interfaccia della riga di comando di Azure a un ID organizzazione.

> [AZURE.NOTE] Ci sono alcune differenze di comportamento quando si usa l'uno o l'altro metodo di autenticazione, perciò assicurarsi di leggere il documento sopra riportato per comprendere le diverse funzionalità.

### Installare Docker e usare l'estensione della VM Docker per Azure
Per installare Docker in locale sul proprio computer seguire le [istruzioni di installazione di Docker](https://docs.docker.com/installation/#installation).

Per usare Docker con una macchina virtuale di Azure, l'immagine Linux usata per la VM deve avere installato l'[agente VM Linux Azure](virtual-machines-linux-agent-user-guide.md). Al momento esistono solo due tipi di immagine che offrono queste funzionalità:

+ un'immagine Ubuntu dalla raccolta immagini di Azure o

+ un'immagine Linux personalizzata creata con l'agente VM Linux Azure installato e configurato. Per altre informazioni su come costruire una VM Linux con l'agente VM di Azure, vedere [Guida dell'utente dell'agente Linux di Azure](virtual-machines-linux-agent-user-guide.md).

### Uso della raccolta immagini di Azure

Da una sessione Bash o Terminal, usare il seguente comando dell’interfaccia della riga di comando di Azure per trovare l'immagine più recente di Ubuntu nella raccolta di VM da usare digitando

`azure vm image list | grep Ubuntu-14_04`

e selezionare uno dei nomi di immagine, ad esempio `b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-it-IT-30GB`, quindi usare il comando seguente per creare una nuova macchina virtuale usando tale immagine.

```
azure vm docker create -e 22 -l "West US" <vm-cloudservice name> "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-it-IT-30GB" <username> <password>
```

dove:

+ *&lt;vm-nome serviziocloud&gt;* è il nome della macchina virtuale che diventerà il computer host del contenitore Docker in Azure

+  *&lt;nomeutete&gt;* è il nome utente dell’utente root predefinito della macchina virtuale

+ *&lt;password&gt;* è la password dell’account *nomeutente* che soddisfa gli standard di complessità per Azure

> [AZURE.NOTE] Attualmente, una password deve contenere almeno 8 caratteri, un carattere minuscolo e uno maiuscolo, un numero e un carattere speciale, ad esempio uno dei seguenti: `!@#$%^&+=` Il punto alla fine della frase precedente NON è un carattere speciale.

Se il comando ha avuto esito positivo, verrà visualizzato un output simile al seguente, a seconda degli argomenti e delle opzioni precisi usati:

![](./media/virtual-machines-linux-classic-cli-use-docker/dockercreateresults.png)

> [AZURE.NOTE] La creazione di una macchina virtuale può richiedere alcuni minuti, ma dopo che ne è stato effettuato il provisioning (il valore dello stato è `ReadyRole`) viene avviato il daemon Docker ed è possibile connettersi all'host contenitore Docker.

Per testare la VM Docker creata in Azure, digitare

`docker --tls -H tcp://<vm-name-you-used>.cloudapp.net:2376 info`

dove *&lt;vm-name-you-used&gt;* è il nome della macchina virtuale usata nella chiamata a `azure vm docker create`. Viene visualizzato codice simile al seguente, che indica che la VM host di Docker è in fase di elaborazione in Azure ed è in attesa dei comandi dell'utente.

Ora è possibile provare a connettersi utilizzando il client docker per ottenere informazioni (in alcune configurazioni del client Docker, ad esempio nei Mac, è necessario utilizzare `sudo`):

	sudo docker --tls -H tcp://testsshasm.cloudapp.net:2376 info
	Password:
	Containers: 0
	Images: 0
	Storage Driver: devicemapper
	Pool Name: docker-8:1-131781-pool
	Pool Blocksize: 65.54 kB
	Backing Filesystem: extfs
	Data file: /dev/loop0
	Metadata file: /dev/loop1
	Data Space Used: 1.821 GB
	Data Space Total: 107.4 GB
	Data Space Available: 28 GB
	Metadata Space Used: 1.479 MB
	Metadata Space Total: 2.147 GB
	Metadata Space Available: 2.146 GB
	Udev Sync Supported: true
	Deferred Removal Enabled: false
	Data loop file: /var/lib/docker/devicemapper/devicemapper/data
	Metadata loop file: /var/lib/docker/devicemapper/devicemapper/metadata
	Library Version: 1.02.77 (2012-10-15)
	Execution Driver: native-0.2
	Logging Driver: json-file
	Kernel Version: 3.19.0-28-generic
	Operating System: Ubuntu 14.04.3 LTS
	CPUs: 1
	Total Memory: 1.637 GiB
	Name: testsshasm
	WARNING: No swap limit support

Per essere certi che tutto funzioni, è possibile esaminare la macchina virtuale per l'estensione Docker:

	azure vm extension get testsshasm
	info: Executing command vm extension get
	+ Getting virtual machines
	data: Publisher Extension name ReferenceName Version State
	data: -------------------- --------------- ------------------------- ------- ------
	data: Microsoft.Azure.E... DockerExtension DockerExtension 1.* Enable
	info: vm extension get command OK

### Autenticazione della VM host di Docker

Oltre a creare la VM di Docker, il comando `azure vm docker create` crea automaticamente anche i certificati necessari che consentono al computer client Docker di connettersi all'host contenitore di Azure con il protocollo HTTPS. I certificati saranno archiviati sulle macchine client e host, secondo le esigenze. Nei tentativi successivi, i certificati esistenti verranno riutilizzati e condivisi con il nuovo host.

Per impostazione predefinita, i certificati vengono inseriti in `~/.docker` e Docker verrà configurato per l'esecuzione sulla porta **2376**. Per scegliere una porta o una directory differente, usare una delle seguenti opzioni della riga di comando `azure vm docker create` per configurare la VM host del contenitore Docker in modo da usare una porta differente o certificati differenti per connettere i client:

```
-dp, --docker-port [port]              Port to use for docker [2376]
-dc, --docker-cert-dir [dir]           Directory containing docker certs [.docker/]
```

Il daemon Docker sull'host è configurato per restare in ascolto delle connessioni client e autenticarle sulla porta specificata usando i certificati generati dal comando `azure vm docker create`. La macchina client deve avere questi certificati per poter accedere all'host Docker.

> [AZURE.NOTE] Un host di rete in esecuzione senza questi certificati sarà vulnerabile a chiunque possa connettersi alla macchina. Prima di modificare la configurazione predefinita, assicurarsi di aver compreso i rischi a cui si sottopongono i computer e le applicazioni.

## Passaggi successivi

* È ora possibile passare alla [guida dell'utente di Docker] e usare la VM Docker. Per creare una VM abilitata per Docker incorporata nel nuovo portale, vedere l'articolo su [come usare l'estensione della VM Docker con il portale].

* L'estensione della VM Docker di Azure supporta anche Docker Compose, che usa un file YAML dichiarativo per eseguire un'applicazione modellata dallo sviluppatore in qualsiasi ambiente e generare una distribuzione coerente. Vedere [Introduzione a Docker e Compose per definire ed eseguire un'applicazione multi-contenitore in una macchina virtuale di Azure].

<!--Anchors-->
[Subheading 1]: #subheading-1
[Subheading 2]: #subheading-2
[Subheading 3]: #subheading-3
[Next steps]: #next-steps

[How to use the Docker VM Extension with Azure]: #How-to-use-the-Docker-VM-Extension-with-Azure
[Virtual Machine Extensions for Linux and Windows]: #Virtual-Machine-Extensions-For-Linux-and-Windows
[Container and Container Management Resources for Azure]: #Container-and-Container-Management-Resources-for-Azure



<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: ../web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md
[come usare l'estensione della VM Docker con il portale]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/

[guida dell'utente di Docker]: https://docs.docker.com/userguide/
 
[Introduzione a Docker e Compose per definire ed eseguire un'applicazione multi-contenitore in una macchina virtuale di Azure]: virtual-machines-linux-docker-compose-quickstart.md

<!---HONumber=AcomDC_0831_2016-->