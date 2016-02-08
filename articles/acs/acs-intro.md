<properties
   pageTitle="Introduzione al servizio contenitore di Azure | Microsoft Azure"
   description="Il servizio contenitore di Azure (ACS) fornisce un modo per semplificare la creazione, la configurazione e la gestione di un cluster di macchine virtuali che sono preconfigurate per eseguire le applicazioni nei contenitori."
   services="virtual-machines"
   documentationCenter=""
   authors="rgardler"
   manager="nepeters"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, Contenitori, Micro-servizi, Mesos, Azure"/>
   
<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="home-page"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/02/2015"
   ms.author="rogardle"/>

# Introduzione al servizio contenitore di Azure

Il servizio contenitore di Azure (ACS) fornisce un modo per semplificare la creazione, la configurazione e la gestione di un cluster di macchine virtuali che sono preconfigurate per eseguire le applicazioni nei contenitori. Utilizzando una configurazione ottimizzata di strumenti di pianificazione e orchestrazione open source più diffusi, ACS consente di utilizzare le competenze esistenti o disegnare su un corpo di grandi dimensioni e in continua crescita di assistenza della community per distribuire e gestire applicazioni basate sul contenitore in Microsoft Azure.

<br /> ![ACS fornisce uno strumento per gestire applicazioni in container su più host in Azure.](./media/acs-intro/acs-cluster.png) <br /><br />

ACS si avvale di Docker per garantire che i contenitori di applicazioni siano completamente portabili. Supporta inoltre la scelta di Marathon, Chronos e Apache Mesos o Docker Swarm per garantire che queste applicazioni possano essere scalate in migliaia, persino decine di migliaia di contenitori.

Il servizio contenitore di Azure consente di sfruttare i vantaggi delle funzioni del grado dell’organizzazione di Azure mantenendo al tempo stesso la portabilità dell'applicazione, inclusi i livelli di orchestrazione.

