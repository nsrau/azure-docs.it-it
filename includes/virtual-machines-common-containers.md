
 

Azure offre soluzioni cloud eccezionali montate su macchine virtuali&mdash; basate sull'emulazione di componenti hardware del computer fisico&mdash;per abilitare lo spostamento agile di distribuzioni software e migliorare notevolmente il consolidamento delle risorse piuttosto che l’hardware fisico. Negli ultimi anni, in gran parte grazie all’approccio [Docker](https://www.docker.com) ai contenitori e all'ecosistema Docker, la tecnologia dei contenitori Linux ha notevolmente ampliato i modi in cui è possibile sviluppare e gestire i software distribuiti. Il codice dell'applicazione in un contenitore è isolato dalla VM di Azure ospitata, come anche altri contenitori nella stessa VM, il che offre maggiore flessibilità di sviluppo e distribuzione al livello dell’applicazione&mdash;oltre alla flessibilità che le VM di Azure già offrono.

**Ma questo è il passato.** La novità *più recente* è che Azure offre ulteriori vantaggi Docker:

- [Molti](../articles/virtual-machines/virtual-machines-linux-classic-cli-use-docker.md) [diversi](virtual-machines-linux-classic-portal-use-docker.md) [modi](virtual-machines-linux-classic-docker-quickstart.md) di [creare host Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu) per i contenitori in base alle proprie esigenze
- [Gestione risorse di Azure](../articles/resource-group-overview.md) e [modelli del gruppo di risorse](../articles/resource-group-authoring-templates.md) per semplificare la distribuzione e l’aggiornamento di applicazioni distribuite complesse
- integrazione con un'ampia gamma di strumenti di gestione della configurazione sia proprietari che open source

E poiché è possibile creare a livello di programmazione le VM e i contenitori Linux in Azure, è inoltre possibile utilizzare strumenti di *orchestrazione* per VM e contenitori per creare gruppi di Macchine virtuali (VM) e distribuire le applicazioni sia all'interno di contenitori Linux che, presto, all’interno di [contenitori Windows](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview).

