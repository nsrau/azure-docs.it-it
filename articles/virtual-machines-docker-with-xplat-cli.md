<properties title="Come usare l'estensione della VM Docker dall'interfaccia multipiattaforma di Azure (xplat-cli)" pageTitle="Uso dell'estensione della VM Docker per Linux in Azure" description="Descrive Docker e le estensioni di Macchine virtuali di Azure e mostra come creare a livello di codice le macchine virtuali host di Docker su Azure dalla riga di comando usando l'interfaccia della riga di comando azure-cli." metaKeywords="linux, virtual machines, vm, azure, docker, linux containers,  lxc, virtualization" services="virtual-machines" solutions="dev-test" documentationCenter="virtual-machines" authors="rasquill" videoId="" scriptId="" manager="timlt" />

<tags ms.service="virtual-machines" ms.devlang="multiple" ms.topic="article" ms.tgt_pltfrm="vm-linux" ms.workload="infrastructure-services" ms.date="10/21/2014" ms.author="rasquill" />

# Uso dell'estensione della VM Docker dall'interfaccia multipiattaforma di Azure (xplat-cli)

Questo argomento descrive come creare una macchina virtuale con l'estensione della VM Docker di xplat-cli su qualsiasi piattaforma. [Docker][Docker] è uno dei più popolari approcci alla virtualizzazione che usa [contenitori Linux][contenitori Linux] invece di macchine virtuali allo scopo di isolare i dati ed eseguire i calcoli su risorse condivise. È possibile usare l'estensione della macchina virtuale Docker per l'[Agente Linux di Azure][Agente Linux di Azure] per creare una VM Docker che ospiti un numero qualsiasi di contenitori per le applicazioni su Azure. Per assistere a una discussione di alto livello sui contenitori e i relativi vantaggi, guardare questa [sessione con lavagna condivisa relativa a Docker][sessione con lavagna condivisa relativa a Docker].

-   [Come usare l'estensione della VM Docker con Azure][Come usare l'estensione della VM Docker con Azure]
-   [Estensioni della macchina virtuale per Linux e Windows][Estensioni della macchina virtuale per Linux e Windows]
-   [Contenitore e risorse di gestione del contenitore per Azure][Contenitore e risorse di gestione del contenitore per Azure]
-   [Passaggi successivi][Passaggi successivi]

## <span id="How to use the Docker VM Extension with Azure"></span>Come usare l'estensione della VM Docker con Azure</a>

Per usare l'estensione della VM Docker con Azure è necessario installare una versione dell'[interfaccia della riga di comando di Azure][interfaccia della riga di comando di Azure] (denominata **xplat-cli** in questo argomento) successiva alla 0.8.6 (al momento della stesura di questo articolo, la versione corrente è la 0.8.10). È possibile installare xplat-cli su Mac, Linux e Windows.

> [WACOM.NOTE] Sebbene sia possibile installare xplat-cli su Microsoft Windows, Docker è stato costruito con dipendenze del kernel specifiche di Linux. Per usare Windows come client Docker, occorre quindi ospitare una distribuzione completa di Linux come macchina virtuale in Hyper-V o in altro hypervisor. A quel punto sarà possibile usare l'interfaccia della riga di comando xplat-cli e i comandi di Docker di questo documento e quelli relativi a Docker. Lo stesso Docker è dotato di un programma di installazione per Windows, [Boot2Docker][Boot2Docker], che è possibile usare per automatizzare questa stessa installazione.

Il processo completo di utilizzo di Docker su Azure è semplice:

-   Installare gli strumenti da riga di comando di xplat-cli e le relative dipendenze sul computer dal quale si intende controllare Azure (su Windows, si tratterà di una distribuzione Linux in esecuzione come macchina virtuale)
-   Usare i comandi di Docker per xplat-cli per creare un host Docker della VM in Azure
-   Usare i comandi locali di Docker per gestire i contenitori Docker nella VM Docker in Azure.

> [WACOM.NOTE] L'interfaccia della riga di comando xplat-cli costituisce al momento l'unico modo per creare una VM controllata da Docker su Azure per ospitare contenitori Docker.

### Installare l'interfaccia della riga di comando multipiattaforma (xplat-cli)

