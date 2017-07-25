---
title: Computing Linux e open source in Azure | Microsoft Docs
description: Elenco relativo ad articoli sul computing Linux e open source in Azure, incluse informazioni di base sull'utilizzo di Linux, alcuni concetti fondamentali sull'esecuzione o il caricamento di immagini Linux in Azure e altri contenuti relativi a tecnologie e ottimizzazioni specifiche.
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: a7e608b5-26ea-41e0-b46b-1a483a257754
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/27/2016
ms.author: rasquill
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 1cdd0e68368d2dc376ee45df67bf5e75288d4ca3
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="linux-and-open-source-computing-on-azure"></a>Computing Linux e open source in Azure
Tutta la documentazione necessaria per creare e gestire macchine virtuali basate su Linux nel modello di distribuzione classica.

> [!IMPORTANT] 
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../../resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti.

## <a name="get-started"></a>Attività iniziali
* [Introduzione a Linux in Azure](intro-on-azure.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Domande frequenti sulle macchine virtuali di Azure create con il modello di distribuzione classica](classic/faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Informazioni sulle immagini per le macchine virtuali](../windows/classic/about-images.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Caricamento di un'immagine di distribuzione personalizzata](classic/create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json), include anche istruzioni per l'uso di una [distribuzione supportata da Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Accedere a una VM Linux con il portale di Azure classico](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="set-up"></a>Configurare
* [Installare l'interfaccia della riga di comando di Azure](../../cli-install-nodejs.md)

## <a name="tutorials"></a>Esercitazioni
* [Installare lo stack LAMP in una macchina virtuale Linux in Azure](create-lamp-stack.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Applicazione Web Ruby on Rails in una VM di Azure](classic/virtual-machines-linux-classic-ruby-rails-web-app.md)
* [Procedura: installare Apache Qpid Proton-C per AMQP e bus di servizio](../../service-bus-messaging/service-bus-amqp-apache.md)

### <a name="databases"></a>Database
* [Ottimizzare le prestazioni di MySQL in Azure](classic/optimize-mysql.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Cluster MySQL](classic/mysql-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Esecuzione di Cassandra con Linux in Azure e accesso da Node.js](classic/cassandra-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Creare un cluster multimaster di MariaDB](classic/mariadb-mysql-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

### <a name="hpc"></a>HPC
* [Introduzione all'uso di nodi di calcolo Linux in un cluster HPC Pack in Azure](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Eseguire NAMD con Microsoft HPC Pack su nodi di calcolo Linux in Azure](classic/hpcpack-cluster-namd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Configurazione di un cluster Linux RDMA per eseguire applicazioni MPI](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

### <a name="docker"></a>Docker
* [Uso dell'estensione della VM Docker dall'interfaccia della riga di comando di Azure (CLI di Azure)](classic/cli-use-docker.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Uso dell'estensione VM Docker dal portale di Azure](classic/portal-use-docker.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Come usare Docker Machine in Azure](docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="ubuntu"></a>Ubuntu
* [Procedura: cluster MySQL](classic/mysql-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Procedura: Node.js e Cassandra](classic/cassandra-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

### <a name="opensuse"></a>OpenSUSE
* [Procedura: installare ed eseguire MySQL](classic/mysql-on-opensuse.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

### <a name="coreos"></a>CoreOS
* [Procedura: come usare CoreOS in Azure](https://coreos.com/os/docs/latest/booting-on-azure.html)

## <a name="planning"></a>Pianificazione
* [Linee guida sull'implementazione dei servizi di infrastruttura di Azure](../windows/infrastructure-subscription-accounts-guidelines.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Selezione dei nomi utente per Linux](usernames.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Come configurare un set di disponibilità per le macchine virtuali nel modello di distribuzione classica](../windows/classic/configure-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Come pianificare la manutenzione nelle VM di Azure](classic/planned-maintenance-schedule.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Gestione della disponibilità delle macchine virtuali](../windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Manutenzione pianificata per macchine virtuali Linux in Azure](planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="deployment"></a>Distribuzione
* [Creare una macchina virtuale personalizzata che esegue Linux](../windows/classic/createportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Nozioni di base: Acquisizione di una VM Linux per creare un modello](classic/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Informazioni per le distribuzioni non approvate](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="management"></a>gestione
* [SSH](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Come reimpostare una password o le proprietà SSH per Linux](classic/reset-access.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Uso di privilegi root](use-root-privileges.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="azure-resources"></a>Risorse di Azure
* [Agente Linux di Azure](../windows/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Estensioni VM e funzionalità di Azure](../windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Inserimento di dati personalizzati in una macchina virtuale di Azure](../windows/classic/inject-custom-data.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="storage"></a>Archiviazione
* [Collegare un disco dati a una VM Linux](../windows/classic/attach-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Scollegare un disco dati da una VM Linux](classic/detach-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="networking"></a>Rete
* [Come configurare gli endpoint in una macchina virtuale classica in Azure](../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

## <a name="troubleshooting"></a>Risoluzione dei problemi
* [Risolvere i problemi relativi alle connessioni Secure Shell (SSH) a una macchina virtuale di Azure basata su Linux](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Risolvere i problemi della distribuzione classica con la creazione di una nuova macchina virtuale Linux in Azure](classic/troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)  
* [Risolvere i problemi della distribuzione classica con il riavvio e il ridimensionamento di una macchina virtuale Linux esistente in Azure](../windows/restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) 

## <a name="reference"></a>riferimento
* [Comandi dell'interfaccia della riga di comando di Azure in modalità Gestione servizi di Azure (asm)](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)
* [API REST di gestione dei servizi di Azure](https://msdn.microsoft.com/library/azure/ee460799.aspx)

## <a name="general-links"></a>Collegamenti generali
Di seguito sono riportati i collegamenti a blog Microsoft, pagine di Technet e siti esterni invece della documentazione di Azure.com come in precedenza. Poiché sia Azure sia l'ambito del computing open source sono in continua evoluzione, quasi sicuramente i collegamenti non sono aggiornati, *nonostante* il constante impegno di Microsoft per aggiungere gli argomenti più recenti e rimuovere quelli obsoleti. Segnalare eventuali articoli da aggiungere inserendo un commento oppure inviare una richiesta di rimozione al [repository Github](https://github.com/Azure/azure-content/).

* [Esecuzione di ASP.NET 5 in Linux mediante i contenitori Docker](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)
* [Come distribuire un'immagine di VM CentOS da OpenLogic](https://azure.microsoft.com/blog/2013/01/11/deploying-openlogic-centos-images-on-windows-azure-virtual-machines/)
* [Infrastruttura di aggiornamento SUSE](https://forums.suse.com/showthread.php?5622-New-Update-Infrastructure)
* [SUSE Linux Enterprise Server per la SAP Cloud Appliance Library](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver11sp3forsapcloudappliance/)
* [Linux a disponibilità elevata in Azure in 12 passaggi](http://blogs.technet.com/b/keithmayer/archive/2014/10/03/quick-start-guide-building-highly-available-linux-servers-in-the-cloud-on-microsoft-azure.aspx)
* [Automatizzare il provisioning di Linux in Azure con l'interfaccia della riga di comandi di Azure, node.js, jhawk](http://blogs.technet.com/b/keithmayer/archive/2014/11/24/step-by-step-automated-provisioning-for-linux-in-the-cloud-with-microsoft-azure-xplat-cli-json-and-node-js-part-1.aspx)
* [GlusterFS in Azure](http://dastouri.azurewebsites.net/gluster-on-azure-part-1/)

### <a name="freebsd"></a>FreeBSD
* [Esecuzione di FreeBSD in Azure](https://azure.microsoft.com/blog/2014/05/22/running-freebsd-in-azure/)
* [Distribuzione di FreeBSD con facilità](http://msopentech.com/blog/2014/10/24/easy-deploy-freebsd-microsoft-azure-vm-depot/)
* [Distribuzione di un'immagine personalizzata di FreeBSD](http://msopentech.com/blog/2014/05/14/deploy-customize-freebsd-virtual-machine-image-microsoft-azure/)
* [Kaspersky AV per Linux File Server](https://azure.microsoft.com/marketplace/partners/kaspersky-lab/kav-for-lfs-kav-for-lfs/)

### <a name="nosql"></a>NoSQL
* [8 database NoSql open source per Azure](http://openness.microsoft.com/blog/2014/11/03/open-source-nosql-databases-microsoft-azure/)
* [Slideshare (MSOpenTech): esperienze con CouchDb in Azure](http://www.slideshare.net/brianbenz/experiences-using-couchdb-inside-microsofts-azure-team)
* [Esecuzione di CouchDB-as-a-Service con node.js, CORS e Grunt](http://msopentech.com/blog/2013/12/19/tutorial-building-multi-tier-windows-azure-web-application-use-cloudants-couchdb-service-node-js-cors-grunt-2/)
* [Redis in Windows nel servizio Cache Redis di Azure](http://msopentech.com/blog/2014/05/12/redis-on-windows/)
* [Annuncio del provider di stato della sessione ASP.NET per la versione di anteprima di Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)
* [Blog: RavenHQ ora disponibile in Azure Marketplace](https://azure.microsoft.com/blog/2014/08/12/ravenhq-now-available-in-the-azure-store/)

### <a name="big-data"></a>Big Data
* [Installazione di Hadoop nelle macchine virtuali Linux di Azure](http://blogs.msdn.com/b/benjguin/archive/2013/04/05/how-to-install-hadoop-on-windows-azure-linux-virtual-machines.aspx)
* [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/)

### <a name="relational-database"></a>Database relazionale
* [Architettura a disponibilità elevata di MySQL in Microsoft Azure](http://download.microsoft.com/download/6/1/C/61C0E37C-F252-4B33-9557-42B90BA3E472/MySQL_HADR_solution_in_Azure.pdf)
* [Installazione di Postgres con corosync, pg_bouncer utilizzando ILB](https://github.com/chgeuer/postgres-azure)

### <a name="linux-high-performance-computing-hpc"></a>HPC (High Performance Computing) in Linux
* [Modelli della guida introduttiva: avviare un cluster SLURM](https://github.com/Azure/azure-quickstart-templates/tree/master/slurm) (e [post di blog](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx))
* [Modello di Guida introduttiva: creare un cluster HPC con nodi di calcolo di Linux](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)

### <a name="devops-management-and-optimization"></a>DevOps, gestione e ottimizzazione
Poiché l'ambito relativo a DevOps, gestione e ottimizzazione è in continua e rapida evoluzione, si consideri l'elenco seguente come un punto di partenza.

* [Video in Macchine virtuali di Azure, uso di Chef, Puppet e Docker per la gestione di VM Linux](https://azure.microsoft.com/blog/2014/12/15/azure-virtual-machines-using-chef-puppet-and-docker-for-managing-linux-vms/)
* [Guida completa alla distribuzione di cluster Kubernetes automatizzata con CoreOS e Weave](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)
* [Visualizzatore Kubernetes](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/)
* [Plug-in slave di Jenkins per Azure](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
* [Repository GitHub: plug-in di archiviazione di Jenkins per Azure](https://github.com/jenkinsci/windows-azure-storage-plugin)
* [Terze parti: plug-in slave di Hudson per Azure](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
* [Terze parti: plug-in di archiviazione di Hudson per Azure](https://github.com/hudson3-plugins/windows-azure-storage-plugin)
* [Video: che cos'è Chef e come funziona?](https://msopentech.com/blog/2014/03/31/using-chef-to-manage-azure-resources/)
* [Video: come usare l'Automazione di Azure con VM Linux](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Automation-104-managing-Linux-and-creating-Modules-with-Joe-Levy)
* [Blog: come eseguire PowerShell DSC per Linux](http://blogs.technet.com/b/privatecloud/archive/2014/05/19/powershell-dsc-for-linux-step-by-step.aspx)
* [GitHub: DSC per client Docker](https://github.com/anweiss/DockerClientDSC)
* [Plug-in di Packer per Azure](https://github.com/msopentech/packer-azure)


