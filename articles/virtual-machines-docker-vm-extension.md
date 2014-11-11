<properties title="Using the Docker Virtual Machines Extension for Linux on Azure" pageTitle="Using the Docker VM Extension for Linux on Azure" description="Describes Docker and the Azure Virtual Machines extensions, and shows how to programmatically create Virtual Machines on Azure that are docker hosts from the command line using the azure-cli command interface." metaKeywords="linux, virtual machines, vm, azure, docker, linux containers,  lxc, virtualization" services="virtual-machines" solutions="dev-test" documentationCenter="virtual-machines" authors="rasquill" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.devlang="multiple" ms.topic="article" ms.tgt_pltfrm="vm-linux" ms.workload="infrastructure-services" ms.date="08/21/2014" ms.author="ralph.squillace@microsoft.com" />

# Come usare l'estensione della macchina virtuale Docker per Linux in Azure

[Docker][Docker] è uno dei più popolari approcci alla virtualizzazione che usa [contenitori Linux][contenitori Linux] invece di macchine virtuali allo scopo di isolare i dati ed eseguire i calcoli su risorse condivise. È possibile usare l'estensione della macchina virtuale Docker per l'[Agente Linux di Azure][Agente Linux di Azure] per creare una VM Docker che ospiti un numero qualsiasi di contenitori per le applicazioni su Azure.

Questo argomento fa seguito a questo [annuncio sul blog di MS Open Tech][annuncio sul blog di MS Open Tech] e descrive:

-   [Contenitori Docker e Linux][Contenitori Docker e Linux]
-   [Come usare l'estensione della VM Docker con Azure][Come usare l'estensione della VM Docker con Azure]
-   [Estensioni della macchina virtuale per Linux e Windows][Estensioni della macchina virtuale per Linux e Windows]
-   [Contenitore e risorse di gestione del contenitore per Azure][Contenitore e risorse di gestione del contenitore per Azure]

## Contenitori Docker e Linux

[Docker][Docker] è uno dei più popolari approcci alla virtualizzazione che usa [contenitori Linux][contenitori Linux] invece di macchine virtuali allo scopo di isolare i dati ed eseguire i calcoli su risorse condivise. Inoltre, fornisce altri servizi che consentono di creare o assemblare rapidamente applicazioni e distribuirle tra altri contenitori Docker.

I contenitori Docker e Linux non sono [Hypervisor][Hypervisor] come Windows Hyper-V e [KVM][KVM] su Linux (vi sono molti altri esempi). Gli hypervisor virtualizzano il sistema operativo sottostante per consentire l'esecuzione di sistemi operativi completi all'interno di un hypervisor come se fossero un'applicazione.

L'approccio dell'hypervisor offre importanti vantaggi in termini di sicurezza perché la macchina virtuale "guest" non ha alcun accesso al sistema operativo "host", ma può solo usare le risorse dell'hypervisor. Tra gli svantaggi sono tuttavia inclusi tempi di elaborazione prolungati e costi di archiviazione più elevati, oltre a tempi di avvio delle nuove macchine virtuali relativamente più lenti perché, tra le altre cose, replicano completamente i sistemi operativi guest.

#### Contenitori Docker e Linux

Docker e altri *contenitori* con lo stesso approccio hanno radicalmente ridotto sia i tempi di avvio che quelli di elaborazione, oltre ai costi di archiviazione, richiesti dall'uso del processo e dalle funzionalità di isolamento del file system del kernel Linux, in modo da esporre solo le funzionalità del kernel a un contenitore altrimenti isolato. Dall'interno del contenitore, le funzionalità di kernel e file system appaiono all'applicazione come se fossero l'unica applicazione in esecuzione.

In aggiunta, Docker offre diverse funzionalità di gestione del contenitore che consentono di caricare varie immagini del contenitore dalla community oltre a creare e caricare i propri in maniera rapidissima. Per informazioni dettagliate su Docker e sul suo funzionamento, vedere le [informazioni su Docker][informazioni su Docker].

Come molte tecnologie, anche questa comporta vantaggi considerevoli e alcuni svantaggi. Poiché i contenitori condividono l'accesso al kernel del computer host, se il malware riesce a penetrare potrebbe accedere non solo al computer host, ma anche agli altri contenitori. Per proteggere il sistema contenitore in maniera più sicura, [Docker consiglia][Docker consiglia] di usare criteri di gruppo aggiuntivi o la [sicurezza basata sui ruoli][sicurezza basata sui ruoli], come [SELinux][SELinux] o [AppArmor][AppArmor], ad esempio, oltre a ridurre per quanto possibile le capacità del kernel concesse ai contenitori. Su Internet ci sono molti altri documenti in cui si descrivono gli approcci alla sicurezza quando si usano contenitori come Docker.

