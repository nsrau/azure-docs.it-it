---
title: Hosting di contenitore Docker nel cloud di Azure | Microsoft Docs
description: Il servizio contenitore di Azure fornisce un modo per semplificare la creazione, la configurazione e la gestione di un cluster di macchine virtuali che sono preconfigurate per eseguire le applicazioni nei contenitori.
services: container-service
documentationcenter: 
author: rgardler
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, Contenitori, Micro-servizi, Mesos, Azure
ms.assetid: 52da4163-1182-4b2e-be00-4951e5c1da16
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2017
ms.author: rogardle
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 31897e11abfe70ed08381f0d13c6bdabe56c28ed
ms.openlocfilehash: 04fabadb1c713deb007b740369496b61bb5f424f
ms.lasthandoff: 03/02/2017



---
# <a name="introduction-to-docker-container-hosting-solutions-with-azure-container-service"></a>Introduzione alle soluzioni di hosting di contenitori Docker con il servizio contenitore di Azure 
Il servizio contenitore di Azure fornisce un modo per semplificare la creazione, la configurazione e la gestione di un cluster di macchine virtuali preconfigurate per eseguire le applicazioni nei contenitori. Usa una configurazione ottimizzata di strumenti di pianificazione e orchestrazione open source comuni. Ciò consente di usare le competenze già acquisite o di attingere da un consistente e crescente bagaglio di competenze a livello di community per distribuire e gestire applicazioni basate sul contenitore in Microsoft Azure.

![Il servizio contenitore di Azure fornisce uno strumento per gestire applicazioni in contenitori su più host in Azure.](./media/acs-intro/acs-cluster-new.png)

Il servizio contenitore di Azure si avvale del formato di contenitore Docker per garantire che i contenitori di applicazioni siano completamente portabili. Supporta anche la scelta di Marathon e DC/OS, Docker Swarm o Kubernetes per garantire che queste applicazioni possano essere ridimensionate in migliaia, persino decine di migliaia, di contenitori.

Il servizio contenitore di Azure consente di sfruttare i vantaggi delle funzioni di classe enterprise di Azure mantenendo al tempo stesso la portabilità delle applicazioni, inclusi i livelli di orchestrazione.

## <a name="using-azure-container-service"></a>Utilizzo del servizio contenitore di Azure
L'obiettivo del servizio contenitore di Azure è fornire un ambiente host contenitore tramite tecnologie e strumenti open source, che sono attualmente diffusi fra i nostri clienti. A tal fine vengono esposti gli endpoint API standard per l'agente di orchestrazione scelto: DC/OS, Docker Swarm o Kubernetes. Tramite questi endpoint è possibile usare qualsiasi software in grado di comunicare con essi. Ad esempio, nel caso dell'endpoint Docker Swarm, è possibile scegliere di usare l'interfaccia della riga di comando (CLI) Docker. Per DC/OS è possibile scegliere l'interfaccia della riga di comando DCOS. Per Kubernetes, è possibile scegliere `kubectl`.

## <a name="creating-a-docker-cluster-by-using-azure-container-service"></a>Creazione di un cluster Docker con il servizio contenitore di Azure
Per iniziare a usare il servizio contenitore di Azure, distribuire un cluster del servizio tramite il portale (cercare **Servizio contenitore di Azure** nel Marketplace), usando un modello di Azure Resource Manager ([Docker Swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm), [DC/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos) o [Kubernetes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes)) oppure l'[interfaccia della riga di comando di Azure 2.0](container-service-create-acs-cluster-cli.md). I modelli di avvio rapido forniti possono essere modificati per includere una configurazione di Azure aggiuntiva o avanzata. Per altre informazioni, vedere [Distribuire un cluster del servizio contenitore di Azure](container-service-deployment.md).

## <a name="deploying-an-application"></a>Distribuzione di un'applicazione
Il servizio contenitore di Azure consente di scegliere tra Docker Swarm, DC/OS o Kubernetes per l'orchestrazione. La modalità di distribuzione dell'applicazione dipende dall'agente di orchestrazione scelto.

