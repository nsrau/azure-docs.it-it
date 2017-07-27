Le soluzioni del cloud di Azure si basano sulle macchine virtuali (emulazione di componenti hardware del computer fisico) e consentono così la creazione agile di pacchetti di distribuzioni software e un migliore consolidamento delle risorse rispetto all'hardware fisico. I contenitori [Docker](https://www.docker.com) e l'ecosistema Docker hanno espanso significativamente le modalità a disposizione per sviluppare, fornire e gestire il software distribuito. Il codice applicativo presente in un contenitore è isolato dalla VM host e da altri contenitori nella stessa VM. Questo isolamento offre maggiore flessibilità di sviluppo e distribuzione.

Azure offre i valori Docker seguenti:

* [Molti](../articles/virtual-machines/linux/docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) modi [diversi](../articles/virtual-machines/linux/dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) di creare host Docker per i contenitori in base alle proprie esigenze
* Il [servizio contenitore di Azure](https://azure.microsoft.com/documentation/services/container-service/) crea cluster di host contenitori usando agenti di orchestrazione come **marathon** e **swarm**.
* [Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md) e i [modelli di gruppi di risorse](../articles/resource-group-authoring-templates.md) per semplificare la distribuzione e l'aggiornamento di applicazioni distribuite complesse
* integrazione con un'ampia gamma di  strumenti di gestione della configurazione sia proprietari che open source

E poiché è possibile creare a livello di programmazione le VM e i contenitori Linux in Azure, è inoltre possibile usare strumenti di *orchestrazione* per VM e contenitori per creare gruppi di Macchine virtuali (VM) e distribuire le applicazioni sia all'interno di contenitori Linux che, ora, all'interno di [contenitori Windows](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview).

In questo articolo non solo vengono discussi questi concetti a livello generale, ma vengono anche forniti numerosi link per accedere a maggiori informazioni, esercitazioni e prodotti correlati all’utilizzo di contenitori e cluster in Azure. Se si è già in possesso di queste informazioni, i collegamenti sono disponibili negli [strumenti per l'uso dei contenitori](#tools-for-working-with-azure-vms-and-containers).

## <a name="the-difference-between-virtual-machines-and-containers"></a>La differenza tra le macchine virtuali e i contenitori
Le macchine virtuali vengono eseguite all'interno di un ambiente di virtualizzazione hardware isolato fornito da un [hypervisor](http://en.wikipedia.org/wiki/Hypervisor). In Azure il servizio [Macchine virtuali](https://azure.microsoft.com/services/virtual-machines/) gestisce tali attività per conto dell'utente. Per creare macchine virtuali è sufficiente scegliere il sistema operativo e configurarlo e/o caricare un'immagine di macchina virtuale personalizzata. Il servizio Macchine virtuali è una tecnologia collaudata e consolidata nel tempo e sono disponibili diversi strumenti per la gestione dei sistemi operativi e delle app in esso contenuti.  Le app in una macchina virtuale vengono nascoste al sistema operativo. Dal punto di vista di un'applicazione o un utente in una macchina virtuale, quest'ultima appare come un computer fisico autonomo.

I [contenitori Linux](http://en.wikipedia.org/wiki/LXC) e quelli creati e ospitati con gli strumenti Docker non usano un hypervisor per garantire l'isolamento. L'host del contenitore usa le funzionalità di isolamento dei processi e del file system del kernel Linux per esporre al contenitore le relative app, alcune funzionalità kernel e il relativo file system isolato. Dal punto di vista di un'app in esecuzione all'interno di un contenitore, quest'ultimo appare come un'istanza univoca del sistema operativo. Un'app indipendente non può vedere processi o altre risorse all'esterno del relativo contenitore.

In un contenitore Docker vengono usate molte meno risorse rispetto a quelle usate in una macchina virtuale. I contenitori Docker fanno uso di un modello di esecuzione e isolamento dell'applicazione che non condivide il kernel dell'host Docker. Il contenitore ha una superficie molto inferiore, perché non include l'intero sistema operativo. I tempi di avvio e lo spazio su disco necessario sono notevolmente inferiori rispetto a quelli di una macchina virtuale.
I contenitori Windows offrono gli stessi vantaggi dei contenitori Linux per le app eseguite in Windows. I contenitori Windows supportano il formato di immagine Docker e l'API Docker, tuttavia possono essere gestiti anche tramite PowerShell. Due runtime contenitore sono disponibili con i contenitori Windows, i contenitori Windows Server e i contenitori Hyper-V. I contenitori Hyper-V offrono un ulteriore livello di isolamento, perché ogni contenitore è ospitato in una macchina virtuale altamente ottimizzata. Per ulteriori informazioni sui contenitori Windows, vedere [Informazioni sui contenitori Windows](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview). Per iniziare a usare i contenitori di Windows in Azure, vedere come [distribuire un cluster del servizio contenitore di Azure](../articles/container-service/dcos-swarm/container-service-deployment.md).

## <a name="what-are-containers-good-for"></a>Per quale scopo sono utili i contenitori?

I contenitori permettono di migliorare quanto segue:

* Velocità dello sviluppo e della condivisione estesa del codice dell'applicazione.
* Velocità e affidabilità del testing di un'applicazione.
* Velocità e affidabilità della distribuzione di un'applicazione.

I contenitori vengono eseguiti in un host contenitore o un sistema operativo. In Azure si tratta di una macchina virtuale di Azure. Anche se si sta già apprezzando l'idea dei contenitori, sarà comunque necessario disporre di un'infrastruttura di VM che ospiti i contenitori, ma il vantaggio è che non occorre determinare su quale VM vengono eseguiti i contenitori (anche se, nel caso in cui il contenitore richieda un ambiente di esecuzione di Linux o Windows, sarà importante tenerlo presente, ad esempio).


## <a name="what-are-containers-good-for"></a>Per quale scopo sono utili i contenitori?
I contenitori sono utili per molti scopi, ad esempio consentono&mdash;come [Servizi Cloud di Azure](https://azure.microsoft.com/services/cloud-services/) e [Azure Service Fabric](../articles/service-fabric/service-fabric-overview.md)&mdash;la creazione di applicazioni distribuite con singoli servizi e orientate ai microservizi, in cui il design dell'applicazione si basa su parti più piccole e componibili e non su componenti più grandi e ad accoppiamento avanzato.

Ciò vale soprattutto in ambienti di cloud pubblico come Azure, in cui in si possono affittare VM quando e dove si desidera. Non solo si ottengono un isolamento, una rapida distribuzione e gli strumenti di orchestrazione, ma è anche possibile prendere decisioni più efficienti sull’infrastruttura delle applicazioni.

Ad esempio, si potrebbe disporre attualmente di una distribuzione costituita da 9 VM di Azure di grandi dimensioni per un'applicazione distribuita a disponibilità elevata. Se i componenti di questa applicazione possono essere distribuiti in contenitori, sarà possibile utilizzare solo 4 VM e distribuire i componenti dell'applicazione all'interno di 20 contenitori per garantire la ridondanza e il bilanciamento del carico.

Questo è solo un esempio, naturalmente, ma se si riesce ad attuarlo nel proprio scenario, è possibile regolare i picchi di utilizzo con più contenitori anziché con più VM di Azure e utilizzare il carico complessivo della CPU rimanente con molta più efficienza di prima.

Inoltre, esistono molti scenari che non si prestano a un approccio di microservizi; sarà possibile capire meglio se i microservizi e i contenitori sono di aiuto.

### <a name="container-benefits-for-developers"></a>Vantaggi dei contenitori per gli sviluppatori
In generale, è facile rendersi conto che la tecnologia dei contenitori è un passo in avanti, ma esistono anche vantaggi più specifici. Esaminiamo l'esempio dei contenitori Docker. Questo argomento non descrive approfonditamente Docker (a tale scopo vedere le [informazioni su Docker](https://www.docker.com/whatisdocker/) o consultare [Wikipedia](http://wikipedia.org/wiki/Docker_%28software%29)), ma Docker e il relativo ecosistema offrono notevoli vantaggi agli sviluppatori e ai professionisti IT.

Gli sviluppatori familiarizzano velocemente con i contenitori di Docker, perché in particolare ciò rende semplice l'uso di contenitori Windows e Linux:

* Gli sviluppatori possono usare comandi semplici e incrementali per creare un'immagine fissa facile da distribuire e possono automatizzare la creazione di tali immagini utilizzando un dockerfile
* Gli sviluppatori possono condividere facilmente le immagini usando semplici comandi push e pull in stile [git](https://git-scm.com/) in registri Docker [pubblici](https://registry.hub.docker.com/) o [privati](../articles/virtual-machines/virtual-machines-linux-docker-registry-in-blob-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Possono considerare i componenti dell’applicazione isolata invece che i computer.
* Possono utilizzare un numero elevato di strumenti che comprendono contenitori Docker e immagini di base diverse

### <a name="container-benefits-for-operations-and-it-professionals"></a>Vantaggi dei contenitori per professionisti IT e operazioni
Anche i professionisti IT e operazioni traggono benefici dalla combinazione di contenitori e macchine virtuali.

* i servizi indipendenti sono isolati dall’ambiente di esecuzione host della VM
* il codice indipendente è effettivamente identico
* i servizi indipendenti possono essere avviati, arrestati e spostati rapidamente tra gli ambienti di sviluppo, test e produzione

Funzionalità come queste&mdash;e ve ne sono altre&mdash;suscitano l'interesse di organizzazioni IT professionali consolidate che hanno il compito di adattare le risorse&mdash;inclusa la pura potenza di elaborazione&mdash;alle attività necessarie non solo per continuare a operare sul mercato, ma anche per aumentare la soddisfazione dei clienti e offrire loro una migliore assistenza. Aziende di piccole dimensioni, ISV e -startup hanno esattamente la stessa necessità, ma potrebbero descriverla in maniera diversa.

## <a name="what-are-virtual-machines-good-for"></a>Per quali scopi sono utili le macchine virtuali?
Le macchine virtuali forniscono la struttura portante del cloud computing, e questo non cambia. Se le macchine virtuali si avviano più lentamente, hanno un maggiore footprint su disco e non vengono mappati direttamente in un'architettura di microservizi, hanno dei vantaggi molto importanti:

1. Per impostazione predefinita, dispongono protezioni di sicurezza predefinite molto più solide per il computer host
2. Supportano qualsiasi sistema operativo tra i più diffusi e le configurazioni di applicazioni
3. Dispongono di ecosistemi di strumenti consolidati per il comando e il controllo
4. Forniscono l'ambiente di esecuzione ai contenitori host

Quest’ultimo elemento è importante, poiché un'applicazione indipendente richiede comunque un sistema operativo specifico e un tipo di CPU, a seconda delle chiamate che l’applicazione farà. È importante ricordare che si installano contenitori nelle VM perché questi contengono le applicazioni che si desidera distribuire. I contenitori non possono sostituire le VM o i sistemi operativi.

## <a name="high-level-feature-comparison-of-vms-and-containers"></a>Confronto a livello generale delle funzionalità delle VM e dei contenitori
La tabella seguente descrive a un livello generale le differenze in termini di funzionalità presenti&mdash;senza particolari operazioni aggiuntive&mdash;tra le VM e i contenitori Linux. Si noti che alcune funzionalità potrebbero essere più o meno vantaggiose a seconda delle proprie necessità di applicazione, e che, come accade con tutti i software, ulteriori operazioni comportano un incremento del supporto alle funzionalità, in particolare nell’area della sicurezza.

| Funzionalità | VM | Contenitori |
|:--- | --- | --- |
| Supporto alla sicurezza "predefinito" |a un livello superiore |a un livello leggermente inferiore |
| Memoria su disco richiesta |Sistema operativo completo più app |Solo requisiti app |
| Tempo richiesto per l'avvio |Notevolmente più lungo: avvio del sistema operativo più caricamento app |Notevolmente più breve: avviare solo le app perché il kernel è già in esecuzione |
| Portabilità |Portabile con preparazione adeguata |Portabile in formato di immagine; in genere più ridotto |
| Automazione dell’immagine |Varia notevolmente a seconda del sistema operativo e delle app |[Registro di Docker](https://registry.hub.docker.com/); altri |

## <a name="creating-and-managing-groups-of-vms-and-containers"></a>Creazione e gestione di gruppi di VM e contenitori
A questo punto, qualsiasi architetto, sviluppatore o specialista in operazioni IT potrebbe pensare: "Posso automatizzare TUTTO questo; questo è davvero Data-Center-As-A-Service!".

È vero, può esserlo, e un numero infinito di sistemi, molti dei quali magari già in uso, possono gestire gruppi di macchine virtuali di Azure e inserire codice personalizzato usando script, spesso con [CustomScriptingExtension per Windows](https://msdn.microsoft.com/library/azure/dn781373.aspx) o [CustomScriptingExtension per Linux](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/). È possibile&mdash;e probabilmente l'operazione è già stata eseguita&mdash;automatizzare le distribuzioni di Azure usando gli script di PowerShell o dell'interfaccia della riga di comando di Azure.

Queste funzionalità vengono poi spesso trasferite a strumenti come [Puppet](https://puppetlabs.com/) e [Chef](https://www.chef.io/) per automatizzare la creazione e la configurazione delle macchine virtuali su larga scala. Qui sono disponibili alcuni collegamenti relativi all'[uso di questi strumenti con Azure](#tools-for-working-with-containers).

### <a name="azure-resource-group-templates"></a>Modelli di gruppo di risorse di Azure
Più di recente, Azure ha rilasciato l'API REST di [Azure Resource Manager](../articles/resource-manager-deployment-model.md) e aggiornato gli strumenti di PowerShell e dell'interfaccia della riga di comando di Azure per semplificarne l'uso. È possibile distribuire, modificare o ridistribuire intere topologie di applicazioni usando i [modelli di Gestione risorse di Azure](../articles/resource-group-authoring-templates.md) con l'API di Gestione risorse di Azure usando:

* il [portale di Azure con i modelli](https://github.com/Azure/azure-quickstart-templates)&mdash;si consiglia di usare il pulsante "Distribuisci in Azure"
* l'[interfaccia della riga di comando di Azure](../articles/virtual-machines/linux/cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* i [moduli di Azure PowerShell](../articles/virtual-machines/linux/cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="deployment-and-management-of-entire-groups-of-azure-vms-and-containers"></a>Distribuzione e gestione di interi gruppi di VM di Azure e contenitori
Esistono diversi sistemi più diffusi che consentono di distribuire interi gruppi di VM e installare Docker (o altri sistemi host Linux per contenitori ) su di essi come gruppo automatizzabile. Per i link diretti, vedere la sezione [contenitori e strumenti](#containers-and-vm-technologies) qui di seguito. Esistono diversi sistemi che eseguono questa operazione in misura maggiore o minore, e questo elenco non è esaustivo. A seconda delle proprie competenze e scenari, essi possono essere o meno utili.

Docker mette a disposizione un proprio set di strumenti per la creazione di VM ([docker-machine](../articles/virtual-machines/linux/docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) e uno strumento di gestione del cluster del contenitore Docker per il bilanciamento del carico ([swarm](../articles/virtual-machines/virtual-machines-linux-docker-swarm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). L'[estensione della VM Docker di Azure](https://github.com/Azure/azure-docker-extension/blob/master/README.md) mette anche a disposizione il supporto predefinito per [`docker-compose`](https://docs.docker.com/compose/), che può distribuire contenitori configurati di applicazioni in più contenitori.

Inoltre, è possibile provare il [Data Center Operating System (DCOS) di Mesosphere](http://docs.mesosphere.com/install/azurecluster/). DCOS si basa sul "kernel di sistemi distribuiti" open source [mesos](http://mesos.apache.org/) che consente di trattare il datacenter come un servizio indirizzabile. DCOS mette a disposizione pacchetti predefiniti per diversi importanti sistemi come [Spark](http://spark.apache.org/) e [Kafka](http://kafka.apache.org/) (e altri), nonché servizi predefiniti come [Marathon](https://mesosphere.github.io/marathon/) (un sistema di controllo dei contenitori) e [Chronos](https://mesos.github.io/chronos/) (un'utilità di pianificazione distribuita). Mesos è nata attraverso le lezioni apprese con Twitter, AirBnb e altre aziende di scala web. È anche possibile usare **swarm** come motore di orchestrazione.

Inoltre, [kubernetes](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/) è un sistema open source per la gestione di gruppi di VM e contenitori derivato dalle lezioni apprese con Google. È possibile utilizzare [kubernetes con Weave per fornire supporto alla rete](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave).

[Deis](http://deis.io/overview/) è una "Platform-as-a-Service" (PaaS) open source che rende più semplice distribuire e gestire le applicazioni sui propri server. Deis è basato su Docker e CoreOS per fornire una PaaS semplificata con un flusso di lavoro ispirato a Heroku. È possibile [creare un gruppo di VM di Azure a 3 nodi e installare Deis](../articles/virtual-machines/linux/deis-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) in Azure e quindi [installare un'applicazione Hello World Go](../articles/virtual-machines/linux/deis-cluster.md#deploy-and-scale-a-hello-world-application) in modo semplice.

[CoreOS](https://coreos.com/os/docs/latest/booting-on-azure.html), una distribuzione Linux con un footprint ottimizzato, supporto Docker e un proprio sistema contenitore denominato [rkt](https://github.com/coreos/rkt), mette anche a disposizione uno strumento di gestione del gruppo di contenitori denominato [fleet](https://coreos.com/using-coreos/clustering/).

Ubuntu, un'altra distribuzione Linux molto diffuso, supporta Docker molto bene, ma supporta anche [Linux clusters (in stile LXC)](https://help.ubuntu.com/lts/serverguide/lxc.html).

## <a name="tools-for-working-with-azure-vms-and-containers"></a>Strumenti per lavorare con VM di Azure e contenitori
Per lavorare con i contenitori e le VM di Azure sono necessari degli strumenti. In questa sezione viene fornito un elenco di solo alcuni dei più utili o importanti concetti e strumenti sui contenitori, i gruppi e i grandi strumenti per la configurazione e l’orchestrazione che vengono usati con essi.

> [!NOTE]
> Quest'area si sta evolvendo davvero rapidamente e nonostante facciamo del nostro meglio per mantenere aggiornato questo argomento e i relativi link, potrebbe essere un compito difficile. Si consiglia di effettuare ricerche su argomenti di interesse per rimanere aggiornati.
>
>

### <a name="containers-and-vm-technologies"></a>Tecnologie per i contenitori e le VM
Alcune tecnologie per i contenitori di Linux:

* [Docker](https://www.docker.com)
* [LXC](https://linuxcontainers.org/)
* [CoreOS e rkt](https://github.com/coreos/rkt)
* [Open Container Project](http://opencontainers.org/)
* [RancherOS](http://rancher.com/rancher-os/)

Link ai contenitori Windows:

* [Contenitori Windows](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview)

Link a Visual Studio Docker:

* [Visual Studio 2015 RC Tools for Docker - Preview](https://visualstudiogallery.msdn.microsoft.com/6f638067-027d-4817-bcc7-aa94163338f0)

Strumenti di Docker:

* [Daemon docker](https://docs.docker.com/installation/#installation)
* Client di Docker
  * [Windows Docker Client su Chocolatey](https://chocolatey.org/packages/docker)
  * [Istruzioni per l’installazione di Docker](https://docs.docker.com/installation/#installation)

Docker su Microsoft Azure:

* [Estensione della VM Docker per Linux in Azure](../articles/virtual-machines/linux/dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Guida dell’utente di Estensione della VM Docker di Azure](https://github.com/Azure/azure-docker-extension/blob/master/README.md)
* [Uso dell'estensione della VM Docker dall'interfaccia della riga di comando di Azure (CLI di Azure)](../articles/virtual-machines/linux/classic/cli-use-docker.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Uso dell'estensione VM Docker dal portale di Azure](../articles/virtual-machines/linux/classic/portal-use-docker.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Come usare Docker Machine in Azure](../articles/virtual-machines/linux/docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Come usare Docker con swarm in Azure](../articles/virtual-machines/virtual-machines-linux-docker-swarm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Introduzione a Docker e Compose in Azure](../articles/virtual-machines/linux/docker-compose-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Utilizzo di un modello di gruppo di risorse di Azure per creare rapidamente un host Docker in Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)
* [Supporto predefinito per `compose`](https://github.com/Azure/azure-docker-extension#11-public-configuration-keys) per le applicazioni contenute
* [Implementare un registro di Docker privato in Azure](../articles/virtual-machines/virtual-machines-linux-docker-registry-in-blob-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Distribuzioni Linux ed esempi di Azure:

* [CoreOS](https://coreos.com/os/docs/latest/booting-on-azure.html)

Configurazione, gestione del cluster e orchestrazione del contenitore:

* [Flotta su CoreOS](https://coreos.com/using-coreos/clustering/)
* Deis

  * [Creare un gruppo di VM di Azure a 3 nodi, installare Deis e avviare un'applicazione Hello World Go](../articles/virtual-machines/linux/deis-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* kubernetes

  * [Guida completa alla distribuzione di cluster Kubernetes automatizzata con CoreOS e Weave](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)
  * [Visualizzatore Kubernetes](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/)
* [Mesos](http://mesos.apache.org/)

  * [Data Center Operating System (DCOS) di Mesosphere](http://beta-docs.mesosphere.com/install/azurecluster/)
* [Jenkins](https://jenkins-ci.org/) e [Hudson](http://hudson-ci.org/)

  * [Blog: plug-in slave di Jenkins per Azure](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
  * [Repository GitHub: plug-in di archiviazione di Jenkins per Azure](https://github.com/jenkinsci/windows-azure-storage-plugin)
  * [Terze parti: plug-in slave di Hudson per Azure](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
  * [Terze parti: plug-in di archiviazione di Hudson per Azure](https://github.com/hudson3-plugins/windows-azure-storage-plugin)
* [Automazione di Azure](https://azure.microsoft.com/services/automation/)

  * [Video: come usare l'Automazione di Azure con VM Linux](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Automation-104-managing-Linux-and-creating-Modules-with-Joe-Levy)
* Powershell DSC per Linux

  * [Blog: come eseguire PowerShell DSC per Linux](http://blogs.technet.com/b/privatecloud/archive/2014/05/19/powershell-dsc-for-linux-step-by-step.aspx)
  * [GitHub: DSC per client Docker](https://github.com/anweiss/DockerClientDSC)

## <a name="next-steps"></a>Passaggi successivi
Vedere [Docker](https://www.docker.com) e [Contenitori Windows](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview).

<!--Anchors-->
[microservices]: http://martinfowler.com/articles/microservices.html
[microservice]: http://martinfowler.com/articles/microservices.html
<!--Image references-->