## Come usare l'estensione della VM Docker con Azure

Per usare l'estensione della VM Docker con Azure è necessario installare una versione dell'interfaccia della riga di comando [azure-cli][azure-cli] successiva alla 0.8.6 (al momento della stesura di questo articolo, la versione corrente è la 0.8.7). È possibile installare l'interfaccia della riga di comando azure-cli sia su Mac sia su Linux.

> [WACOM.NOTE] È anche possibile installare l'interfaccia della riga di comando azure-cli su Microsoft Windows. Tuttavia, poiché Docker è stato progettato con dipendenze kernel Linux, per usare Windows come client Docker è necessario ospitare una distribuzione completa di Linux come macchina virtuale all'interno di Hyper-V o di un altro hypervisor. A quel punto sarà possibile usare l'interfaccia della riga di comando azure-cli e i comandi di Docker in questo documento e in quelli relativi a Docker. Lo stesso Docker è dotato di un programma di installazione, [Boot2Docker][Boot2Docker], che è possibile usare per automatizzare questa stessa installazione.

Il processo completo di utilizzo di Docker su Azure è semplice:

-   Installare gli strumenti da riga di comando azure-cli e le relative dipendenze sul computer dal quale si intende controllare Azure (su Windows, si tratterà di una distribuzione Linux in esecuzione come macchina virtuale)
-   Usare i comandi di Docker per azure-cli per creare un host Docker della VM in Azure
-   Usare i comandi locali di Docker per gestire i contenitori Docker nella VM Docker in Azure.

> [WACOM.NOTE] L'interfaccia della riga di comando azure-cli costituisce al momento l'unico modo per creare una VM controllata da Docker su Azure per ospitare contenitori Docker. Il documento di installazione generale è disponibile [qui][qui]; le sezioni riportate di seguito offrono alcuni suggerimenti aggiuntivi per eseguire correttamente l'installazione su vari sistemi operativi.

### Installare l'interfaccia della riga di comando azure-cli su Linux

Su Linux, l'installazione dell'interfaccia della riga di comando azure-cli richiede [Node Package Manager (NPM)][Node Package Manager (NPM)], che richiede NodeJS, quindi si potrà usare l'utilità di gestione pacchetti preferita per installare NodeJS, a seconda della piattaforma scelta. Se è stato installato NPM, per ottenere il pacchetto di azure-cli digitare:

    npm install -g azure-cli

Il pacchetto di azure-cli verrà installato a livello globale. Per confermare l'installazione, digitare `azure` nel prompt dei comandi. Dopo pochi secondi verrà visualizzata la grafica ASCII di azure-cli, in cui sono elencati i comandi di base disponibili. Se l'installazione è andata a buon fine, sarà possibile digitare `azure help vm` per visualizzare che uno dei comandi elencati è "docker".

> [WACOM.NOTE] Se si usa un'installazione di Ubuntu 14.04 LTS, l'immagine avrà un'installazione del nodo lievemente differente, che potrebbe richiedere ulteriori operazioni. Un buon suggerimento è disponibile [qui][1] nella sezione relativa a **come eseguire l'installazione con un PPA**, in cui viene descritta la procedura di installazione diretta della versione più recente di NodeJS, che dovrebbe funzionare bene in una distribuzione di Ubuntu 14.04 LTS.

In alternativa, come per la maggior parte dei componenti Linux, è possibile clonare l'origine, compilarla e installarla anche localmente. Le istruzioni a tale scopo sono disponibili all'indirizzo [][azure-cli]<https://github.com/Azure/azure-sdk-tools-xplat></a>.

### Installare l'interfaccia della riga di comando azure-cli su Mac

Su un Mac, il modo più semplice per installare l'interfaccia della riga di comando azure-cli consiste anche in questo caso nell'usare NPM con lo stesso comando: `npm install -g azure-cli`. Tuttavia, è possibile usare anche il [programma di installazione per Mac][programma di installazione per Mac]. Come per Linux e Windows, sarà quindi possibile digitare `azure` nel prompt dei comandi associato e confermare l'avvenuta installazione di azure-cli.