### <a name="using-dcos"></a>Uso di DC/OS
DC/OS è un sistema operativo distribuito basato sul kernel dei sistemi distribuiti Apache Mesos. Apache Mesos fa parte di Apache Software Foundation e annovera tra gli utenti e i collaboratori alcuni dei [nomi più importanti del panorama IT](http://mesos.apache.org/documentation/latest/powered-by-mesos/) .

![Servizio contenitore di Azure configurato per DC/OS che mostra agenti e schemi.](media/acs-intro/dcos.png)

DC/OS e Apache Mesos offrono un set di funzionalità molto ampio:

* Scalabilità collaudata
* Schemi replicati a tolleranza di errore e slave con Apache ZooKeeper
* Supporto per i contenitori formattati Docker
* Isolamento nativo tra le attività con i contenitori Linux
* Pianificazione di più risorse (memoria, CPU, disco e porte)
* Java, Python e API C++ per lo sviluppo di nuove applicazioni parallele
* Interfaccia utente Web per la visualizzazione dello stato del cluster

Per impostazione predefinita, DC/OS in esecuzione nel servizio contenitore di Azure include la piattaforma di orchestrazione Marathon per la pianificazione dei carichi di lavoro. La distribuzione di DC/OS del servizio ACS include tuttavia il pacchetto Mesosphere Universe di servizi che possono essere aggiunti al servizio, tra cui Spark, Hadoop, Cassandra e molto altro ancora.

![Universe DC/OS nel servizio contenitore di Azure](media/dcos/universe.png)

#### <a name="using-marathon"></a>Uso di Marathon
Marathon è un sistema di init e controllo di tutto il cluster per i servizi in cgroups o, nel caso del servizio contenitore di Azure, in contenitori formattati Docker. Marathon offre un'interfaccia utente Web da cui è possibile distribuire le applicazioni. L'accesso avviene tramite un URL simile a `http://DNS_PREFIX.REGION.cloudapp.azure.com`, dove DNS\_PREFIX e REGION vengono definiti in fase di distribuzione. Naturalmente, è anche possibile fornire il proprio nome DNS. Per altre informazioni sull'esecuzione di un contenitore tramite l'interfaccia utente Web di Marathon, vedere [Gestione di contenitori DC/OS tramite l'interfaccia utente Web di Marathon](container-service-mesos-marathon-ui.md).

![Elenco di applicazioni Marathon](media/dcos/marathon-applications-list.png)

È anche possibile usare le API REST per la comunicazione con Marathon. Esistono una serie di librerie client disponibili per ogni strumento. Coprono un'ampia gamma di linguaggi e, naturalmente, è possibile utilizzare il protocollo HTTP in qualsiasi linguaggio. Inoltre, molti strumenti comuni di DevOps forniscono il supporto per Marathon. Ciò fornisce flessibilità massima per il team operativo quando si lavora con un cluster del servizio contenitore di Azure. Per altre informazioni sull'esecuzione di un contenitore tramite l'API REST di Marathon, vedere [Gestione di contenitori DC/OS tramite l'API REST di Marathon](container-service-mesos-marathon-rest.md).

### <a name="using-docker-swarm"></a>Utilizzo di Docker Swarm
Docker Swarm fornisce clustering nativo per Docker. Poiché Docker Swarm serve l'API Docker standard, gli strumenti che già comunicano con un daemon Docker possono usare Swarm per la scalabilità trasparente a più host nel servizio contenitore di Azure.

![Servizio contenitore di Azure configurato per l'uso di Swarm.](media/acs-intro/acs-swarm2.png)

Gli strumenti supportati per la gestione dei contenitori in un cluster Swarm includono in particolare quanto segue:

* Dokku
* Docker CLI e Docker Compose
* Krane
* Jenkins

### <a name="using-kubernetes"></a>Uso di Kubernetes
Kubernetes è un diffuso strumento open source di livello di produzione per l'orchestrazione di contenitori. Kubernetes automatizza la distribuzione, il ridimensionamento e la gestione delle applicazioni nei contenitori. Trattandosi di una soluzione open source che dipende dalla community open source, Kubernetes funziona perfettamente nel servizio contenitore di Azure e permette di distribuirvi contenitori su larga scala.

![Servizio contenitore di Azure configurato per l'uso di Kubernetes.](media/acs-intro/kubernetes.png)

Dispone di un set completo di funzionalità tra cui:
* Scalabilità orizzontale
* Bilanciamento del carico e rilevamento del servizio
* Gestione della configurazione e segreti
* Implementazioni e ripristini automatizzati basati sull'API
* Riparazione automatica




## <a name="videos"></a>Video
Introduzione al servizio contenitore di Azure (101):  

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Container-Service-101/player]
>
>

Compilazione di applicazioni tramite il servizio contenitore di Azure (build 2016)

> [!VIDEO https://channel9.msdn.com/Events/Build/2016/B822/player]
>
>

## <a name="next-steps"></a>Passaggi successivi

Distribuire un cluster del servizio contenitore usando il [portale](container-service-deployment.md) o l'[interfaccia della riga di comando di Azure 2.0](container-service-create-acs-cluster-cli.md).
