---
title: Panoramica dei cluster di Azure e Service Fabric autonomo
description: È possibile creare cluster di Service Fabric in qualsiasi macchina virtuale o computer con Windows Server o Linux. È quindi possibile distribuire ed eseguire applicazioni di Service Fabric in qualsiasi ambiente in cui è presente un set di computer Windows Server o Linux interconnessi, in locale, in Microsoft Azure o con qualsiasi provider di cloud.
author: dkkapur
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: dekapur
ms.custom: sfrev
ms.openlocfilehash: a3627effe10039ded5007f9dd060bf1865929040
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75751152"
---
# <a name="comparing-azure-and-standalone-service-fabric-clusters-on-windows-server-and-linux"></a>Creare cluster di Service Fabric in Azure e autonomi in Windows Server e Linux

Un cluster di Service Fabric è un set di computer fisici o macchine virtuali connessi in rete, in cui vengono distribuiti e gestiti i microservizi. Un computer o una VM che fa parte di un cluster è chiamato nodo del cluster. I cluster possono essere ridimensionati fino a migliaia di nodi. Se si aggiungono nuovi nodi al cluster, Service Fabric ribilancia le repliche e le istanze di partizione del servizio nel numero incrementato di nodi. Le prestazioni complessive dell'applicazione migliorano e la contesa per l'accesso alla memoria si riduce. Se i nodi del cluster non vengono usati in modo efficiente, è possibile ridurre il numero di nodi del cluster. Service Fabric ribilancia di nuovo le repliche e le istanze di partizione nel numero ridotto di nodi per usare al meglio l'hardware in ogni nodo.

Service Fabric permette di creare cluster di Service Fabric in qualsiasi VM o computer con Windows Server o Linux in esecuzione. È quindi possibile distribuire ed eseguire applicazioni di Service Fabric in qualsiasi ambiente in cui è presente un set di computer Windows Server o Linux interconnessi, in locale, in Microsoft Azure o con qualsiasi provider di cloud.

## <a name="benefits-of-clusters-on-azure"></a>Vantaggi dei cluster in Azure

Per semplificare e rendere più affidabili le operazioni e la gestione del cluster, in Azure viene offerta l'integrazione con altre funzionalità e servizi di Azure.

* **Portale di Azure:** il portale di Azure semplifica la creazione e la gestione dei cluster.
* **Gestione risorse di Azure:** l'uso di Gestione risorse di Azure consente di gestire facilmente tutte le risorse usate dal cluster come singola unità e semplifica il tracciamento dei costi e la fatturazione.
* **Cluster di Service Fabric come risorsa di Azure:** un cluster di Service Fabric è una risorsa di Azure, quindi è possibile modellarla in modo analogo alle altre risorse in Azure.
* **Integrazione con l'infrastruttura di Azure** Service Fabric si coordina con l'infrastruttura di Azure per il sistema operativo, la rete e altri aggiornamenti, in modo da migliorare la disponibilità e l'affidabilità delle applicazioni.  
* **Diagnostica:** In Azure viene fornita l'integrazione con diagnostica di Azure e i log di monitoraggio di Azure.
* **Ridimensionamento automatico:** per i cluster in Azure è disponibile la funzionalità predefinita di ridimensionamento automatico, grazie a set di ridimensionamento delle macchine virtuali. In locale e in altri ambienti cloud è necessario sviluppare una funzionalità di ridimensionamento automatico specifica oppure ridimensionare manualmente usando le API esposte da Service Fabric per il ridimensionamento dei cluster.

## <a name="benefits-of-standalone-clusters"></a>Vantaggi dei cluster autonomi

* Per l'hosting di un cluster è possibile scegliere qualsiasi provider di cloud.
* Dopo essere state scritte, le applicazioni di Service Fabric possono essere eseguite in più ambienti di hosting senza alcuna modifica o con cambiamenti minimi.
* Le conoscenze acquisite nella creazione di applicazioni di Service Fabric saranno utili anche per altri ambienti di hosting.
* L'esperienza operativa derivata dall'esecuzione e dalla gestione di cluster di Service Fabric sarà utile anche per altri ambienti.
* Ampia copertura di clienti non limitata da vincoli dell'ambiente di hosting.
* Un livello aggiuntivo di affidabilità e protezione contro interruzioni generalizzate per consentire lo spostamento dei servizi in un altro ambiente di distribuzione in caso di blackout di un centro dati o del provider di servizi cloud.

## <a name="next-steps"></a>Passaggi successivi

* Leggere la panoramica dei [cluster di Service Fabric in Azure](service-fabric-azure-clusters-overview.md)
* Leggere la panoramica dei [cluster di Service Fabric autonomi](service-fabric-standalone-clusters-overview.md)
* Informazioni sulle [Opzioni di supporto Service Fabric](service-fabric-support.md)