### Connettere l'interfaccia della riga di comando azure-cli al proprio account Azure

Prima di poter usare l'interfaccia della riga di comando azure-cli è necessario associare alla stessa le credenziali del proprio account Azure sulla propria piattaforma. La sezione [Come connettersi alla sottoscrizione di Azure][Come connettersi alla sottoscrizione di Azure] illustra come scaricare e importare il file **.publishsettings** o associare l'interfaccia della riga di comando azure-cli a un ID dell'organizzazione. I passaggi per i metodi di autenticazione e autorizzazione sono descritti nel documento sopra citato.

> [WACOM.NOTE] Ci sono alcune differenze di comportamento quando si usa l'uno o l'altro metodo di autorizzazione, perciò assicurarsi di leggere il documento sopra riportato per comprendere le differenti funzionalità.

### Installare Docker e usare l'estensione della VM Docker per Azure

A questo punto, sarà stata installata l'interfaccia della riga di comando azure-cli su un computer (o su una macchina virtuale su tale computer) e sarà stata eseguita la connessione all'account Azure. Per installare Docker in locale sul proprio computer seguire le [istruzioni di installazione di Docker][istruzioni di installazione di Docker]. Per la maggior parte dei sistemi operativi e delle distribuzioni ciò significa digitare `apt-get install docker.io`. Verificare di aver installato Docker 1.0 o una versione successiva.

È stata installata l'interfaccia della riga di comando azure-cli sul computer, effettuata la connessione al proprio account Azure ed eseguita l'installazione di Docker. Per creare una nuova VM host di Docker in Azure è necessaria un'immagine della VM di Linux su cui sia stato installato l'[Agente VM Linux di Azure][Agente Linux di Azure]. Attualmente, le uniche immagini sul quale è già installato l'agente sono

-   un'immagine Ubuntu dalla raccolta immagini o

-   un'immagine Linux personalizzata, creata con l'Agente VM Linux di Azure installato e configurato. Per altre informazioni su come creare le proprie VM Linux personalizzate con l'Agente VM di Azure, vedere [Agente VM Linux di Azure][Agente Linux di Azure].

Con il prompt dei comandi azure-cli cercare nella galleria VM l'immagine Ubuntu più recente da usare digitando

`azure vm image list | grep Ubuntu-14_04`

e tenersi pronti a copiare il nome di una delle immagini più recenti elencate. Al prompt dei comandi digitare

    azure vm docker create -e 22 -l "West US" <vm-cloudservice name> "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20140729-alpha2-it-it-30GB" <username> <password>

dove:

-   *<vm-cloudservice name>* è il nome della VM che diventerà il computer host del contenitore Docker in Azure

-   *<username>* è il nome utente dell'utente ROOT predefinito della VM

-   *<password>* è la password dell'account *nomeutente* che soddisfa gli standard di complessità per Azure

