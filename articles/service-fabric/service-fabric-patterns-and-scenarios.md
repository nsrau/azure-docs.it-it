---
title: Modelli e scenari per Azure Service Fabric | Documentazione Microsoft
description: Informazioni sulle procedure consigliate e collaudate, sui modelli riutilizzabili per progettare, sviluppare e gestire i microservizi in Service Fabric.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: d5aa75ff-98b9-4573-a2e5-7f5ab288157a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/04/2017
ms.author: ryanwi
ms.openlocfilehash: 8ac4a10b9030a4d56824d1a42ed2729ebb2d208e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="service-fabric-patterns-and-scenarios"></a>Modelli e scenari per Service Fabric
Se si vogliono sviluppare microservizi su larga scala con Azure Service Fabric, può essere utile imparare dagli esperti che hanno progettato questa piattaforma distribuita come servizio (PaaS, Platform as a Service). Per iniziare occorre scegliere l'architettura appropriata, per poi imparare le procedure per ottimizzare le risorse per l'applicazione. Il corso [Service Fabric Patterns and Practices](https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=mudwqISGD_6005167344) (Modelli e procedure consigliate per Service Fabric) offre le risposte alle domande più frequenti dei clienti reali sugli scenari di utilizzo e le aree applicative di Service Fabric.
 
È possibile scoprire come progettare, sviluppare e gestire i microservizi in Service Fabric usando procedure consigliate e modelli collaudati riutilizzabili. Si può iniziare da una panoramica di Service Fabric per poi procedere con vari argomenti di approfondimento come l'ottimizzazione e la sicurezza del cluster, la migrazione di applicazioni legacy, IoT su vasta scala, l'hosting di motori di gioco e altro ancora. Sono anche disponibili informazioni su come controllare il recapito continuo per diversi carichi di lavoro e dettagli sul supporto di Linux e i contenitori. 

## <a name="introduction"></a>Introduzione
Esplorare le procedure consigliate e raccogliere informazioni per la scelta di una piattaforma distribuita come servizio (PaaS, Platform as a Service) o un'infrastruttura distribuita come servizio (IaaS, Infrastructure as a Service). Informazioni dettagliate sui principi di progettazione delle applicazioni collaudati.

<table><tr><th>Video</th><th>Presentazione di PowerPoint</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=N2KwbbSGD_6405167344">
<img src="./media/service-fabric-patterns-and-scenarios/intro.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=mudwqISGD_6005167344">Introduzione a Service Fabric</a></td></tr>
</table>

## <a name="cluster-planning-and-management"></a>Pianificazione e gestione del cluster
Questa presentazione di Azure Service Fabric include informazioni sulla pianificazione della capacità, l'ottimizzazione del cluster e la sicurezza del cluster.

<table><tr><th>Video</th><th>Presentazione di PowerPoint</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=cyDYZcSGD_2805167344">
<img src="./media/service-fabric-patterns-and-scenarios/cluster.png" WIDTH="360" HEIGHT="244">
</a></td><td> <a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=E5B3nJSGD_805167344">Pianificazione e gestione del cluster</a></td></tr>
</table>

## <a name="hyper-scale-web"></a>Web con iperscalabilità
Una presentazione dei concetti relativi al Web con iperscalabilità, incluse disponibilità e affidabilità, iperscalabilità e gestione dello stato.

<table><tr><th>Video</th><th>Presentazione di PowerPoint</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=NgldAdSGD_405167344">
<img src="./media/service-fabric-patterns-and-scenarios/hyperscaleweb.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=CPMLBLSGD_7705167344">Web con iperscalabilità</a></td></tr>
</table>

## <a name="iot"></a>IoT
Esplorare Internet delle cose (IoT) nel contesto di Azure Service Fabric, inclusi pipeline IoT di Azure, multitenancy e IoT su vasta scala.

<table><tr><th>Video</th><th>Presentazione di PowerPoint</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=naFUVeSGD_1505167344">
<img src="./media/service-fabric-patterns-and-scenarios/iot.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=kfqFWMSGD_6205167344">IoT</a></td></tr>
</table>

## <a name="gaming"></a>Giochi
Informazioni sui giochi a turni, i giochi interattivi e l'hosting di motori di gioco esistenti.

<table><tr><th>Video</th><th>Presentazione di PowerPoint</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=6wECzeSGD_3805167344">
<img src="./media/service-fabric-patterns-and-scenarios/gaming.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=kfqFWMSGD_6205167344">Giochi</a></td></tr>
</table>

## <a name="continuous-delivery"></a>Recapito continuo
Presentazione dei concetti, tra i quali integrazione continua/recapito continuo con Visual Studio Team Services, flusso di lavoro di compilazione/creazione del pacchetto/pubblicazione, programma di installazione per più ambienti e pacchetto del servizio/condivisione.

<table><tr><th>Video</th><th>Presentazione di PowerPoint</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=78h5ofSGD_305167344">
<img src="./media/service-fabric-patterns-and-scenarios/cd.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=VlENvOSGD_105167344">Recapito continuo</a></td></tr>
</table>

## <a name="migration"></a>Migrazione
Informazioni sulla migrazione da un servizio cloud, oltre alla migrazione di app legacy.

<table><tr><th>Video</th><th>Presentazione di PowerPoint</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=hd1cMgSGD_5605167344">
<img src="./media/service-fabric-patterns-and-scenarios/migration.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=GQAq4QSGD_8305167344">Migrazione</a></td></tr>
</table>

## <a name="containers-and-linux-support"></a>Contenitori e supporto di Linux
Ottenere la risposta alla domanda "Perché usare i contenitori?" Informazioni sull'anteprima per i contenitori di Windows, il supporto di Linux e l'orchestrazione dei contenitori Linux. Sono disponibili anche indicazioni pratiche per la migrazione di app .NET Core a Linux.

<table><tr><th>Video</th><th>Presentazione di PowerPoint</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=V1ERJhSGD_305167344">
<img src="./media/service-fabric-patterns-and-scenarios/containers.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=mlYsZRSGD_2105167344">Contenitori e supporto Linux</a></td></tr>
</table>

## <a name="next-steps"></a>Passaggi successivi
Dopo aver raccolto informazioni dettagliate sui modelli e gli scenari di utilizzo di Service Fabric, è possibile procedere con la lettura e scoprire come [creare e gestire i cluster](service-fabric-deploy-anywhere.md), [eseguire la migrazione delle app di Servizi cloud a Service Fabric](service-fabric-cloud-services-migration-worker-role-stateless-service.md), [configurare il recapito continuo](service-fabric-set-up-continuous-integration.md) e [distribuire i contenitori](service-fabric-containers-overview.md).
