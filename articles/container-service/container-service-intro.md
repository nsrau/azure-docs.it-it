<properties
   pageTitle="Introduzione al servizio contenitore di Azure | Microsoft Azure"
   description="Il servizio contenitore di Azure (ACS) fornisce un modo per semplificare la creazione, la configurazione e la gestione di un cluster di macchine virtuali che sono preconfigurate per eseguire le applicazioni nei contenitori."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, Contenitori, Micro-servizi, Mesos, Azure"/>
   
<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="rogardle"/>

# Introduzione al servizio contenitore di Azure

Il servizio contenitore di Azure (ACS) fornisce un modo per semplificare la creazione, la configurazione e la gestione di un cluster di macchine virtuali che sono preconfigurate per eseguire le applicazioni nei contenitori. Utilizzando una configurazione ottimizzata di strumenti di pianificazione e orchestrazione open source più diffusi, ACS consente di utilizzare le competenze esistenti o disegnare su un corpo di grandi dimensioni e in continua crescita di assistenza della community per distribuire e gestire applicazioni basate sul contenitore in Microsoft Azure.

<br /> ![ACS fornisce uno strumento per gestire applicazioni in container su più host in Azure.](./media/acs-intro/acs-cluster.png) <br /><br />

ACS si avvale del formato di contenitore Docker per garantire che i contenitori di applicazioni siano completamente portabili. Supporta inoltre la scelta di Marathon e Apache Mesos o Docker Swarm per garantire che queste applicazioni possano essere scalate in migliaia, persino decine di migliaia di contenitori.

Il servizio contenitore di Azure consente di sfruttare i vantaggi delle funzioni del grado dell’organizzazione di Azure mantenendo al tempo stesso la portabilità dell'applicazione, inclusi i livelli di orchestrazione.

Utilizzo del servizio contenitore di Azure
-----------------------------

Il nostro obiettivo con il servizio contenitore di Azure è fornire un ambiente host contenitore, tramite strumenti open source e tecnologie, che sono attualmente i più diffusi fra i nostri clienti. A tal fine, vengono esposti gli endpoint API standard per l'agente di orchestrazione scelto. Utilizzando questi endpoint è possibile utilizzare qualsiasi software in grado di comunicare con essi. Nel caso dell'endpoint Docker Swarm, ad esempio, è possibile scegliere di utilizzare Docker CLI, mentre per Apache Mesos è possibile scegliere di utilizzare DCOS CLI.

Creazione di un Cluster Docker con il servizio contenitore di Azure
-------------------------------------------------------

Per iniziare a usare il servizio contenitore di Azure verrà distribuito un cluster ACS tramite un modello di Azure Resource Manager. Questa distribuzione può essere configurata con dimensioni e opzioni di disponibilità diverse e verrà configurata con Apache Mesos o Docker Swarm. I modelli di Azure Resource Manager possono essere distribuiti tramite il portale di Azure, l'interfaccia della riga di comando di Azure o PowerShell. I modelli possono anche essere modificati per includere una configurazione di Azure aggiuntiva o avanzata. Per altre informazioni sulla distribuzione e sul cluster ACS, vedere [Distribuire un cluster del servizio contenitore di Azure](./container-service-deployment.md).

Distribuzione di un'applicazione
------------------------

Durante l'anteprima forniamo una scelta di Docker Swarm o Mesos Apache (con framework DCOS Marathon e DCOS Chronos) per l'orchestrazione.

### Utilizzo di Apache Mesos

Apache Mesos è un progetto open source ospitato nella Apache Software Foundation. Vengono elencati alcuni dei [protagonisti in IT](http://mesos.apache.org/documentation/latest/powered-by-mesos/) come utenti e collaboratori.

![ACS configurato per Swarm che mostra agenti e schemi.](media/acs-intro/acs-mesos.png)

Mesos comprime un set di funzionalità eccezionali.

-   Scalabilità a 10.000 dei nodi

-   Schemi replicati a tolleranza di errore e slave utilizzando ZooKeeper

-   Supporto per i contenitori formattati Docker

-   Isolamento nativo tra le attività con i contenitori Linux

-   Pianificazione di più risorse (memoria, CPU, disco e porte)

-   Java, Python e APIs C++ per lo sviluppo di nuove applicazioni parallele

-   Interfaccia utente Web per la visualizzazione dello stato del cluster

Mesos include il supporto per un numero elevato di [framework](http://mesos.apache.org/documentation/latest/frameworks/) che possono essere utilizzati per la pianificazione dei carichi di lavoro del servizio contenitore di Azure. Per impostazione predefinita, ACS include i framework Marathon e Chronos.

#### Utilizzo di Marathon e Chronos

Marathon è un sistema di init e controllo di tutto il cluster per i servizi in cgroups o, nel caso di ACS, in contenitori formato Docker. Si tratta di un partner ideale di Chronos, un'utilità di pianificazione processi a tolleranza d'errore per Mesos che gestisce le dipendenze e le pianificazioni in base al tempo.

Marathon e Chronos forniscono un'interfaccia utente Web da cui è possibile distribuire le applicazioni. Si accederà a questo con un URL simile a `http://DNS\_PREFIX.REGION.cloudapp.azure.com` dove DNS\_PREFIX e REGION sono entrambi definiti in fase di distribuzione. Naturalmente, è anche possibile fornire il proprio nome DNS. Per altre informazioni sull'esecuzione di un contenitore tramite l'interfaccia utente Web di Marathon, vedere [Gestione di contenitori tramite l'interfaccia utente Web](./container-service-mesos-marathon-ui.md).

Inoltre, è possibile utilizzare le API REST per la comunicazione con Marathon e Chronos. Sono disponibili numerose librerie client per ogni strumento, che coprono un'ampia gamma di linguaggi e, naturalmente, è possibile utilizzare il protocollo HTTP in qualsiasi linguaggio. Inoltre, molti strumenti comuni di DevOps forniscono il supporto per queste utilità di pianificazione. Ciò fornisce flessibilità massima per il team operativo quando si lavora con un cluster ACS. Per altre informazioni sull'esecuzione di un contenitore tramite l'API REST di Marathon, vedere [Gestione di contenitori tramite l'API REST](./container-service-mesos-marathon-rest.md).

### Utilizzo di Docker Swarm

Docker Swarm fornisce clustering nativo per Docker. Poiché Docker Swarm serve l'API Docker standard, gli strumenti che già comunicano con un daemon Docker possono usare Swarm per la scalabilità trasparente a più host nel servizio contenitore di Azure.

![ACS configurato per l'utilizzo di Apache Mesos, che mostra jumpbox, agenti e schemi.](media/acs-intro/acs-swarm.png)

Gli strumenti supportati per la gestione dei contenitori in un cluster Swarm includono in particolare quanto segue:

-   Dokku

-   Docker CLI e Docker Compose

-   Krane

-   Jenkins

Video
------
Annuncio di AzureCon:

> [AZURE.VIDEO azurecon-2015-deep-dive-on-the-azure-container-service-with-mesos]  

Introduzione a ACS:

> [AZURE.VIDEO connect-2015-getting-started-developing-with-docker-and-azure-container-service]

<!---HONumber=AcomDC_0330_2016-->