> [WACOM.NOTE] Attualmente, una password deve contenere almeno 8 caratteri, un carattere minuscolo e uno maiuscolo, un numero e un carattere speciale, come [!@\#$%^&+=][!@\#$%^&+=]. Il punto alla fine della frase precedente NON è un carattere speciale.

Se il comando ha avuto esito positivo, verrà visualizzato un output simile al seguente, a seconda degli argomenti e delle opzioni precisi usati:

![][0]

> [WACOM.NOTE] Come già accennato in precedenza, la creazione di una macchina virtuale può richiedere alcuni minuti, ma dopo che ne è stato effettuato il provisioning viene avviato il daemon Docker e sarà possibile connettersi all'host contenitore Docker.

Per testare la VM Docker creata in Azure, digitare

`docker --tls -H tcp://<vm-name-you-used>.cloudapp.net:4243 info`

dove *<vm-name-you-used>* è il nome della macchina virtuale usata nella chiamata a `azure vm docker create`. A seconda degli argomenti e delle opzioni specificati, si vedrà un output simile alla risposta seguente, che indica che la VM host di Docker è operativa in Azure e in attesa dei comandi.

![][2]

## Nota sull'autenticazione della VM host di Docker

Oltre a creare la VM di Docker, il comando `azure vm docker create` crea automaticamente anche i necessari certificati che consentono al computer client Docker di connettersi all'host contenitore di Azure con il protocollo HTTPS. I certificati saranno archiviati sulle macchine client e host, secondo le esigenze. Nelle esecuzioni successive, i certificati esistenti verranno riutilizzati e condivisi con il nuovo host.

Per impostazione predefinita, i certificati vengono inseriti in `~/.docker` e Docker verrà configurato per l'esecuzione sulla porta **4243**. Per scegliere una porta o una directory differente, usare una delle seguenti opzioni della riga di comando `azure vm docker create` per configurare la VM host del contenitore Docker in modo da usare una porta differente o certificati differenti per connettere i client:

    -dp, --docker-port [port]              Port to use for docker [4243]
    -dc, --docker-cert-dir [dir]           Directory containing docker certs [.docker/]

Il daemon Docker sull'host è configurato per restare in ascolto delle connessioni client e autenticarle sulla porta specificata usando i certificati generati dal comando `azure vm docker create`. La macchina client deve avere questi certificati per poter accedere all'host Docker.

> [WACOM.NOTE] Viceversa, un host di rete in esecuzione senza questi certificati sarà vulnerabile a chiunque possa connettersi alla macchina. Prima di modificare la configurazione predefinita, assicurarsi di aver compreso i rischi a cui si sottopongono i computer e le applicazioni.

## Passaggi successivi

È ora possibile eseguire i comandi di Docker elencati nella [guida dell'utente di Docker][guida dell'utente di Docker] sulla VM host di Docker in Azure.

## Estensioni della macchina virtuale per Linux e Windows

L'estensione VM di Docker per Azure è solo una delle varie estensioni VM che offrono un comportamento speciale, e molte altre sono attualmente in corso di sviluppo. Ad esempio, diverse funzionalità dell'estensione [Agente VM Linux][Agente Linux di Azure] consentono di modificare e gestire l'immagine, incluse le funzionalità di sicurezza, kernel e rete, e così via. L'estensione VMAccess per Windows consente di reimpostare o modificare le impostazioni di accesso a Desktop remoto e la password dell'amministratore.

Per un elenco completo, vedere [Estensioni della VM di Azure][Estensioni della VM di Azure].

<!--Anchors-->

  [Docker]: https://www.docker.com/
  [contenitori Linux]: http://en.wikipedia.org/wiki/LXC
  [Agente Linux di Azure]: http://azure.microsoft.com/it-it/documentation/articles/virtual-machines-linux-agent-user-guide/
  [annuncio sul blog di MS Open Tech]: http://msopentech.com/blog/2014/08/15/getting_started_docker_on_microsoft_azure/
  [Contenitori Docker e Linux]: #Docker-and-Linux-Containers
  [Come usare l'estensione della VM Docker con Azure]: #How-to-use-the-Docker-VM-Extension-with-Azure
  [Estensioni della macchina virtuale per Linux e Windows]: #Virtual-Machine-Extensions-For-Linux-and-Windows
  [Contenitore e risorse di gestione del contenitore per Azure]: #Container-and-Container-Management-Resources-for-Azure
  [Hypervisor]: http://en.wikipedia.org/wiki/Hypervisor
  [KVM]: http://www.linux-kvm.org/page/Main_Page
  [informazioni su Docker]: https://www.docker.com/whatisdocker/
  [Docker consiglia]: https://docs.docker.com/articles/security/
  [sicurezza basata sui ruoli]: http://en.wikipedia.org/wiki/Role-based_access_control
  [SELinux]: http://selinuxproject.org/page/Main_Page
  [AppArmor]: http://wiki.apparmor.net/index.php/Main_Page
  [azure-cli]: https://github.com/Azure/azure-sdk-tools-xplat
  [Boot2Docker]: https://docs.docker.com/installation/windows/
  [qui]: http://azure.microsoft.com/it-it/documentation/articles/xplat-cli/#install
  [1]: https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-an-ubuntu-14-04-server
  [programma di installazione per Mac]: http://go.microsoft.com/fwlink/?linkid=252249&clcid=0x409
  [Come connettersi alla sottoscrizione di Azure]: http://azure.microsoft.com/it-it/documentation/articles/xplat-cli/#configure
  [istruzioni di installazione di Docker]: https://docs.docker.com/installation/#installation
  [0]: ./media/virtual-machines-docker/dockercreateresults.png
  [2]: ./media/virtual-machines-docker/connectingtodockerhost.png
  [guida dell'utente di Docker]: https://docs.docker.com/userguide/
  [Estensioni della VM di Azure]: http://msdn.microsoft.com/it-it/library/azure/dn606311.aspx