Poiché il servizio è in anteprima, coloro che sono interessati a testare il servizio devono [auto candidarsi](http://aka.ms/acspreview). Una volta ottenuto l'accesso in anteprima, un messaggio di posta elettronica verrà inviato con ulteriori dettagli inclusi modelli di distribuzione e istruzioni della guida introduttiva. Per utilizzare il servizio, è necessaria una sottoscrizione di Azure. Se non possiede ancora di una sottoscrizione, è possibile eseguire l'iscrizione per una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).

Utilizzo del servizio contenitore di Azure
-----------------------------

Il nostro obiettivo con il servizio contenitore di Azure è fornire un ambiente host contenitore, tramite strumenti open source e tecnologie, che sono attualmente i più diffusi fra i nostri clienti. A tal fine, vengono esposti gli endpoint API standard per Docker e l'agente di orchestrazione scelto. Utilizzando questi endpoint è possibile utilizzare qualsiasi software in grado di comunicare con essi. Nel caso dell'endpoint Docker Swarm, ad esempio, è possibile scegliere di utilizzare Docker Compose, mentre per Apache Mesos è possibile scegliere di utilizzare DCOS CLI.

Creazione di un Cluster Docker con il servizio contenitore di Azure
-------------------------------------------------------

Dopo aver [richiesto](http://aka.ms/acspreview) e ottenuto l'accesso all'anteprima, è possibile utilizzare uno dei numerosi modelli di gestione risorse di Azure che consente di distribuire il primo cluster tramite il portale di Azure. Utilizzando questi modelli, è possibile creare rapidamente un servizio e iniziare immediatamente la distribuzione di applicazioni. Per iniziare è sufficiente scegliere la dimensione del cluster e se si desidera usare Docker Swarm o Apache Mesos per gestire le applicazioni.

È anche possibile [utilizzare la riga di comando](/documentation/articles/resource-group-template-deploy/) per creare servizi contenitore di Azure utilizzando questi stessi modelli. Dopo aver acquisito familiarità con la struttura di questi modelli si sarà in grado di scrivere i propri e automatizzare completamente la creazione di un cluster del servizio contenitore di Azure.

Il supporto e la documentazione completa verranno forniti per visualizzare in anteprima i partecipanti e verranno pubblicati qui una volta che il servizio viene aperto al pubblico.

Distribuzione di un'applicazione
------------------------

Durante l'anteprima forniamo una scelta di Docker Swarm o Mesos Apache (con framework DCOS Marathon e DCOS Chronos) per l'orchestrazione.

### Utilizzo di Apache Mesos

Apache Mesos è un progetto open source ospitato nella Apache Software Foundation. Vengono elencati alcuni dei [protagonisti in IT](http://mesos.apache.org/documentation/latest/powered-by-mesos/) come utenti e collaboratori.

![ACS configurato per Swarm che mostra agenti e schemi.](media/acs-intro/acs-mesos.png)

Mesos comprime un set di funzionalità eccezionali.

-   Scalabilità a 10.000 dei nodi

-   Schemi replicati a tolleranza di errore e slave utilizzando ZooKeeper

-   Supporto per i contenitori Docker

-   Isolamento nativo tra le attività con i contenitori Linux

-   Pianificazione di più risorse (memoria, CPU, disco e porte)

-   Java, Python e APIs C++ per lo sviluppo di nuove applicazioni parallele

-   Interfaccia utente Web per la visualizzazione dello stato del cluster

Mesos include il supporto per un numero elevato di [framework](http://mesos.apache.org/documentation/latest/frameworks/) che possono essere utilizzati per la pianificazione dei carichi di lavoro del servizio contenitore di Azure. Per impostazione predefinita, ACS include i framework Marathon e Chronos.

#### Utilizzo di Marathon e Chronos

Marathon è un sistema di init e controllo di tutto il cluster per i servizi in cgroups o, nel caso di ACS, in contenitori Docker. Si tratta di un partner ideale di Chronos, un'utilità di pianificazione processi a tolleranza d'errore per Mesos che gestisce le dipendenze e le pianificazioni in base al tempo.

Marathon e Chronos forniscono un'interfaccia utente Web da cui è possibile distribuire le applicazioni. Si accederà a questo con un URL simile a `http://DNS\_PREFIX.REGION.cloudapp.azure.com` dove DNS\_PREFIX e REGION sono entrambi definiti in fase di distribuzione. Naturalmente, è anche possibile fornire il proprio nome DNS.

Inoltre, è possibile utilizzare le API REST per la comunicazione con Marathon e Chronos. Sono disponibili numerose librerie client per ogni strumento, che coprono un'ampia gamma di linguaggi e, naturalmente, è possibile utilizzare il protocollo HTTP in qualsiasi linguaggio. Inoltre, molti strumenti comuni di DevOps forniscono il supporto per queste utilità di pianificazione. Ciò fornisce flessibilità massima per il team operativo quando si lavora con un cluster ACS.

Il supporto e la documentazione completa verranno forniti per visualizzare in anteprima i partecipanti e verranno pubblicati qui una volta che il servizio viene aperto al pubblico.

### Utilizzo di Docker Swarm

Docker Swarm fornisce clustering nativo per Docker. Poiché Docker Swarm serve l'API Docker standard, gli strumenti che già comunicano con un daemon Docker possono usare Swarm per la scalabilità trasparente a più host nel servizio contenitore di Azure.

![ACS configurato per l'utilizzo di Apache Mesos, che mostra jumpbox, agenti e schemi.](media/acs-intro/acs-swarm.png)

Gli strumenti supportati per la gestione dei contenitori in un cluster Swarm includono in particolare quanto segue:

-   Dokku

-   Docker CLI e Docker Compose

-   Krane

-   Jenkins

Il supporto e la documentazione completa verranno forniti per visualizzare in anteprima i partecipanti e verranno pubblicati qui una volta che il servizio viene aperto al pubblico.

Ottenere l'accesso
--------------

Poiché il servizio è in anteprima coloro che sono interessati a testare il servizio devono [auto candidarsi](http://aka.ms/acspreview). Innanzitutto è necessaria una sottoscrizione di Azure. Se non possiede ancora di una sottoscrizione, è possibile eseguire l'iscrizione per una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).

Video
------
Annuncio di AzureCon:

> [AZURE.VIDEO azurecon-2015-deep-dive-on-the-azure-container-service-with-mesos]  

Introduzione a ACS:

> [AZURE.VIDEO connect-2015-getting-started-developing-with-docker-and-azure-container-service]

<!---HONumber=AcomDC_0128_2016-->