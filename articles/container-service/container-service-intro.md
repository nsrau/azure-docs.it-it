<properties
   pageTitle="Introduzione al servizio contenitore di Azure | Microsoft Azure"
   description="Il servizio contenitore di Azure fornisce un modo per semplificare la creazione, la configurazione e la gestione di un cluster di macchine virtuali che sono preconfigurate per eseguire le applicazioni nei contenitori."
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

Il servizio contenitore di Azure fornisce un modo per semplificare la creazione, la configurazione e la gestione di un cluster di macchine virtuali preconfigurate per eseguire le applicazioni nei contenitori. Usa una configurazione ottimizzata di strumenti di pianificazione e orchestrazione open source comuni. Ciò consente di usare le competenze già acquisite o di attingere da un consistente e crescente bagaglio di competenze a livello di community per distribuire e gestire applicazioni basate sul contenitore in Microsoft Azure.

<br /> ![Il servizio contenitore di Azure fornisce uno strumento per gestire applicazioni in contenitori su più host in Azure.](./media/acs-intro/acs-cluster.png) <br /><br />

Il servizio contenitore di Azure si avvale del formato di contenitore Docker per garantire che i contenitori di applicazioni siano completamente portabili. Supporta anche la scelta di Marathon e DC/OS o Docker Swarm per garantire che queste applicazioni possano essere scalate in migliaia, persino decine di migliaia, di contenitori.

Il servizio contenitore di Azure consente di sfruttare i vantaggi delle funzioni di classe enterprise di Azure mantenendo al tempo stesso la portabilità delle applicazioni, inclusi i livelli di orchestrazione.

Utilizzo del servizio contenitore di Azure
-----------------------------

L'obiettivo del servizio contenitore di Azure è fornire un ambiente host contenitore tramite tecnologie e strumenti open source, che sono attualmente diffusi fra i nostri clienti. A tal fine, vengono esposti gli endpoint API standard per l'agente di orchestrazione scelto. Tramite questi endpoint è possibile usare qualsiasi software in grado di comunicare con essi. Ad esempio, nel caso dell'endpoint Docker Swarm, è possibile scegliere di usare l'interfaccia della riga di comando (CLI) Docker. Per DC/OS è possibile scegliere di usare l'interfaccia della riga di comando DCOS.

Creazione di un cluster Docker con il servizio contenitore di Azure
-------------------------------------------------------

Per iniziare a usare il servizio contenitore di Azure verrà distribuito un cluster del servizio tramite un modello di Azure Resource Manager. Questa distribuzione può essere configurata con opzioni di dimensioni e di disponibilità diverse, usando DC/OS o Docker Swarm. I modelli di Azure Resource Manager possono essere distribuiti tramite il portale di Azure con l'interfaccia della riga di comando di Azure o PowerShell. I modelli possono anche essere modificati per includere una configurazione di Azure aggiuntiva o avanzata. Per altre informazioni sulla distribuzione del cluster del servizio contenitore di Azure, vedere [Distribuire un cluster del servizio contenitore di Azure](container-service-deployment.md).

Distribuzione di un'applicazione
------------------------

Il servizio contenitore di Azure consente di scegliere tra Docker Swarm e DC/OS per l'orchestrazione.

### Uso di DC/OS

DC/OS è un sistema operativo distribuito basato sul kernel dei sistemi distribuiti Apache Mesos. Apache Mesos fa parte di Apache Software Foundation e annovera tra gli utenti e i collaboratori alcuni dei [nomi più importanti del panorama IT](http://mesos.apache.org/documentation/latest/powered-by-mesos/).

![Servizio contenitore di Azure configurato per Swarm che mostra agenti e schemi.](media/acs-intro/dcos.png)

DC/OS e Apache Mesos offrono un set di funzionalità molto ampio:

-   Scalabilità fino a decine di migliaia di nodi

-   Schemi replicati a tolleranza di errore e slave con Apache ZooKeeper

-   Supporto per i contenitori formattati Docker

-   Isolamento nativo tra le attività con i contenitori Linux

-   Pianificazione di più risorse (memoria, CPU, disco e porte)

-   Java, Python e API C++ per lo sviluppo di nuove applicazioni parallele

-   Interfaccia utente Web per la visualizzazione dello stato del cluster

Per impostazione predefinita, DC/OS in esecuzione nel servizio contenitore di Azure include la piattaforma di orchestrazione Marathon per la pianificazione dei carichi di lavoro.

#### Uso di Marathon

Marathon è un sistema di init e controllo di tutto il cluster per i servizi in cgroups o, nel caso del servizio contenitore di Azure, in contenitori formattati Docker. Si tratta di un partner ideale per [Chronos](https://mesos.github.io/chronos/), un'utilità di pianificazione processi con tolleranza di errore per DC/OS che gestisce le dipendenze e le pianificazioni in base al tempo.

Marathon offre un'interfaccia utente Web da cui è possibile distribuire le applicazioni. L'accesso avviene tramite un URL simile a `http://DNS_PREFIX.REGION.cloudapp.azure.com`, dove DNS\_PREFIX e REGION vengono entrambi definiti in fase di distribuzione. Naturalmente, è anche possibile fornire il proprio nome DNS. Per altre informazioni sull'esecuzione di un contenitore tramite l'interfaccia utente Web di Marathon, vedere [Gestione di contenitori tramite l'interfaccia utente Web](container-service-mesos-marathon-ui.md).

È anche possibile usare le API REST per la comunicazione con Marathon. Esistono una serie di librerie client disponibili per ogni strumento. Coprono un'ampia gamma di linguaggi e, naturalmente, è possibile utilizzare il protocollo HTTP in qualsiasi linguaggio. Inoltre, molti strumenti comuni di DevOps forniscono il supporto per queste utilità di pianificazione. Ciò fornisce flessibilità massima per il team operativo quando si lavora con un cluster del servizio contenitore di Azure. Per altre informazioni sull'esecuzione di un contenitore tramite l'API REST di Marathon, vedere [Gestione di contenitori tramite l'API REST](container-service-mesos-marathon-rest.md).

### Utilizzo di Docker Swarm

Docker Swarm fornisce clustering nativo per Docker. Poiché Docker Swarm serve l'API Docker standard, gli strumenti che già comunicano con un daemon Docker possono usare Swarm per la scalabilità trasparente a più host nel servizio contenitore di Azure.

![Servizio contenitore di Azure configurato per l'uso di DC/OS, che mostra jumpbox, agenti e schemi.](media/acs-intro/acs-swarm2.png)

Gli strumenti supportati per la gestione dei contenitori in un cluster Swarm includono in particolare quanto segue:

-   Dokku

-   Docker CLI e Docker Compose

-   Krane

-   Jenkins

Video
------
Annuncio di AzureCon:

> [AZURE.VIDEO azurecon-2015-deep-dive-on-the-azure-container-service-with-mesos]  

Introduzione al servizio contenitore di Azure:

> [AZURE.VIDEO connect-2015-getting-started-developing-with-docker-and-azure-container-service]

Creazione di applicazioni mediante il servizio contenitore di Azure

> [https://channel9.msdn.com/Events/Build/2016/B822]

<!---HONumber=AcomDC_0629_2016-->