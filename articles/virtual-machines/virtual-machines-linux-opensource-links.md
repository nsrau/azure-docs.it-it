<properties
	pageTitle="Computing Linux e open source in Azure | Microsoft Azure"
	description="Elenco relativo ad articoli sul computing Linux e open source in Azure, incluse informazioni di base sull'utilizzo di Linux, alcuni concetti fondamentali sull'esecuzione o il caricamento di immagini Linux in Azure e altri contenuti relativi a tecnologie e ottimizzazioni specifiche."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="01/03/2016"
	ms.author="rasquill"/>



# Computing Linux e open source in Azure

Lo scopo di questo documento consiste nell'offrire un elenco completo di tutti gli argomenti scritti da Microsoft e dai partner sull'esecuzione di macchine virtuali basate su Linux e altri ambienti di calcolo e applicazioni open source in Microsoft Azure. È stata aggiunta una nota sia agli articoli che descrivono solo il modello di distribuzione classica sia a quelli che usano il modello di distribuzione di Gestione risorse. Gli articoli per i quali non è presente una nota sul modello di distribuzione descrivono entrambi i modelli.

Poiché sia Azure sia l'ambito del computing open source sono in continua evoluzione, il documento risulterà quasi sicuramente non aggiornato, *nonostante* il constante impegno di Microsoft nell'aggiunta degli argomenti più recenti e nella rimozione di quelli obsoleti. Segnalare eventuali articoli da aggiungere inserendo un commento oppure inviare una richiesta di rimozione al [repository Github](https://github.com/Azure/azure-content/).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## Note generali
Le sezioni sono suddivise sulla destra della pagina. È possibile che alcuni collegamenti siano disponibili in più sezioni, in quanto relativi a più concetti, distribuzioni o tecnologie. Sono anche disponibili alcuni argomenti che descrivono le diverse opzioni di Linux, i repository di immagini, i case study e le procedure pratiche per il caricamento di immagini personalizzate:

- [Azure Marketplace](https://azure.microsoft.com/marketplace/virtual-machines/)
- [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index)
- [Eventi e dimostrazioni: Microsoft Openness CEE](http://www.opennessatcee.com/)
- [Distribuzione classica]: [Procedura: Caricamento di un'immagine di distribuzione personalizzata](virtual-machines-linux-classic-create-upload-vhd.md) (include anche istruzioni per l'uso di una [distribuzione supportata da Azure](virtual-machines-linux-endorsed-distros.md))
- [Note: requisiti generali per l'esecuzione di Linux in Azure](virtual-machines-linux-create-upload-generic.md)
- [Distribuzione classica]: [Note: Introduzione generale a Linux in Azure](virtual-machines-linux-intro-on-azure.md)

<!--
- [Distros](#distros) &mdash; Topics to do with a specific distro.
- [The Basics](#basics) &mdash; A lot of the basic things to do that you either know or need to know.
- [Community Images and Repositories](#images) &mdash; Other places for very useful information, repositories, and binaries.
- [Languages and Platforms](#langsandplats)
- [Samples and Scripts](#samples)
- [Auth and Encryption](#security) &mdash; Important security-related topics, not necessarily specific to Azure.
- [Devops, Management, and Optimization](#devops) &mdash; A big category, changing rapidly.
- [Support, Troubleshooting, and "It Just Doesn't Work"](#supportdebug) &mdash; Really.
-->

## Distribuzioni

Sono disponibili moltissime distribuzioni di Linux, suddivise in genere in base ai sistemi di gestione dei pacchetti: alcuni sono basati su dpkg, ad esempio Debian e Ubuntu, mentre altri sono basati su rpm, quali CentOS, SUSE e RedHat. Alcune società offrono immagini di distribuzione in qualità di partner Microsoft ufficiali e queste distribuzioni sono approvate. Altre distribuzioni sono fornite dalla community. Alle distribuzioni incluse in questa sezione sono associati articoli ufficiali, anche se sono state usate solo in esempi di altre tecnologie.

### [Ubuntu](https://azure.microsoft.com/marketplace/partners/Canonical/)

Ubuntu è una distribuzione Linux approvata da Azure molto diffusa ed è basata sulla gestione dei pacchetti dpkg e apt-get.

1. [Procedura: caricare la propria immagine di Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)
2. [Procedura: stack LAMP di Ubuntu](virtual-machines-linux-install-lamp-stack.md)
3. [Distribuzione classica]: [Procedura: Cluster MySQL](virtual-machines-linux-classic-mysql-cluster.md)
4. [Distribuzione classica]: [Procedura: Node.js e Cassandra](virtual-machines-linux-classic-cassandra-nodejs.md)
5. [Distribuzione di Gestione risorse]: [Procedura: IPython Notebook](virtual-machines-linux-jupyter-notebook.md)
6. [Distribuzione classica]: [Utenti esperti: Esecuzione di ASP.NET 5 su Linux usando contenitori Docker](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)


### [Debian](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=Debian)

Debian è un'importante distribuzione per l'ambito Linux e open source basata sulla gestione di pacchetti dpgk e apt-get. In MSOpenTech VM Depot sono disponibili alcune immagini da usare.

### CentOS

La distribuzione Linux CentOS è una piattaforma stabile, prevedibile, gestibile e riproducibile derivata da Red Hat Enterprise Linux (RHEL).

1. [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=centos)
2. [Raccolta di immagini](https://azure.microsoft.com/marketplace/partners/OpenLogic/)
3. [Procedura: preparare una macchina virtuale personalizzata basata su CentOS per Azure](virtual-machines-linux-create-upload-centos.md)
4. [Distribuzione classica]: [Blog su come distribuire un'immagine di VM CentOS da OpenLogic](https://azure.microsoft.com/blog/2013/01/11/deploying-openlogic-centos-images-on-windows-azure-virtual-machines/)
6. [Distribuzione classica]: [Procedura: Installare Apache Qpid Proton-C per AMQP e bus di servizio](../service-bus/service-bus-amqp-apache.md/)

### SUSE Linux Enterprise Server e openSUSE

9. [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=OpenSUSE)
11. [Distribuzione classica]: [Procedura: Installare ed eseguire MySQL](virtual-machines-linux-classic-mysql-on-opensuse.md)
12. [Procedura: preparare una macchina virtuale SLES oppure openSUSE personalizzata](virtual-machines-linux-suse-create-upload-vhd.md)  
13. [[Forum SUSE] Procedura: Passare a un nuovo server delle patch](https://forums.suse.com/showthread.php?5622-New-Update-Infrastructure)
14. [Immagini: SUSE Linux Enterprise Server per la SAP Cloud Appliance Library](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver11sp3forsapcloudappliance/)

### CoreOS

CoreOS è una distribuzione di piccole dimensioni, ottimizzata per la pura scalabilità di calcolo con un livello elevato di controllo per la personalizzazione.

10. [Raccolta di immagini](https://azure.microsoft.com/marketplace/partners/coreos/)  
11. [Distribuzione classica]: [Procedura: Usare CoreOS in Azure](virtual-machines-linux-classic-coreos-howto.md)
12. [Distribuzione classica]: [Procedura: Guida introduttiva a fleet e Docker su CoreOS in Azure](virtual-machines-linux-classic-coreos-fleet-get-started.md)


#### [Oracle Linux](https://azure.microsoft.com/marketplace/?term=Oracle+Linux)
  2. [Preparare una macchina virtuale Oracle Linux per Azure](virtual-machines-linux-oracle-create-upload-vhd.md)

### FreeBSD

12. [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index?sort=Date&search=FreeBSD)
13. [Distribuzione classica]: [Blog sull'esecuzione di FreeBSD in Azure](https://azure.microsoft.com/blog/2014/05/22/running-freebsd-in-azure/)
14. [Distribuzione classica]: [Blog: Distribuire facilmente FreeBSD](http://msopentech.com/blog/2014/10/24/easy-deploy-freebsd-microsoft-azure-vm-depot/)
15. [Blog: distribuzione di un'immagine personalizzata di FreeBSD](http://msopentech.com/blog/2014/05/14/deploy-customize-freebsd-virtual-machine-image-microsoft-azure/)
17. [Procedura: installare l'agente Linux di Azure](virtual-machines-linux-agent-user-guide.md)
18. [Marketplace: Kaspersky AV per Linux File Server](https://azure.microsoft.com/marketplace/partners/kaspersky-lab/kav-for-lfs-kav-for-lfs/)

## Nozioni di base

1. [Nozioni di base: Interfaccia della riga di comando di Azure (CLI di Azure)](../xplat-cli-install.md)
<!-- 4. [The basics: Certificate Use and Management](http://msdn.microsoft.com/library/azure/gg981929.aspx)-->
5. [Nozioni di base: Selezione dei nomi utente per Linux](virtual-machines-linux-usernames.md)
6. [Distribuzione classica]: [Nozioni di base: Accedere a una VM Linux con il portale di Azure classico](virtual-machines-linux-classic-log-on.md)
7. [Nozioni di base: SSH](virtual-machines-linux-ssh-from-linux.md)
8. [Distribuzione classica]: [Nozioni di base: Come reimpostare una password o le proprietà SSH per Linux](virtual-machines-linux-classic-reset-access.md)
9. [Nozioni di base: Uso di privilegi root](virtual-machines-linux-use-root-privileges.md)
10. [Distribuzione classica]: [Nozioni di base: Collegare un disco dati a una VM Linux](virtual-machines-linux-classic-attach-disk.md)
11. [Distribuzione classica]: [Nozioni di base: Scollegare un disco dati a una VM Linux](virtual-machines-linux-classic-detach-disk.md)
12. [Blog sulle nozioni di base: Ottimizzazione di archiviazione, dischi e prestazioni con Linux e Azure](http://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx)
13. [Nozioni di base: RAID](virtual-machines-linux-configure-raid.md)
14. [Distribuzione classica]: [Nozioni di base: Acquisizione di una VM Linux per creare un modello](virtual-machines-linux-classic-capture-image.md)
15. [Nozioni di base: Agente Linux di Azure](virtual-machines-linux-agent-user-guide.md)
16. [Nozioni di base: Estensioni VM e funzionalità di Azure](virtual-machines-windows-extensions-features.md)
17. [Distribuzione classica]: [Nozioni di base: Inserimento di dati personalizzati in una VM per l'uso con Cloud-Init](virtual-machines-windows-classic-inject-custom-data.md)
18. [Distribuzione classica]: [Blog sulle nozioni di base: Creazione di server Linux a disponibilità elevata in Azure in 12 passaggi](http://blogs.technet.com/b/keithmayer/archive/2014/10/03/quick-start-guide-building-highly-available-linux-servers-in-the-cloud-on-microsoft-azure.aspx)
19. [Distribuzione classica]: [Blog sulle nozioni di base: Automatizzare il provisioning di Linux in Azure con l'interfaccia della riga di comando di Azure, Node.js, JHawk](http://blogs.technet.com/b/keithmayer/archive/2014/11/24/step-by-step-automated-provisioning-for-linux-in-the-cloud-with-microsoft-azure-xplat-cli-json-and-node-js-part-1.aspx)
20. [Nozioni di base: Estensione della VM Docker di Azure](virtual-machines-linux-dockerextension.md)
23. [Distribuzione classica]: [Riferimento all'API REST di gestione dei servizi di Azure](https://msdn.microsoft.com/library/azure/ee460799.aspx)
24. [Distribuzione classica]: [GlusterFS in Azure](http://dastouri.azurewebsites.net/gluster-on-azure-part-1/)

## Repository e immagini della community
3. [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index) &mdash; per immagini di macchine virtuali fornite dalla community.
4. [GitHub](https://github.com/Azure/) &mdash; per l'interfaccia della riga di comando di Azure e molti altri strumenti e progetti.
5. [Docker Hub Registry](https://registry.hub.docker.com/) &mdash; il registro per immagini dei contenitori Docker.

## Linguaggi e piattaforme
### [Centro per sviluppatori Java in Azure](https://azure.microsoft.com/develop/java/)

1. [Immagini](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=java)
2. [Procedura: usare il bus di servizio da Java con AMQP 1.0](http://msdn.microsoft.com/library/azure/jj841073.aspx)
3. [Procedura: configurare Tomcat7 in Linux mediante il portale di Azure classico](virtual-machines-linux-classic-setup-tomcat.md)
4. [Video: SDK Java di Azure per la gestione dei servizi](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-157-The-Java-SDK-for-Azure-Management-with-Brady-Gaster)
5. [Blog: introduzione ad Azure Management Libraries per Java](https://azure.microsoft.com/blog/2014/09/15/getting-started-with-the-azure-java-management-libraries/)
5. [Repository GitHub: toolkit di Azure per Eclipse con Java](https://github.com/MSOpenTech/WindowsAzureToolkitForEclipseWithJava)
6. [Riferimento: toolkit di Azure per Eclipse con Java](http://msdn.microsoft.com/library/azure/hh694271.aspx)
7. [Repository GitHub: plug-in MS Open Tech Tools per IntelliJ IDEA e Android Studio](https://github.com/MSOpenTech/msopentech-tools-for-intellij)
7. [Blog: contributi di MSOpenTech a OpenJDK](http://msopentech.com/blog/2014/10/21/ms-open-techs-first-contribution-openjdk/)
8. [Immagini: WebSphere](https://azure.microsoft.com/marketplace/partners/msopentech/was-8-5-was-8-5-5-3/)
9. [Immagini: WebLogic](https://azure.microsoft.com/marketplace/?term=weblogic)


### Linguaggi JVM

1. [Scala: esecuzione di applicazioni Play Framework nei servizi cloud di Azure](http://msopentech.com/blog/2014/09/25/tutorial-running-play-framework-applications-microsoft-azure-cloud-services-2/)

### Tipi, installazioni e aggiornamenti di SDK
4. [Azure Service Management SDK: Java](http://dl.windowsazure.com/javadoc/)
5. [Azure Service Management SDK: Go](https://github.com/MSOpenTech/azure-sdk-for-go)
5. [Azure Service Management SDK: Ruby](https://github.com/MSOpenTech/azure-sdk-for-ruby)
    - [Procedura: installare Ruby on Rails](virtual-machines-linux-classic-ruby-rails-web-app.md)
6. [Azure Service Management SDK: Python](https://github.com/Azure/azure-sdk-for-python)
    - [Procedura: applicazione Web Hello World Django (Mac-Linux)](virtual-machines-linux-python-django-web-app.md)
7. [Azure Service Management SDK: Node.js](https://github.com/MSOpenTech/azure-sdk-for-node)
8. [Azure Service Management SDK: PHP](https://github.com/MSOpenTech/azure-sdk-for-php)
    - [Procedura: installare lo stack LAMP in una macchina virtuale di Azure](virtual-machines-linux-install-lamp-stack.md)
    - [Video: installare lo stack LAMP in una macchina virtuale di Azure](http://channel9.msdn.com/Shows/Azure-Friday/LAMP-stack-on-Azure-VMs-with-Guy-Bowerman)
9. [Azure Service Management SDK: .NET](https://github.com/Azure/azure-sdk-for-net)
10. [Blog: Mono, ASP.NET 5, Linux e Docker](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)

## Esempi e script

Esaminare questa sezione per compilare rapidamente. In caso di suggerimenti, inviare una richiesta di pull o specificarli nella sezione Commenti riportata di seguito.

2. [Repository Github di Patrick Chanezon per Linux in Azure](https://github.com/chanezon/azure-linux)
3. [Video: come spostare dati USB locali da Linux ad Azure usando **usbip**](http://channel9.msdn.com/Blogs/Open/On-premises-USB-devices-on-Linux-on-Azure-via-usbip)
4. [Video: accesso all'interfaccia grafica basata su Linux in Azure nel browser con fernapp](http://channel9.msdn.com/Blogs/Open/Accessing-Linux-based-GUI-on-Azure-over-browser-with-fernapp)
5. [Video: archiviazione condivisa in Linux mediante l'anteprima file di Azure -- Parte 1](http://channel9.msdn.com/Blogs/Open/Shared-storage-on-Linux-via-Azure-Files-Preview-Part-1)
6. [Video: uso dei dispositivi Linux in Azure mediante il bus di servizio e i siti Web](http://channel9.msdn.com/Blogs/Open/Embracing-Linux-devices-on-Azure-via-Service-Bus-and-Web-Sites)
7. [Video: connessione ad Azure di un'applicazione nativa e basata su Linux memorizzata nella cache](http://channel9.msdn.com/Blogs/Open/Connecting-a-Linux-based-native-memcache-application-to-Windows-Azure)
8. [Video: bilanciamento del carico di servizi Linux a disponibilità elevata in Azure: OpenLDAP e MySQL](http://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL)


## Dati

Questa sezione include informazioni su diversi approcci e tecnologie di archiviazione, inclusi database NoSQL, archivi relazionali e Big Data.

### NoSQL

1. [Blog: 8 database NoSql open source per Azure](http://openness.microsoft.com/blog/2014/11/03/open-source-nosql-databases-microsoft-azure/)
2. Couchdb
    - [Slideshare (MSOpenTech): esperienze con CouchDb in Azure](http://www.slideshare.net/brianbenz/experiences-using-couchdb-inside-microsofts-azure-team)
    - [Blog: esecuzione di CouchDB-as-a-Service con node.js, CORS e Grunt](http://msopentech.com/blog/2013/12/19/tutorial-building-multi-tier-windows-azure-web-application-use-cloudants-couchdb-service-node-js-cors-grunt-2/)
3. MongoDB
    - [Procedura: creare un'applicazione Node.js in Azure con MongoDB mediante il componente aggiuntivo MongoLab](../app-service-web/web-sites-dotnet-store-data-mongodb-vm.md)
4. Cassandra
    - [How to: esecuzione di Cassandra con Linux in Azure e accesso da Node.js](virtual-machines-linux-classic-cassandra-nodejs.md)
5. Redis
    - [Blog: Redis in Windows nel servizio Cache Redis di Azure](http://msopentech.com/blog/2014/05/12/redis-on-windows/)
    - [Blog: annuncio del provider di stato della sessione ASP.NET per la versione di anteprima di Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)
6. RavenHQ
    - [Blog: RavenHQ ora disponibile in Azure Marketplace](https://azure.microsoft.com/blog/2014/08/12/ravenhq-now-available-in-the-azure-store/)

### Big Data
2. Hadoop/Cloudera  
	- [Blog: installazione di Hadoop nelle macchine virtuali Linux di Azure](http://blogs.msdn.com/b/benjguin/archive/2013/04/05/how-to-install-hadoop-on-windows-azure-linux-virtual-machines.aspx)
3. [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/): un servizio Hadoop completamente gestito in Azure.

### Database relazionale
2. MySQL
    - [Procedura: installare ed eseguire MySQL](virtual-machines-linux-classic-mysql-on-opensuse.md)
    - [Procedura: ottimizzare le prestazioni di MySQL in Azure](virtual-machines-linux-classic-optimize-mysql.md)
    - [Procedura: cluster MySQL](virtual-machines-linux-classic-mysql-cluster.md)
    - [Procedura: creare un database MySQL usando il Marketplace](../store-php-create-mysql-database.md)
    - [Procedura: Django e MySQL in Siti Web di Azure con Python e Visual Studio](../app-service-web/web-sites-python-ptvs-django-mysql.md)
    - [Architettura a disponibilità elevata di MySQL in Microsoft Azure](http://download.microsoft.com/download/6/1/C/61C0E37C-F252-4B33-9557-42B90BA3E472/MySQL_HADR_solution_in_Azure.pdf)
7. MariaDB
    - [Procedura: creare un cluster multimaster di MariaDB](virtual-machines-linux-classic-mariadb-mysql-cluster.md)
8. [Installazione di Postgres con corosync, pg\_bouncer utilizzando ILB](https://github.com/chgeuer/postgres-azure)


## Autenticazione e crittografia

L'autenticazione e la crittografia sono aspetti essenziali per lo sviluppo software e sul Web sono disponibili molti argomenti che illustrano come apprendere e usare le tecniche di sicurezza appropriate per entrambe. Questo articolo offre alcune informazioni di base sull'utilizzo, per permettere di iniziare a usare rapidamente carichi di lavoro Linux e open source, oltre a fare riferimento agli strumenti da usare per reimpostare o rimuovere funzionalità di sicurezza remote in Azure. Sono riportate le procedure di base, ma presto verranno aggiunti scenari più complessi.

4. [Nozioni di base: Uso e gestione dei certificati](http://msdn.microsoft.com/library/azure/gg981929.aspx)
7. [Nozioni di base: SSH](virtual-machines-linux-ssh-from-linux.md)
8. [Nozioni di base: Come reimpostare una password o le proprietà SSH per Linux](virtual-machines-linux-classic-reset-access.md)
9. [Nozioni di base: Uso di privilegi root](virtual-machines-linux-use-root-privileges.md)

## HPC (High Performance Computing) in Linux

Eseguire i carichi di lavoro HPC sui cluster di macchine virtuali Linux create con strumenti open source o con Microsoft HPC Pack.

1.	[Modelli della guida introduttiva: Avviare un cluster SLURM](https://azure.microsoft.com/documentation/templates/slurm/) (e [post di blog](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx))
3.	[Modello di Guida introduttiva: creare un cluster HPC con nodi di calcolo di Linux](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)
4.	[Esercitazione: Introduzione all’uso di nodi di calcolo Linux in un cluster HPC Pack in Azure](virtual-machines-linux-classic-hpcpack-cluster.md)
5.	[Esercitazione: Eseguire NAMD con Microsoft HPC Pack su nodi di calcolo Linux in Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)
6.	[Esercitazione: Configurazione di un cluster Linux RDMA per eseguire applicazioni MPI](virtual-machines-linux-classic-rdma-cluster.md)


## DevOps, gestione e ottimizzazione

Questa sezione inizia con un post di blog contenente una serie di video in [Video: macchine virtuali di Azure: utilizzo di Chef, Puppet e Docker per la gestione delle VM Linux](https://azure.microsoft.com/blog/2014/12/15/azure-virtual-machines-using-chef-puppet-and-docker-for-managing-linux-vms/). L'ambito relativo a DevOps, gestione e ottimizzazione è tuttavia in continua e rapida evoluzione. L'esempio seguente deve essere quindi considerato solo come un punto di partenza.

1. Docker
	- [Estensione della VM Docker per Linux in Azure](virtual-machines-linux-dockerextension.md)
	- [Uso dell'estensione della VM Docker dall'interfaccia della riga di comando di Azure (CLI di Azure)](virtual-machines-linux-classic-cli-use-docker.md)
	- [Uso dell'estensione VM Docker dal portale di Azure](virtual-machines-linux-classic-portal-use-docker.md)
    - [Introduzione rapida a Docker in Azure Marketplace](virtual-machines-linux-classic-docker-quickstart.md)
	- [Come usare Docker Machine in Azure](virtual-machines-linux-classic-docker-machine.md)
	- [Come usare Docker Swarm in Azure](virtual-machines-linux-docker-swarm.md)
	- [Introduzione a Docker e Compose in Azure](virtual-machines-linux-docker-compose-quickstart.md)

2. [Fleet con CoreOS](virtual-machines-linux-classic-coreos-howto.md)
3. Deis
	- [Repository GitHub: installazione di Deis in un cluster CoreOS in Azure](https://github.com/chanezon/azure-linux/tree/master/coreos/deis)
4. Kubernetes
	- [Guida completa alla distribuzione di cluster Kubernetes automatizzata con CoreOS e Weave](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)
	- [Visualizzatore Kubernetes](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/)
5. Jenkins e Hudson
	- [Blog: plug-in slave di Jenkins per Azure](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
	- [Repository GitHub: plug-in di archiviazione di Jenkins per Azure](https://github.com/jenkinsci/windows-azure-storage-plugin)
	- [Terze parti: plug-in slave di Hudson per Azure](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
	- [Terze parti: plug-in di archiviazione di Hudson per Azure](https://github.com/hudson3-plugins/windows-azure-storage-plugin)
10. Chef
	- [Chef e Macchine virtuali](virtual-machines-windows-chef-automation.md)
	- [Video: che cos'è Chef e come funziona?](https://msopentech.com/blog/2014/03/31/using-chef-to-manage-azure-resources/)

12. Automazione di Azure
	- [Video: come usare l'Automazione di Azure con VM Linux](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Automation-104-managing-Linux-and-creating-Modules-with-Joe-Levy)
13. Powershell DSC per Linux
    - [Blog: come eseguire PowerShell DSC per Linux](http://blogs.technet.com/b/privatecloud/archive/2014/05/19/powershell-dsc-for-linux-step-by-step.aspx)
    - [GitHub: DSC per client Docker](https://github.com/anweiss/DockerClientDSC)
13. [Ubuntu Juju](https://juju.ubuntu.com/docs/config-azure.html)
14. [Plug-in di Packer per Azure](https://github.com/msopentech/packer-azure)

## Supporto, risoluzione dei problemi e informazioni sui problemi irreversibili

1. Documentazione del supporto tecnico Microsoft
	- [Supporto: supporto per immagini Linux in Microsoft Azure](http://support2.microsoft.com/kb/2941892)

<!--Anchors-->
[Distros]: #distros
[The Basics]: #basics
[Community Images and Repositories]: #images
[Languages and Platforms]: #langsandplats
[Samples and Scripts]: #samples
[Auth and Encryption]: #security
[Devops, Management, and Optimization]: #devops
[Support, Troubleshooting, and "It Just Doesn't Work"]: #supportdebug

<!--Link references--In actual articles, you only need a single period before the slash. -->
[How to use docker-machine on Azure]: virtual-machines-linux-classic-docker-machine.md
[How to use docker with swarm on Azure]: virtual-machines-docker-swarm.md

<!---HONumber=AcomDC_0323_2016-->