In questo articolo non solo vengono discussi questi concetti a livello generale, ma vengono anche forniti numerosi link per accedere a maggiori informazioni, esercitazioni e prodotti correlati all’utilizzo di contenitori e cluster in Azure. Se si è già a conoscenza di tutto ciò e si desidera visualizzare i link, essi si trovano proprio qui negli [strumenti per lavorare con i contenitori](#tools-for-working-with-containers).

## La differenza tra le macchine virtuali e i contenitori

Le macchine virtuali vengono eseguite all'interno di un ambiente di virtualizzazione hardware isolato fornito da un [hypervisor](http://en.wikipedia.org/wiki/Hypervisor). In Azure, il servizio [Macchine virtuali](https://azure.microsoft.com/services/virtual-machines/) si occupa di tutto questo per l'utente: per creare Macchine virtuali è sufficiente scegliere il sistema operativo e configurarlo per eseguire nel modo in cui desidera&mdash; oppure caricando un'immagine di VM personalizzata. Le Macchine virtuali sono una tecnologia "temprata", collaudata dal tempo, e sono disponibili molti strumenti per gestire i sistemi operativi e per configurare le applicazioni che vengono installate ed eseguite. Qualsiasi elemento in esecuzione in una macchina virtuale è nascosto dal sistema operativo host e, dal punto di vista di un'applicazione o utente in esecuzione all'interno di una macchina virtuale, la macchina virtuale sembra essere un computer fisico autonomo.

[Linux containers](http://en.wikipedia.org/wiki/LXC)&mdash; che include quelli creati e ospitati utilizzando strumenti di Docker, e vi sono altri approcci&mdash;non richiede né utilizza un hypervisor per garantire l'isolamento. Al contrario, l'host del contenitore utilizza le funzionalità di isolamento del processo e del file system del kernel Linux per esporre al contenitore (e alla relativa applicazione) solo alcune funzionalità di kernel e il proprio file system isolato (almeno). Dal punto di vista di un'applicazione in esecuzione all'interno di un contenitore, il contenitore appare come un'istanza univoca del sistema operativo Un'applicazione indipendente non può vedere processi o qualsiasi altra risorsa all'esterno del relativo contenitore.

Poiché in questo modello di isolamento ed esecuzione il kernel del computer host Docker è condiviso, e poiché i requisiti del disco del contenitore non includono ora un intero sistema operativo, i tempi di avvio del contenitore e l'overhead di archiviazione su disco richiesto sono molto, molto più ridotti.

È davvero fantastico.

I contenitori Windows forniscono gli stessi vantaggi dei contenitori Linux, ma per le applicazioni eseguite in Windows. I contenitori Windows supportano il formato di immagine Docker e l'API Docker, tuttavia possono essere gestiti anche tramite PowerShell. Due runtime contenitore sono disponibili con i contenitori Windows, i contenitori Windows Server e i contenitori Hyper-V. I contenitori Hyper-V forniscono un ulteriore livello di isolamento tramite l’hosting di ogni contenitore in una macchina virtuale super ottimizzata. Per ulteriori informazioni sui contenitori Windows, vedere [Informazioni sui contenitori Windows](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview). Per provare i contenitori Windows in Azure, vedere la [Guida introduttiva di Azure sui contenitori Windows](https://msdn.microsoft.com/virtualization/windowscontainers/quick_start/azure_setup).

Anche questo è davvero fantastico.

### È troppo bello per essere vero?

Beh, sì&mdash;e no. I contenitori, come qualsiasi altra tecnologia, non fanno sparire magicamente tutte le operazioni richieste dalle applicazioni distribuite. Tuttavia, i contenitori modificano allo stesso tempo:

- la velocità con cui il codice dell’applicazione può essere sviluppato e ampiamente condiviso
- la velocità e la probabilità con cui può essere testato
- la velocità e la probabilità con cui può essere distribuito

Detto ciò, è necessario ricordare che i contenitori eseguono su un host del contenitore&mdash;un sistema operativo, e in Azure quindi una macchina virtuale di Azure. Anche se si sta già apprezzando l'idea dei contenitori, sarà comunque necessario disporre di un'infrastruttura di VM che ospiti i contenitori, ma il vantaggio è che non occorre determinare su quale VM vengono eseguiti i contenitori (anche se, nel caso in cui il contenitore richieda un ambiente di esecuzione di Linux o Windows, sarà importante tenerlo presente, ad esempio).

## Per quale scopo sono utili i contenitori?

I contenitori trovano molte applicazioni, ad esempio favoriscono&mdash; come fanno anche i [Servizi Cloud di Azure](https://azure.microsoft.com/services/cloud-services/) e [Azure Service Fabric](../articles/service-fabric/service-fabric-overview.md)&mdash;la creazione di un singolo servizio, di applicazioni distribuite orientate al [microservizio], in cui il design dell’applicazione si basa su parti più piccole, componibili anziché su componenti più grandi e più fortemente accoppiati.

Ciò vale soprattutto in ambienti di cloud pubblico come Azure, in cui in si possono affittare VM quando e dove si desidera. Non solo si ottengono un isolamento, una rapida distribuzione e gli strumenti di orchestrazione, ma è anche possibile prendere decisioni più efficienti sull’infrastruttura delle applicazioni.

Ad esempio, si potrebbe disporre attualmente di una distribuzione costituita da 9 VM di Azure di grandi dimensioni per un'applicazione distribuita a disponibilità elevata. Se i componenti di questa applicazione possono essere distribuiti in contenitori, sarà possibile utilizzare solo 4 VM e distribuire i componenti dell'applicazione all'interno di 20 contenitori per garantire la ridondanza e il bilanciamento del carico.

Questo è solo un esempio, naturalmente, ma se si riesce ad attuarlo nel proprio scenario, è possibile regolare i picchi di utilizzo con più contenitori anziché con più VM di Azure e utilizzare il carico complessivo della CPU rimanente con molta più efficienza di prima.

Inoltre, esistono molti scenari che non si prestano a un approccio di microservizi; sarà possibile capire meglio se i microservizi e i contenitori sono di aiuto.

### Vantaggi dei contenitori per gli sviluppatori

In generale, è facile rendersi conto che la tecnologia dei contenitori è un passo in avanti, ma esistono anche vantaggi più specifici. Esaminiamo l'esempio dei contenitori Docker. In questo argomento non si parlerà approfonditamente di Docker (leggere [Che cos’è Docker?](https://www.docker.com/whatisdocker/) a tal riguardo o [wikipedia](http://wikipedia.org/wiki/Docker_%28software%29)), ma si presenteranno i notevoli benefici che Docker e il relativo ecosistema offrono per gli sviluppatori e professionisti IT.

Gli sviluppatori familiarizzano velocemente con i contenitori di Docker, perché in particolare ciò rende semplice l’utilizzo di Linux containers:

- Gli sviluppatori possono usare comandi semplici e incrementali per creare un'immagine fissa facile da distribuire e possono automatizzare la creazione di tali immagini utilizzando un dockerfile
- Gli sviluppatori possono condividere facilmente le immagini utilizzando semplici comandi push and pull in stile [git](https://git-scm.com/) in [registri Docker pubblici](https://registry.hub.docker.com/) o [privati](../articles/virtual-machines/virtual-machines-linux-docker-registry-in-blob-storage.md). 
- Possono considerare i componenti dell’applicazione isolata invece che i computer.
- Possono utilizzare un numero elevato di strumenti che comprendono contenitori Docker e immagini di base diverse

### Vantaggi dei contenitori per professionisti IT e operazioni

Anche i professionisti IT e operazioni traggono benefici dalla combinazione di contenitori e macchine virtuali.

- i servizi indipendenti sono isolati dall’ambiente di esecuzione host della VM
- il codice indipendente è effettivamente identico
- i servizi indipendenti possono essere avviati, arrestati e spostati rapidamente tra gli ambienti di sviluppo, test e produzione

Funzionalità come queste&mdash;e ve ne sono di altre&mdash;attirano l’attenzione di aziende consolidate, dove le organizzazioni IT professionali hanno il compito di adattare le risorse&mdash;inclusa la pura potenza elaborazione&mdash; per le attività necessarie al fine non solo di rimanere in affari, ma anche di aumentare la soddisfazione della clientela e la portata. Aziende di piccole dimensioni, ISV e -startup hanno esattamente la stessa necessità, ma potrebbero descriverla in maniera diversa.

## Per quali scopi sono utili le macchine virtuali?

Le macchine virtuali forniscono la struttura portante del cloud computing, e questo non cambia. Se le macchine virtuali si avviano più lentamente, hanno un maggiore footprint su disco e non vengono mappati direttamente in un'architettura di microservizi, hanno dei vantaggi molto importanti:

1. Per impostazione predefinita, dispongono protezioni di sicurezza predefinite molto più solide per il computer host
2. Supportano qualsiasi sistema operativo tra i più diffusi e le configurazioni di applicazioni
3. Dispongono di ecosistemi di strumenti consolidati per il comando e il controllo
4. Forniscono l'ambiente di esecuzione ai contenitori host

Quest’ultimo elemento è importante, poiché un'applicazione indipendente richiede comunque un sistema operativo specifico e un tipo di CPU, a seconda delle chiamate che l’applicazione farà. È importante ricordare che si installano contenitori nelle VM perché questi contengono le applicazioni che si desidera distribuire. I contenitori non possono sostituire le VM o i sistemi operativi.

## Confronto a livello generale delle funzionalità delle VM e dei contenitori

La seguente tabella descrive a un livello generale le differenze in termini di funzionalità che&mdash;senza ulteriori interventi&mdash; esistono tra le VM e i contenitori Linux. Si noti che alcune funzionalità potrebbero essere più o meno vantaggiose a seconda delle proprie necessità di applicazione, e che, come accade con tutti i software, ulteriori operazioni comportano un incremento del supporto alle funzionalità, in particolare nell’area della sicurezza.

| Funzionalità | VM | Contenitori |
| :------------- |-------------| ----------- |
| Supporto alla sicurezza "predefinito" | a un livello superiore | a un livello leggermente inferiore |
| Memoria su disco richiesta | Sistema operativo completo più app | Solo requisiti app |
| Tempo richiesto per l'avvio | Notevolmente più lungo: avvio del sistema operativo più caricamento app | Notevolmente più breve: avviare solo le app perché il kernel è già in esecuzione |
| Portabilità | Portabile con preparazione adeguata | Portabile in formato di immagine; in genere più ridotto | 
| Automazione dell’immagine | Varia notevolmente a seconda del sistema operativo e delle app | [Registro di Docker](https://registry.hub.docker.com/); altri

## Creazione e gestione di gruppi di VM e contenitori

A questo punto, qualsiasi architetto, sviluppatore o specialista in operazioni IT potrebbe pensare: "Posso automatizzare TUTTO questo; questo è davvero Data-Center-As-A-Service!".

È vero e può essere qualsiasi numero di sistemi, molti dei quali sia già disponibile, in grado di gestire i gruppi di macchine virtuali di Azure e inserire codice personalizzato utilizzando gli script, spesso con la [CustomScriptingExtension per Windows](https://msdn.microsoft.com/library/azure/dn781373.aspx) o [CustomScriptingExtension per Linux](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/). È possibile automatizzare&mdash; e probabilmente è stato già fatto&mdash;le distribuzioni di Azure utilizzando gli script di PowerShell o dell’interfaccia della riga di comando di Azure [come segue](../articles/virtual-machines/virtual-machines-windows-create-powershell.md).

Queste capacità poi sono spesso migrate a strumenti come [Puppet](https://puppetlabs.com/) e [Chef](https://www.chef.io/) per automatizzare la creazione di VM su larga scala e la loro configurazione (qui sono disponibili alcuni link su [come utilizzare questi strumenti con Azure](#tools-for-working-with-containers)).

### Modelli di gruppo di risorse di Azure

Più di recente, Azure ha rilasciato l’API REST [Gestione risorse di Azure](../articles/virtual-machines/virtual-machines-windows-compare-deployment-models.md) e aggiornato gli strumenti di PowerShell e dell’interfaccia della riga di comando di Azure per utilizzarla con facilità. È possibile distribuire, modificare o ridistribuire intere topologie di applicazioni usando i [modelli di Gestione risorse di Azure](../articles/resource-group-authoring-templates.md) con l'API di Gestione risorse di Azure usando:

- il [portale di Azure utilizzando i modelli](https://github.com/Azure/azure-quickstart-templates)&mdash;si consiglia di utilizzare il pulsante "Distribuisci su Azure"
- l'[interfaccia della riga di comando di Azure](../articles/virtual-machines/virtual-machines-linux-cli-deploy-templates.md).
- i [ moduli di Azure PowerShell](../articles/virtual-machines/virtual-machines-linux-cli-deploy-templates.md)


### Distribuzione e gestione di interi gruppi di VM di Azure e contenitori

Esistono diversi sistemi più diffusi che consentono di distribuire interi gruppi di VM e installare Docker (o altri sistemi host Linux per contenitori ) su di essi come gruppo automatizzabile. Per i link diretti, vedere la sezione [contenitori e strumenti](#containers-and-vm-technologies) qui di seguito. Esistono diversi sistemi che eseguono questa operazione in misura maggiore o minore, e questo elenco non è esaustivo. A seconda delle proprie competenze e scenari, essi possono essere o meno utili.

Docker dispone del suo proprio set di strumenti per la creazione di VM ([docker-machine](../articles/virtual-machines/virtual-machines-linux-classic-docker-machine.md)) e di uno strumento di gestione del cluster del contenitore Docker per il bilanciamento del carico ([swarm](../articles/virtual-machines/virtual-machines-linux-docker-swarm.md)). Inoltre, l’[Estensione della VM Docker di Azure](https://github.com/Azure/azure-docker-extension/blob/master/README.md) è fornita di un supporto predefinito per [`docker-compose`](https://docs.docker.com/compose/), che può distribuire contenitori configurati di applicazioni all’interno di molteplici contenitori.

Inoltre, è possibile provare il [Data Center Operating System (DCOS) di Mesosphere](http://docs.mesosphere.com/install/azurecluster/). DCOS si basa sul "kernel di sistemi distribuiti" open source [mesos](http://mesos.apache.org/) che consente di trattare il datacenter come un servizio indirizzabile. DCOS dispone di pacchetti predefiniti per diversi importanti sistemi come [Spark](http://spark.apache.org/) e [Kafka](http://kafka.apache.org/) (e altri), nonché di servizi predefiniti come [Marathon](https://mesosphere.github.io/marathon/) (un sistema di controllo dei contenitori) e [Chronos](https://mesosphere.github.io/chronos/) (un'utilità di pianificazione distribuita). Mesos è nata attraverso le lezioni apprese con Twitter, AirBnb e altre aziende di scala web.

Inoltre, [kubernetes](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/) è un sistema open source per la gestione di gruppi di VM e contenitori derivato dalle lezioni apprese con Google. È possibile utilizzare [kubernetes con Weave per fornire supporto alla rete](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave).

[Deis](http://deis.io/overview/) è una "Platform-as-a-Service" (PaaS) open source che rende più semplice distribuire e gestire le applicazioni sui propri server. Deis è basato su Docker e CoreOS per fornire una PaaS semplificata con un flusso di lavoro ispirato a Heroku. È possibile [creare un gruppo di VM di Azure a 3 nodi e installare Deis](../articles/virtual-machines/virtual-machines-linux-deis-cluster.md) in Azure e quindi [installare un'applicazione Hello World Go](../articles/virtual-machines/virtual-machines-linux-deis-cluster.md#deploy-and-scale-a-hello-world-application) con facilità.

[CoreOS](./articles/virtual-machines/virtual-machines-linux-classic-coreos-howto.md), una distribuzione Linux con un footprint ottimizzato, supporto Docker e il proprio sistema contenitore chiamato [rkt](https://github.com/coreos/rkt), dispone inoltre di uno strumento di gestione del gruppo contenitore chiamato [fleet](../articles/virtual-machines/virtual-machines-linux-classic-coreos-fleet-get-started.md).

Ubuntu, un'altra distribuzione Linux molto diffuso, supporta Docker molto bene, ma supporta anche [Linux clusters (in stile LXC)](https://help.ubuntu.com/lts/serverguide/lxc.html).

## Strumenti per lavorare con VM di Azure e contenitori

Per lavorare con i contenitori e le VM di Azure sono necessari degli strumenti. In questa sezione viene fornito un elenco di solo alcuni dei più utili o importanti concetti e strumenti sui contenitori, i gruppi e i grandi strumenti per la configurazione e l’orchestrazione che vengono usati con essi.

> [AZURE.NOTE] Quest'area si sta evolvendo davvero rapidamente e nonostante facciamo del nostro meglio per mantenere aggiornato questo argomento e i relativi link, potrebbe essere un compito difficile. Si consiglia di effettuare ricerche su argomenti di interesse per rimanere aggiornati.

### Tecnologie per i contenitori e le VM

Alcune tecnologie per i contenitori di Linux:

- [Docker](https://www.docker.com)
- [LXC](https://linuxcontainers.org/)
- [CoreOS e rkt](https://github.com/coreos/rkt)
- [Open Container Project](http://opencontainers.org/)
- [RancherOS](http://rancher.com/rancher-os/)

Link ai contenitori Windows:

- [Contenitori Windows](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview)

Link a Visual Studio Docker:

- [Visual Studio 2015 RC Tools for Docker - Preview](https://visualstudiogallery.msdn.microsoft.com/6f638067-027d-4817-bcc7-aa94163338f0)

Strumenti di Docker:

- [Daemon docker](https://docs.docker.com/installation/#installation)
- Client di Docker
	- [Windows Docker Client su Chocolatey](https://chocolatey.org/packages/docker)
	- [Istruzioni per l’installazione di Docker](https://docs.docker.com/installation/#installation)


Docker su Microsoft Azure:

- [Estensione della VM Docker per Linux in Azure](../articles/virtual-machines/virtual-machines-linux-dockerextension.md)
- [Guida dell’utente di Estensione della VM Docker di Azure](https://github.com/Azure/azure-docker-extension/blob/master/README.md)
- [Uso dell'estensione della VM Docker dall'interfaccia della riga di comando di Azure (CLI di Azure)](../articles/virtual-machines/virtual-machines-linux-classic-cli-use-docker.md)
- [Uso dell'estensione VM Docker dal portale di Azure](../articles/virtual-machines/virtual-machines-linux-classic-portal-use-docker.md)
- [Introduzione rapida a Docker in Azure Marketplace](../articles/virtual-machines/virtual-machines-linux-classic-docker-quickstart.md)
- [Come usare Docker Machine in Azure](../articles/virtual-machines/virtual-machines-linux-classic-docker-machine.md)
- [Come usare Docker Swarm in Azure](../articles/virtual-machines/virtual-machines-linux-docker-swarm.md)
- [Introduzione a Docker e Compose in Azure](../articles/virtual-machines/virtual-machines-linux-docker-compose-quickstart.md)
- [Utilizzo di un modello di gruppo di risorse di Azure per creare rapidamente un host Docker in Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)
- [Il supporto incorporato per `compose`](https://github.com/Azure/azure-docker-extension#11-public-configuration-keys) le applicazioni indipendenti
- [Implementare un registro di Docker privato in Azure](virtual-machines-linux-docker-registry-in-blob-storage.md)

Distribuzioni Linux ed esempi di Azure:

- [CoreOS](../articles/virtual-machines/virtual-machines-linux-classic-coreos-howto.md)

Configurazione, gestione del cluster e orchestrazione del contenitore:

- [Flotta su CoreOS](../articles/virtual-machines/virtual-machines-linux-classic-coreos-fleet-get-started.md)

-	Deis
	- [Creare un gruppo di VM di Azure a 3 nodi, installare Deis, e avviare un'applicazione Hello World Go](../articles/virtual-machines/virtual-machines-linux-deis-cluster.md)
	
-	Kubernetes
	- [Guida completa alla distribuzione di cluster Kubernetes automatizzata con CoreOS e Weave](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)
	- [Visualizzatore Kubernetes](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/)
	
-	[Mesos](http://mesos.apache.org/)
	-	[Data Center Operating System (DCOS) di Mesosphere](http://beta-docs.mesosphere.com/install/azurecluster/)
	
-	[Jenkins](https://jenkins-ci.org/) e [Hudson](http://hudson-ci.org/)
	- [Blog: plug-in slave di Jenkins per Azure](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
	- [Repository GitHub: plug-in di archiviazione di Jenkins per Azure](https://github.com/jenkinsci/windows-azure-storage-plugin)
	- [Terze parti: plug-in slave di Hudson per Azure](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
	- [Terze parti: plug-in di archiviazione di Hudson per Azure](https://github.com/hudson3-plugins/windows-azure-storage-plugin)
	
-	[Chef](https://docs.chef.io/index.html)
	- [Chef e Macchine virtuali](../articles/virtual-machines/virtual-machines-windows-install-chef-client.md)
	- [Video: che cos'è Chef e come funziona?](https://msopentech.com/blog/2014/03/31/using-chef-to-manage-azure-resources/)

-	[Automazione di Azure](https://azure.microsoft.com/services/automation/)
	- [Video: come usare l'Automazione di Azure con VM Linux](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Automation-104-managing-Linux-and-creating-Modules-with-Joe-Levy)
	
-	Powershell DSC per Linux
    - [Blog: come eseguire PowerShell DSC per Linux](http://blogs.technet.com/b/privatecloud/archive/2014/05/19/powershell-dsc-for-linux-step-by-step.aspx)
    - [GitHub: DSC per client Docker](https://github.com/anweiss/DockerClientDSC)

## Passaggi successivi

Vedere [Docker](https://www.docker.com) e [Contenitori Windows](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview).

<!--Anchors-->
[microservices]: http://martinfowler.com/articles/microservices.html
[microservizio]: http://martinfowler.com/articles/microservices.html
<!--Image references-->

<!---HONumber=AcomDC_0330_2016-->