Per installare e configurare l'interfaccia della riga di comando multipiattaforma, vedere [Come installare l'interfaccia della riga di comando multipiattaforma di Azure][Come installare l'interfaccia della riga di comando multipiattaforma di Azure]. Per confermare l'installazione, digitare `azure` al prompt dei comandi. Dopo pochi secondi verrà visualizzata la grafica ASCII di xplat-cli, in cui sono elencati i comandi di base disponibili. Se l'installazione è andata a buon fine, sarà possibile digitare `azure help vm` per visualizzare che uno dei comandi elencati è "docker".

> [WACOM.NOTE] Se si usa un'installazione di Ubuntu 14.04 LTS, l'immagine avrà un'installazione del nodo lievemente differente, che potrebbe richiedere ulteriori operazioni. Un buon suggerimento è disponibile [qui][qui] nella sezione relativa a **come eseguire l'installazione con un PPA**, in cui viene descritta la procedura di installazione diretta della versione più recente di NodeJS, che dovrebbe funzionare bene in una distribuzione di Ubuntu 14.04 LTS.

### Connettere l'interfaccia della riga di comando xplat-cli al proprio account Azure

Prima di poter usare l'interfaccia della riga di comando xplat-cli è necessario associare alla stessa le credenziali del proprio account Azure con l'interfaccia xplat-cli sulla propria piattaforma. La sezione [Come connettersi alla sottoscrizione di Azure][Come connettersi alla sottoscrizione di Azure] spiega come scaricare e importare il file **.publishsettings** o un file associato nella riga di comando di xplat-cli con un ID di organizzazione.

> [WACOM.NOTE] Ci sono alcune differenze di comportamento quando si usa l'uno o l'altro metodo di autenticazione, perciò assicurarsi di leggere il documento sopra riportato per comprendere le diverse funzionalità.

### Installare Docker e usare l'estensione della VM Docker per Azure

Per installare Docker in locale sul proprio computer seguire le [istruzioni di installazione di Docker][istruzioni di installazione di Docker]. Per la maggior parte dei sistemi operativi e delle distribuzioni ciò significa digitare `apt-get install docker.io`. Verificare di aver installato Docker 1.0 o una versione successiva.

Per usare Docker con una macchina virtuale di Azure, l'immagine Linux usata per la VM deve avere installato l'[agente VM Linux Azure][Agente Linux di Azure]. Al momento esistono solo due tipi di immagine che offrono queste funzionalità:

-   un'immagine Ubuntu dalla raccolta immagini di Azure o

-   un'immagine Linux personalizzata creata con l'agente VM Linux Azure installato e configurato. Per altre informazioni su come costruire una VM Linux con l'agente VM di Azure, vedere [Guida dell'utente dell'agente Linux di Azure][Agente Linux di Azure].

### Uso della raccolta immagini di Azure

Da una sessione Bash o Terminal, usare il comando xplat-cli seguente per trovare l'immagine più recente di Ubuntu nella raccolta VM da usare digitando

`azure vm image list | grep Ubuntu-14_04`

e selezionare un nome di immagine come `b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04-LTS-amd64-server-20140724-it-it-30GB`, quindi usare il comando seguente per creare nuova VM che usa tale immagine.

    azure vm docker create -e 22 -l "West US" <vm-cloudservice name> "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04-LTS-amd64-server-20140724-it-it-30GB" <username> <password>

dove:

-   *\<vm-cloudservice name\>* è il nome della VM che diventerà il computer host del contenitore Docker in Azure

-   *\<username\>* è il nome utente dell'utente ROOT predefinito della VM

-   *\<password\>* è la password dell'account *username* che soddisfa gli standard di complessità per Azure

> [WACOM.NOTE] Attualmente, una password deve contenere almeno 8 caratteri, un carattere minuscolo e uno maiuscolo, un numero e un carattere speciale, come uno dei seguenti: `!@#$%^&+=` Il punto alla fine della frase precedente NON è un carattere speciale.

Se il comando ha avuto esito positivo, verrà visualizzato un output simile al seguente, a seconda degli argomenti e delle opzioni precisi usati:

![][0]

> [WACOM.NOTE] La creazione di una macchina virtuale può richiedere alcuni minuti, ma dopo che ne è stato effettuato il provisioning viene avviato il daemon Docker ed è possibile connettersi all'host contenitore Docker.

Per testare la VM Docker creata in Azure, digitare

`docker --tls -H tcp://<vm-name-you-used>.cloudapp.net:4243 info`

dove *<vm-name-you-used>* è il nome della macchina virtuale usata nella chiamata a `azure vm docker create`. Viene visualizzato codice simile al seguente, che indica che la VM host di Docker è in fase di elaborazione in Azure ed è in attesa dei comandi dell'utente.

![][1]

### Autenticazione della VM host di Docker

Oltre a creare la VM di Docker, il comando `azure vm docker create` crea automaticamente anche i necessari certificati che consentono al computer client Docker di connettersi all'host contenitore di Azure con il protocollo HTTPS. I certificati saranno archiviati sulle macchine client e host, secondo le esigenze. Nelle esecuzioni successive, i certificati esistenti verranno riutilizzati e condivisi con il nuovo host.

Per impostazione predefinita, i certificati vengono inseriti in `~/.docker` e Docker verrà configurato per l'esecuzione sulla porta **4243**. Per scegliere una porta o una directory differente, usare una delle seguenti opzioni della riga di comando `azure vm docker create` per configurare la VM host del contenitore Docker in modo da usare una porta differente o certificati differenti per connettere i client:

    -dp, --docker-port [port]              Port to use for docker [4243]
    -dc, --docker-cert-dir [dir]           Directory containing docker certs [.docker/]

Il daemon Docker sull'host è configurato per restare in ascolto delle connessioni client e autenticarle sulla porta specificata usando i certificati generati dal comando `azure vm docker create`. La macchina client deve avere questi certificati per poter accedere all'host Docker.

> [WACOM.NOTE] Un host di rete in esecuzione senza questi certificati sarà vulnerabile nei confronti di chiunque possa connettersi alla macchina. Prima di modificare la configurazione predefinita, assicurarsi di aver compreso i rischi a cui si sottopongono i computer e le applicazioni.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## Passaggi successivi

È ora possibile passare alla [guida dell'utente di Docker][guida dell'utente di Docker] e usare la VM Docker. Per creare una VM abilitata per Docker incorporata nel nuovo portale, vedere l'articolo su [come usare l'estensione della VM Docker con il portale][come usare l'estensione della VM Docker con il portale].

<!--Anchors--> <!--Image references--> <!--Link references-->

  [Docker]: https://www.docker.com/
  [contenitori Linux]: http://en.wikipedia.org/wiki/LXC
  [Agente Linux di Azure]: http://azure.microsoft.com/it-it/documentation/articles/virtual-machines-linux-agent-user-guide/
  [sessione con lavagna condivisa relativa a Docker]: http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard
  [Come usare l'estensione della VM Docker con Azure]: #How-to-use-the-Docker-VM-Extension-with-Azure
  [Estensioni della macchina virtuale per Linux e Windows]: #Virtual-Machine-Extensions-For-Linux-and-Windows
  [Contenitore e risorse di gestione del contenitore per Azure]: #Container-and-Container-Management-Resources-for-Azure
  [Passaggi successivi]: #next-steps
  [interfaccia della riga di comando di Azure]: https://github.com/Azure/azure-sdk-tools-xplat
  [Boot2Docker]: https://docs.docker.com/installation/windows/
  [Come installare l'interfaccia della riga di comando multipiattaforma di Azure]: http://azure.microsoft.com/it-it/documentation/articles/xplat-cli/#install
  [qui]: https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-an-ubuntu-14-04-server
  [Come connettersi alla sottoscrizione di Azure]: http://azure.microsoft.com/it-it/documentation/articles/xplat-cli/#configure
  [istruzioni di installazione di Docker]: https://docs.docker.com/installation/#installation
  [0]: ./media/virtual-machines-docker/dockercreateresults.png
  [1]: ./media/virtual-machines-docker/connectingtodockerhost.png
  [guida dell'utente di Docker]: https://docs.docker.com/userguide/
  [come usare l'estensione della VM Docker con il portale]: http://azure.microsoft.com/it-it/documentation/articles/virtual-machines-docker-with-portal/
