<properties
   pageTitle="Come usare Docker Machine in Azure"
   description="Viene illustrato come avviare ed eseguire Docker Machine su Ubuntu in Azure."
   services="virtual-machines"
   documentationCenter="virtual-machines"
   authors="squillace"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="09/22/2015"
   ms.author="rasquill"/>

# Come usare Docker Machine in Azure

In questo argomento viene descritto come utilizzare [Docker](https://www.docker.com/) con [machine](https://github.com/docker/machine) e l’[interfaccia della riga di comando di Azure](https://github.com/Azure/azure-xplat-cli) per creare una macchina virtuale di Azure per gestire rapidamente e facilmente i contenitori Linux da una computer che esegue Ubuntu. A scopo dimostrativo, nell'esercitazione viene illustrato come distribuire sia l'[immagine busybox dell’hub Docker](https://registry.hub.docker.com/_/busybox/) sia l'[immagine nginx dell’hub Docker](https://registry.hub.docker.com/_/nginx/) e configurare il contenitore per instradare le richieste Web al contenitore nginx. Nella documentazione di Docker **machine** viene descritto come modificare queste istruzioni per altre piattaforme.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]In questo articolo viene illustrata la creazione di una risorsa con il modello di distribuzione classica.


Per completare questa esercitazione sono necessari alcuni prerequisiti. È necessario installare quanto segue:

1. [npm](https://docs.npmjs.com/) e [Node.js](http://nodejs.org/)
2. [L’interfaccia della riga di comando di Azure](https://github.com/Azure/azure-xplat-cli)
3. Un [client Docker](https://docs.docker.com/installation/)

Se gli elementi vengono installati in questo ordine, il computer Ubuntu sarà pronto per l'esercitazione. (Questa esercitazione è, in linea di massima, uguale per le altre distro basate su dpkg, ad esempio Debian. Indicare nei commenti se vengono individuati altri requisiti o passaggi).

## Ottenere Docker Machine o compilarlo

Il modo più rapido per procedere con **Docker Machine** consiste nello scaricare la versione appropriata direttamente dal [sito di condivisione delle versioni](https://github.com/docker/machine/releases). Sul computer client di questa esercitazione è in esecuzione Ubuntu in un computer x64, per cui viene usata l'immagine **docker-machine\_linux-amd64**.

È anche possibile compilare autonomamente la propria **Docker Machine** seguendo i passaggi per [contribuire a machine](https://github.com/docker/machine#contributing). Per eseguire la compilazione sarà necessario scaricare 1 GB o più, ma in questo modo sarà possibile personalizzare la propria esperienza nel modo desiderato.

> [AZURE.NOTE]Sarà inoltre possibile creare un [collegamento simbolico ](http://en.wikipedia.org/wiki/Symbolic_link) alla versione della piattaforma in uso, anche se in questa esercitazione verrà usato direttamente il nome binario per illustrare il funzionamento in modo chiaro. Di conseguenza, anziché comandi come `docker-machine env` riportati nella documentazione di **Docker Machine**, in questa esercitazione verrà usato il comando `docker-machine_linux-amd64 env`. La creazione di un collegamento simbolico o l'uso del nome binario direttamente è vantaggioso per l'utente, ma se si modifica il nome utilizzato è necessario modificare il nome anche nelle istruzioni seguenti.

<br />

>  Indipendentemente dal metodo scelto, è necessario chiamare il nome binario direttamente sulla riga di comando o inserirlo nel percorso, ad esempio **/usr/local/bin**. Verificare che sia contrassegnato come eseguibile digitando `chmod +x` &lt;*`binaryName`*&gt; dove &lt;*`binaryName`*&gt; è il nome di Docker Machine eseguibile. In questa esercitazione viene usato **docker-machine\_linux-amd64**.

## Creare i file di certificato e della chiave per docker, macchina e Azure

È necessario ora creare i file di certificato e della chiave con cui Azure potrà verificare l'identità e le autorizzazioni dell'utente, nonché i file necessari a **Docker Machine** per comunicare con la macchina virtuale di Azure e creare e gestire contenitori in remoto. Se questi file sono già disponibili in una directory, ad esempio per l'uso con docker, è possibile riutilizzarli. Tuttavia, per il test di **Docker Machine**, si consiglia di creare i file in una directory separata e di puntare Docker Machine a tali file.

> [AZURE.NOTE]Se si finisce con il provare e riprovare **Docker Machine**, assicurarsi di riutilizzare gli stessi file di certificato e chiave. **Docker Machine** crea anche un set di certificati client. Tutti gli elementi creati possono essere esaminati in `~/.docker/machine`. Se si spostano questi certificati in un altro computer, sarà necessario spostare anche le cartelle dei certificati di **Docker Machine** . Ciò costituirà una differenza se si prevede di usare **Docker Machine** su un'altra piattaforma, ad esempio per vedere semplicemente come funziona.

Se si ha esperienza con le distribuzioni Linux, è possibile che questi file siano già disponibili per l'uso nel computer in una specifica ubicazione. [La procedura viene illustrata anche nella documentazione di Docker HTTPS](https://docs.docker.com/articles/https/). Tuttavia, di seguito viene riportata la procedura più semplice.

1. Creare una cartella locale e, sulla riga di comando, passare in quella cartella e digitare:

		openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem
		openssl pkcs12 -export -out mycert.pfx -in mycert.pem -name "My Certificate"

	Immettere la password di esportazione per il certificato e acquisirla per un utilizzo futuro. Quindi digitare:

		openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer

2. Caricare il file .cer del certificato in Azure. Nel [Portale di Azure](https://manage.windowsazure.com), fare clic su **Impostazioni** in basso a sinistra dell’area di servizio (mostrata sotto)

	![][portalsettingsitem]

	quindi fare clic su **Certificati di gestione**:

	![][managementcertificatesitem]

	infine su **Carica** (nella parte inferiore della pagina) ![][uploaditem] per caricare il file **mycert.cer** creato al passaggio precedente.

3. Nello stesso riquadro **Impostazioni** nel portale, fare clic su **Sottoscrizioni** e acquisire l'ID sottoscrizione da usare quando si crea la macchina virtuale, in quanto verrà richiesto nel passaggio successivo. È anche possibile individuare l'ID sottoscrizione nella riga di comando usando il comando dell’interfaccia della riga di comando di Azure `azure account list`, che consente di visualizzare l'ID per ogni sottoscrizione disponibile nell'account.

4. Creare una macchina virtuale host Docker in Azure tramite il comando **docker-machine create**. Il comando richiede l'ID sottoscrizione acquisito al passaggio precedente e il percorso del file **.pem** creato al passaggio 1. In questo argomento, come nome del servizio cloud di Azure (e della macchina virtuale) viene usato "nome-computer", ma è opportuno sostituire tale nome con il nome scelto e ricordarsi di usare il nuovo nome del servizio cloud negli altri passaggi che richiedono il nome della macchina virtuale. Tenere presente che in questo esempio viene usato il nome binario completo e non un collegamento simbolico di **Docker Machine**.

		docker-machine_linux-amd64 create \
	    -d azure \
	    --azure-subscription-id="<subscription ID acquired above>" \
	    --azure-subscription-cert="mycert.pem" \
	    machine-name

	Dal momento che i primi due passaggi richiedono la creazione di una nuova macchina virtuale e il caricamento dell'agente di Azure per Linux nonché l'aggiornamento della nuova macchina virtuale, verrà visualizzato un risultato simile al seguente.

		INFO[0001] Creating Azure machine...
	    INFO[0049] Waiting for SSH...
	    modprobe: FATAL: Module aufs not found.
	    + sudo -E sh -c sleep 3; apt-get update
	    + sudo -E sh -c sleep 3; apt-get install -y -q linux-image-extra-3.13.0-36-generic
	    E: Unable to correct problems, you have held broken packages.
	    modprobe: FATAL: Module aufs not found.
	    Warning: tried to install linux-image-extra-3.13.0-36-generic (for AUFS)
	     but we still have no AUFS.  Docker may not work. Proceeding anyways!
	    + sleep 10
	    + [ https://get.docker.com/ = https://get.docker.com/ ]
	    + sudo -E sh -c apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 36A1D7869245C8950F966E92D8576A8BA88D21E9
	    gpg: requesting key A88D21E9 from hkp server keyserver.ubuntu.com
	    gpg: key A88D21E9: public key "Docker Release Tool (releasedocker) <docker@dotcloud.com>" imported
	    gpg: Total number processed: 1
	    gpg:               imported: 1  (RSA: 1)
	    + sudo -E sh -c echo deb https://get.docker.com/ubuntu docker main > /etc/apt/sources.list.d/docker.list
	    + sudo -E sh -c sleep 3; apt-get update; apt-get install -y -q lxc-docker
	    + sudo -E sh -c docker version
	    INFO[0368] "machine-name" has been created and is now the active machine.
	    INFO[0368] To point your Docker client at it, run this in your shell: $(docker-machine_linux-amd64 env machine-name)

    > [AZURE.NOTE]Poiché deve essere creata una macchina virtuale, per uno stato pronto potrebbero essere necessari alcuni minuti. Durante l'attesa, è possibile controllare lo stato del nuovo host Docker digitando `azure vm list` mediante l’interfaccia della riga di comando di Azure fino a visualizzare le macchine virtuali con lo stato **ReadyRole**.

5. Impostare le variabili di ambiente del docker e della macchina per la sessione terminale. Nell'ultima riga del feedback viene consigliato di eseguire immediatamente il comando **env** per esportare le variabili di ambiente necessarie per usare il client Docker direttamente con una macchina specifica.

		$(docker-machine_linux-amd64 env machine-name)

	Una volta eseguita questa operazione, non sono necessari comandi specifici per collegarsi alla macchina virtuale creata da Docker **machine** con il client docker locale.

	    $ docker info
	    Containers: 0
	    Images: 0
	    Storage Driver: devicemapper
	     Pool Name: docker-8:1-131736-pool
	     Pool Blocksize: 65.54 kB
	     Backing Filesystem: extfs
	     Data file: /dev/loop0
	     Metadata file: /dev/loop1
	     Data Space Used: 305.7 MB
	     Data Space Total: 107.4 GB
	     Metadata Space Used: 729.1 kB
	     Metadata Space Total: 2.147 GB
	     Udev Sync Supported: false
	     Data loop file: /var/lib/docker/devicemapper/devicemapper/data
	     Metadata loop file: /var/lib/docker/devicemapper/devicemapper/metadata
	     Library Version: 1.02.82-git (2013-10-04)
	    Execution Driver: native-0.2
	    Kernel Version: 3.13.0-36-generic
	    Operating System: Ubuntu 14.04.1 LTS
	    CPUs: 1
	    Total Memory: 1.639 GiB
	    Name: machine-name
	    ID: W3FZ:BCZW:UX24:GDSV:FR4N:N3JW:XOC2:RI56:IWQX:LRTZ:3G4P:6KJK
	    WARNING: No swap limit support

> [AZURE.NOTE]In questa esercitazione viene illustrata la creazione di una sola macchina virtuale con **Docker Machine**. Tuttavia, è possibile ripetere la procedura per creare il numero di macchine virtuali desiderato. In questo caso, il modo migliore per passare da una macchina virtuale all’altra con Docker consiste nell'usare il comando **env** in linea per impostare le variabili d’ambiente di **Docker** per ogni singolo comando. Ad esempio, per usare **docker info** con una macchina virtuale diversa, è possibile digitare `docker $(docker-machine env <VM name>) info`. Il comando **env** inserisce le informazioni relative alla connessione di Docker da usare con tale macchina virtuale.

## L'esercitazione è stata completata. Provare a eseguire alcune applicazioni in remoto usando docker e immagini dell'hub Docker.

È ora possibile usare docker normalmente per creare un'applicazione nel contenitore. L'applicazione più semplice da illustrare è [busybox](https://registry.hub.docker.com/_/busybox/):

	    $  docker run busybox echo hello world
	    Unable to find image 'busybox:latest' locally
	    511136ea3c5a: Pull complete
	    df7546f9f060: Pull complete
	    ea13149945cb: Pull complete
	    4986bf8c1536: Pull complete
	    busybox:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
	    Status: Downloaded newer image for busybox:latest
	    hello world

Tuttavia, è possibile che si desideri creare un'applicazione che si possa visualizzare immediatamente su Internet, ad esempio l'applicazione [nginx](https://registry.hub.docker.com/_/nginx/) dell'[hub Docker](https://registry.hub.docker.com/).

> [AZURE.NOTE]Usare l'opzione **-P** per fare in modo che **Docker ** assegni porte casuali all'immagine e **-d** per garantire che il contenitore venga eseguito in background senza interruzioni. In caso contrario, l'applicazione nginx verrà avviata e immediatamente chiusa. Non dimenticare di eseguire questo passaggio.

	$ docker run --name machinenginx -P -d nginx
    Unable to find image 'nginx:latest' locally
    30d39e59ffe2: Pull complete
    c90d655b99b2: Pull complete
    d9ee0b8eeda7: Pull complete
    3225d58a895a: Pull complete
    224fea58b6cc: Pull complete
    ef9d79968cc6: Pull complete
    f22d05624ebc: Pull complete
    117696d1464e: Pull complete
    2ebe3e67fb76: Pull complete
    ad82b43d6595: Pull complete
    e90c322c3a1c: Pull complete
    4b5657a3d162: Pull complete
    511136ea3c5a: Already exists
    nginx:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
    Status: Downloaded newer image for nginx:latest
    5883e2ff55a4ba0aa55c5c9179cebb590ad86539ea1d4d367d83dc98a4976848

Per visualizzare l'applicazione su Internet, creare un endpoint pubblico sulla porta 80 per la macchina virtuale di Azure ed eseguire il mapping di tale porta alla porta del contenitore nginx. Innanzitutto, usare `docker ps -a` per rilevare il contenitore e individuare le porte assegnate da **Docker** alla porta 80 del contenitore. (Le informazioni visualizzate di seguito sono state modificate per mostrare solo le informazioni relative alla porta; ne verranno visualizzate altre).

	$ docker ps -a
    IMAGE               PORTS
    nginx:latest        0.0.0.0:49153->80/tcp, 0.0.0.0:49154->443/tcp
    busybox:latest

Notare che docker ha assegnato la porta 80 del contenitore alla porta 49153 della macchina virtuale. È ora possibile usare l’interfaccia della riga di comando di Azure per eseguire il mapping della porta 80 pubblica del servizio cloud esterno alla porta 49153 della macchina virtuale. Docker verifica quindi che il traffico tcp in ingresso sulla porta 49153 della macchina virtuale venga instradato al contenitore nginx.

	$ azure vm endpoint create machine-name 80 49153
    info:    Executing command vm endpoint create
    + Getting virtual machines
    + Reading network configuration
    + Updating network configuration
    info:    vm endpoint create command OK

Aprire il browser preferito e dare uno sguardo.

![][nginx]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi
Consultare il [manuale dell'utente di Docker](https://docs.docker.com/userguide/) e creare alcune applicazioni in Microsoft Azure. In alternativa, iniziare a usare [**Docker** e Swarm](https://github.com/docker/swarm) in Azure (virtual-machines-docker-swarm) e scoprire come Swarm può essere usato con Docker e Azure.

<!--Image references-->
[nginx]: ./media/virtual-machines-docker-machine/nginxondocker.png
[portalsettingsitem]: ./media/virtual-machines-docker-machine/portalsettingsitem.png
[managementcertificatesitem]: ./media/virtual-machines-docker-machine/managementcertificatesitem.png
[uploaditem]: ./media/virtual-machines-docker-machine/uploaditem.png

<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: ../web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md
 

<!---HONumber=Sept15_HO4-->