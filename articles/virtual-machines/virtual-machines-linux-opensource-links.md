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
	ms.date="05/10/2016"
	ms.author="rasquill"/>



# Computing Linux e open source in Azure

Tutta la documentazione necessaria per creare e gestire macchine virtuali basate su Linux nel modello di distribuzione classica.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modello Gestione risorse.

## Attività iniziali
- [Introduzione a Linux in Azure](virtual-machines-linux-intro-on-azure.md)
- [Domande frequenti sulle macchine virtuali di Azure create con il modello di distribuzione classica](virtual-machines-linux-classic-faq.md)
- [Informazioni sulle immagini per le macchine virtuali](virtual-machines-linux-classic-about-images.md)
- [Caricamento di un'immagine di distribuzione personalizzata](virtual-machines-linux-classic-create-upload-vhd.md), include anche istruzioni per l'uso di una [distribuzione approvata di Azure](virtual-machines-linux-endorsed-distros.md)
- [Accedere a una VM Linux con il portale di Azure classico](virtual-machines-linux-classic-log-on.md)

## Configurare

- [Installare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md)


## Esercitazioni

- [Installare lo stack LAMP in una macchina virtuale Linux in Azure](virtual-machines-linux-install-lamp-stack.md)
- [Applicazione Web Ruby on Rails in una macchina virtuale di Azure](virtual-machines-linux-classic-ruby-rails-web-app.md)
- [Procedura: installare Apache Qpid Proton-C per AMQP e bus di servizio](../service-bus/service-bus-amqp-apache.md)

### Database
- [Ottimizzare le prestazioni di MySQL in Azure](virtual-machines-linux-classic-optimize-mysql.md)
- [Cluster MySQL](virtual-machines-linux-classic-mysql-cluster.md)
- [Esecuzione di Cassandra con Linux in Azure e accesso da Node.js](virtual-machines-linux-classic-cassandra-nodejs.md)
- [Creare un cluster multimaster di MariaDB](virtual-machines-linux-classic-mariadb-mysql-cluster.md)

### HPC
- [Introduzione all'uso di nodi di calcolo Linux in un cluster HPC Pack in Azure](virtual-machines-linux-classic-hpcpack-cluster.md)
- [Eseguire NAMD con Microsoft HPC Pack su nodi di calcolo Linux in Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)
- [Configurazione di un cluster Linux RDMA per eseguire applicazioni MPI](virtual-machines-linux-classic-rdma-cluster.md)

### Docker
- [Uso dell'estensione della VM Docker dall'interfaccia della riga di comando di Azure (CLI di Azure)](virtual-machines-linux-classic-cli-use-docker.md)
- [Uso dell'estensione VM Docker dal portale di Azure](virtual-machines-linux-classic-portal-use-docker.md)
- [Introduzione rapida a Docker in Azure Marketplace](virtual-machines-linux-classic-docker-quickstart.md)
- [Come usare Docker Machine in Azure](virtual-machines-linux-classic-docker-machine.md)

### Ubuntu
- [Procedura: cluster MySQL](virtual-machines-linux-classic-mysql-cluster.md)
- [Procedura: Node.js e Cassandra](virtual-machines-linux-classic-cassandra-nodejs.md)

### OpenSUSE
- [Procedura: installare ed eseguire MySQL](virtual-machines-linux-classic-mysql-on-opensuse.md)

### CoreOS
- [Procedura: come usare CoreOS in Azure](virtual-machines-linux-classic-coreos-howto.md)
- [Procedura: guida introduttiva a Fleet e Docker su CoreOS in Azure](virtual-machines-linux-classic-coreos-fleet-get-started.md)


## Pianificazione
- [Linee guida sull'implementazione dei servizi di infrastruttura di Azure](virtual-machines-linux-infrastructure-service-guidelines.md)
- [Selezione dei nomi utente per Linux](virtual-machines-linux-usernames.md)
- [Come configurare un set di disponibilità per le macchine virtuali nel modello di distribuzione classica](virtual-machines-linux-classic-configure-availability.md)
- [Come pianificare la manutenzione nelle VM di Azure](virtual-machines-linux-planned-maintenance-schedule.md)
- [Gestione della disponibilità delle macchine virtuali](virtual-machines-linux-manage-availability.md)
- [Manutenzione pianificata per macchine virtuali Linux in Azure](virtual-machines-linux-planned-maintenance.md)


## Distribuzione
- [Creare una macchina virtuale personalizzata che esegue Linux](virtual-machines-linux-classic-createportal.md)
- [Nozioni di base: Acquisizione di una VM Linux per creare un modello](virtual-machines-linux-classic-capture-image.md)
- [Informazioni per le distribuzioni non approvate](virtual-machines-linux-create-upload-generic.md)


## gestione

- [SSH](virtual-machines-linux-ssh-from-linux.md)
- [Come reimpostare una password o le proprietà SSH per Linux](virtual-machines-linux-classic-reset-access.md)
- [Uso di privilegi root](virtual-machines-linux-use-root-privileges.md)


## Risorse di Azure

- [Agente Linux di Azure](virtual-machines-linux-agent-user-guide.md)
- [Estensioni VM e funzionalità di Azure](virtual-machines-windows-extensions-features.md)
- [Inserimento di dati personalizzati in una macchina virtuale di Azure](virtual-machines-windows-classic-inject-custom-data.md)


## Archiviazione

- [Collegare un disco dati a una VM Linux](virtual-machines-linux-classic-attach-disk.md)
- [Scollegare un disco dati da una VM Linux](virtual-machines-linux-classic-detach-disk.md)
- [RAID](virtual-machines-linux-configure-raid.md)


## Rete
- [Come configurare gli endpoint in una macchina virtuale classica in Azure](virtual-machines-linux-classic-setup-endpoints.md)


## Risoluzione dei problemi
- [Risolvere i problemi relativi alle connessioni Secure Shell (SSH) a una macchina virtuale di Azure basata su Linux](virtual-machines-linux-troubleshoot-ssh-connection.md)
- [Risolvere i problemi della distribuzione classica con la creazione di una nuova macchina virtuale Linux in Azure](virtual-machines-linux-classic-troubleshoot-deployment-new-vm.md)  
- [Risolvere i problemi della distribuzione classica con il riavvio e il ridimensionamento di una macchina virtuale Linux esistente in Azure](virtual-machines-linux-classic-restart-resize-error-troubleshooting.md) 


## riferimento

- [Comandi dell'interfaccia della riga di comando di Azure in modalità Gestione servizi di Azure (asm)](../virtual-machines-command-line-tools.md)
- [API REST di gestione dei servizi di Azure](https://msdn.microsoft.com/library/azure/ee460799.aspx)




## Collegamenti generali
Di seguito sono riportati i collegamenti a blog Microsoft, pagine di Technet e siti esterni invece della documentazione di Azure.com come in precedenza. Poiché sia Azure sia l'ambito del computing open source sono in continua evoluzione, quasi sicuramente i collegamenti non sono aggiornati, *nonostante* il constante impegno di Microsoft per aggiungere gli argomenti più recenti e rimuovere quelli obsoleti. Segnalare eventuali articoli da aggiungere inserendo un commento oppure inviare una richiesta di rimozione al [repository Github](https://github.com/Azure/azure-content/).

- [Esecuzione di ASP.NET 5 in Linux mediante i contenitori Docker](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)
- [Come distribuire un'immagine di VM CentOS da OpenLogic](https://azure.microsoft.com/blog/2013/01/11/deploying-openlogic-centos-images-on-windows-azure-virtual-machines/)
- [Infrastruttura di aggiornamento SUSE](https://forums.suse.com/showthread.php?5622-New-Update-Infrastructure)
- [SUSE Linux Enterprise Server per la SAP Cloud Appliance Library](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver11sp3forsapcloudappliance/)
- [Linux a disponibilità elevata in Azure in 12 passaggi](http://blogs.technet.com/b/keithmayer/archive/2014/10/03/quick-start-guide-building-highly-available-linux-servers-in-the-cloud-on-microsoft-azure.aspx)
- [Automatizzare il provisioning di Linux in Azure con l'interfaccia della riga di comandi di Azure, node.js, jhawk](http://blogs.technet.com/b/keithmayer/archive/2014/11/24/step-by-step-automated-provisioning-for-linux-in-the-cloud-with-microsoft-azure-xplat-cli-json-and-node-js-part-1.aspx)
- [GlusterFS in Azure](http://dastouri.azurewebsites.net/gluster-on-azure-part-1/)

### FreeBSD
- [Esecuzione di FreeBSD in Azure](https://azure.microsoft.com/blog/2014/05/22/running-freebsd-in-azure/)
- [Distribuzione di FreeBSD con facilità](http://msopentech.com/blog/2014/10/24/easy-deploy-freebsd-microsoft-azure-vm-depot/)
- [Distribuzione di un'immagine personalizzata di FreeBSD](http://msopentech.com/blog/2014/05/14/deploy-customize-freebsd-virtual-machine-image-microsoft-azure/)
- [Kaspersky AV per Linux File Server](https://azure.microsoft.com/marketplace/partners/kaspersky-lab/kav-for-lfs-kav-for-lfs/)

### NoSQL

- [8 database NoSql open source per Azure](http://openness.microsoft.com/blog/2014/11/03/open-source-nosql-databases-microsoft-azure/)
- [Slideshare (MSOpenTech): esperienze con CouchDb in Azure](http://www.slideshare.net/brianbenz/experiences-using-couchdb-inside-microsofts-azure-team)
- [Esecuzione di CouchDB-as-a-Service con node.js, CORS e Grunt](http://msopentech.com/blog/2013/12/19/tutorial-building-multi-tier-windows-azure-web-application-use-cloudants-couchdb-service-node-js-cors-grunt-2/)

- [Redis in Windows nel servizio Cache Redis di Azure](http://msopentech.com/blog/2014/05/12/redis-on-windows/)
- [Annuncio del provider di stato della sessione ASP.NET per la versione di anteprima di Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)

- [Blog: RavenHQ ora disponibile in Azure Marketplace](https://azure.microsoft.com/blog/2014/08/12/ravenhq-now-available-in-the-azure-store/)

### Big Data
- [Installazione di Hadoop nelle macchine virtuali Linux di Azure](http://blogs.msdn.com/b/benjguin/archive/2013/04/05/how-to-install-hadoop-on-windows-azure-linux-virtual-machines.aspx)
- [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/)

### Database relazionale
- [Architettura a disponibilità elevata di MySQL in Microsoft Azure](http://download.microsoft.com/download/6/1/C/61C0E37C-F252-4B33-9557-42B90BA3E472/MySQL_HADR_solution_in_Azure.pdf)
- [Installazione di Postgres con corosync, pg\_bouncer utilizzando ILB](https://github.com/chgeuer/postgres-azure)

### HPC (High Performance Computing) in Linux

- [Modelli della guida introduttiva: Avviare un cluster SLURM](https://github.com/Azure/azure-quickstart-templates/tree/master/slurm) (e [post di blog](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx))
- [Modello di Guida introduttiva: creare un cluster HPC con nodi di calcolo di Linux](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)

### DevOps, gestione e ottimizzazione

Poiché l'ambito relativo a DevOps, gestione e ottimizzazione è in continua e rapida evoluzione, si consideri l'elenco seguente come un punto di partenza.

- [Video in Macchine virtuali di Azure, uso di Chef, Puppet e Docker per la gestione di VM Linux](https://azure.microsoft.com/blog/2014/12/15/azure-virtual-machines-using-chef-puppet-and-docker-for-managing-linux-vms/)

- [Guida completa alla distribuzione di cluster Kubernetes automatizzata con CoreOS e Weave](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)
- [Visualizzatore Kubernetes](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/)

- [Plug-in slave di Jenkins per Azure](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
- [Repository GitHub: plug-in di archiviazione di Jenkins per Azure](https://github.com/jenkinsci/windows-azure-storage-plugin)

- [Terze parti: plug-in slave di Hudson per Azure](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
- [Terze parti: plug-in di archiviazione di Hudson per Azure](https://github.com/hudson3-plugins/windows-azure-storage-plugin)

- [Video: che cos'è Chef e come funziona?](https://msopentech.com/blog/2014/03/31/using-chef-to-manage-azure-resources/)

- [Video: come usare l'Automazione di Azure con VM Linux](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Automation-104-managing-Linux-and-creating-Modules-with-Joe-Levy)

- [Blog: come eseguire PowerShell DSC per Linux](http://blogs.technet.com/b/privatecloud/archive/2014/05/19/powershell-dsc-for-linux-step-by-step.aspx)
- [GitHub: DSC per client Docker](https://github.com/anweiss/DockerClientDSC)

- [Ubuntu Juju](https://juju.ubuntu.com/docs/config-azure.html)

- [Plug-in di Packer per Azure](https://github.com/msopentech/packer-azure)

<!---HONumber=AcomDC_0511_2016-->