---
title: Panoramica dei cluster di Service Fabric autonomi | Microsoft Docs
description: I cluster di Service Fabric vengono eseguiti in Windows Server e Linux, per poter distribuire e ospitare le applicazioni di Service Fabric in qualsiasi ambiente che esegue Windows Server o Linux.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/01/2019
ms.author: dekapur
ms.openlocfilehash: b4b7759d1dc23c1a1b3a9b5aeb2a181923e14d40
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58670711"
---
# <a name="overview-of-service-fabric-standalone-clusters"></a>Panoramica dei cluster di Service Fabric autonomi

Un cluster di Service Fabric è un set di computer fisici o macchine virtuali connessi in rete, in cui vengono distribuiti e gestiti i microservizi. Un computer o una VM che fa parte di un cluster è chiamato nodo del cluster. I cluster possono essere ridimensionati fino a migliaia di nodi. Se si aggiungono nuovi nodi al cluster, Service Fabric ribilancia le repliche e le istanze di partizione del servizio nel numero incrementato di nodi. Le prestazioni complessive dell'applicazione migliorano e la contesa per l'accesso alla memoria si riduce. Se i nodi del cluster non vengono usati in modo efficiente, è possibile ridurre il numero di nodi del cluster. Service Fabric ribilancia di nuovo le repliche e le istanze di partizione nel numero ridotto di nodi per usare al meglio l'hardware in ogni nodo.

Un tipo di nodo definisce le dimensioni, il numero e le proprietà di un set di nodi nel cluster. Ogni tipo di nodo può quindi essere aumentato o ridotto in modo indipendente, avere diversi set di porte aperte e avere metriche per la capacità diverse. I tipi di nodo vengono usati per definire i ruoli relativi a un set di nodi del cluster, ad esempio "front-end" o "back-end". Il cluster può avere più di un tipo di nodo, ma il tipo di nodo primario deve essere costituito da almeno cinque macchine virtuali per i cluster di produzione (o da almeno tre macchine virtuali per i cluster di test). I [servizi di sistema Service Fabric](service-fabric-technical-overview.md#system-services) vengono inseriti nei nodi del tipo di nodo primario.

Il processo di creazione di un cluster di Service Fabric locale è simile al processo di creazione di un cluster in qualsiasi ambiente cloud con un set di macchine virtuali. I passaggi iniziali per effettuare il provisioning delle macchine virtuali saranno controllati dal provider cloud o dall'ambiente locale corrente. Dopo aver ottenuto un set di macchine virtuali con connettività di rete abilitata, i passaggi successivi per configurare il pacchetto di Service Fabric, modificare le impostazioni del cluster ed eseguire gli script di creazione e gestione del cluster risulteranno identici. In questo modo, l'esperienza e le conoscenze relative all'uso e alla gestione dei cluster di Service Fabric potranno essere usate anche quando si scelgono nuovi ambienti host.

## <a name="cluster-security"></a>Sicurezza del cluster
Un cluster di Service Fabric è una risorsa di cui si è proprietari.  È responsabilità dell'utente proteggere i cluster per evitare che utenti non autorizzati si connettano a essi. Un cluster sicuro è particolarmente importante quando si eseguono carichi di lavoro nel cluster.

### <a name="node-to-node-security"></a>Sicurezza da nodo a nodo
La sicurezza da nodo a nodo protegge le comunicazioni tra le macchine virtuali o i computer di un cluster. Questo scenario di sicurezza assicura che solo i computer autorizzati a connettersi al cluster possano partecipare all'hosting di applicazioni e servizi nel cluster. Service Fabric usa certificati X.509 per proteggere un cluster e fornire le funzionalità di sicurezza dell'applicazione.  Un certificato cluster è necessario per proteggere il traffico del cluster e fornire l'autenticazione per il cluster e il server.  I certificati autofirmati possono essere usati per i cluster di test, ma per proteggere i cluster di produzione occorre usare un certificato di un'Autorità di certificazione attendibile.

Per i cluster Windows autonomi è possibile abilitare anche la sicurezza di Windows. Se sono presenti Windows Server 2012 R2 e Active Directory, è consigliabile usare la sicurezza di Windows con account del servizio gestito del gruppo. In caso contrario, usare la sicurezza di Windows con account di Windows.

Per altre informazioni, vedere [Sicurezza da nodo a nodo](service-fabric-cluster-security.md#node-to-node-security)

### <a name="client-to-node-security"></a>Sicurezza da client a nodo
La sicurezza da client a nodo autentica i client e aiuta a proteggere la comunicazione tra un client e i singoli nodi del cluster. Questo tipo di sicurezza aiuta a garantire che solo gli utenti autorizzati possano accedere al cluster e alle applicazioni distribuite nel cluster. I client vengono identificati in modo univoco tramite le credenziali di sicurezza del relativo certificato X.509. È possibile usare un numero qualsiasi di certificati client facoltativi per autenticare i client di amministrazione o utente con il cluster.

Oltre ai certificati client, è possibile configurare Azure Active Directory anche per autenticare i client con il cluster.

Per altre informazioni, vedere [Sicurezza da client a nodo](service-fabric-cluster-security.md#client-to-node-security)

### <a name="role-based-access-control-rbac"></a>Controllo degli accessi in base al ruolo
Service Fabric supporta anche il controllo di accesso per limitare l'accesso a determinate operazioni di cluster per gruppi di utenti diversi. In questo modo il cluster è più sicuro. Per i client che si connettono a un cluster sono supportati due tipi di controllo di accesso diversi: il ruolo di amministratore e il ruolo utente.  

Per altre informazioni, vedere [Controllo degli accessi in base al ruolo](service-fabric-cluster-security.md#role-based-access-control-rbac).

## <a name="scaling"></a>Ridimensionamento

Le richieste delle applicazioni cambiano nel tempo. Potrebbe essere necessario aumentare le risorse del cluster per supportare l'aumento del carico di lavoro delle applicazioni o del traffico di rete oppure ridurre le risorse del cluster quando le richieste si riducono. Dopo aver creato un cluster di Service Fabric, è possibile scalare il cluster in orizzontale (modificare il numero di nodi) o in verticale (modificare le risorse dei nodi). È possibile ridimensionare il cluster in qualsiasi momento, anche quando sono in esecuzione carichi di lavoro nel cluster. Quando si ridimensiona il cluster, vengono automaticamente ridimensionate anche le applicazioni.

Per altre informazioni, vedere [Ridimensionamento dei cluster autonomi](service-fabric-cluster-scaling-standalone.md).

## <a name="upgrading"></a>Aggiornamento

Un cluster autonomo è una risorsa che si possiede interamente. Perciò si è responsabili dell'applicazione delle patch al sistema operativo sottostante e dell'avvio degli aggiornamenti dell'infrastruttura. È possibile configurare il cluster per ricevere gli aggiornamenti automatici del runtime quando Microsoft rilascia una nuova versione oppure selezionare la versione supportata del runtime che si vuole. Oltre ad aggiornare l'infrastruttura, è possibile anche applicare patch al sistema operativo e aggiornare la configurazione del cluster, ad esempio i certificati o le porte delle applicazioni. 

Per altre informazioni, vedere [Aggiornamento dei cluster autonomi](service-fabric-cluster-upgrade-standalone.md).

## <a name="supported-operating-systems"></a>Sistemi operativi supportati
È possibile creare cluster in macchine virtuali o in computer che eseguono questi sistemi operativi (Linux non è ancora supportato):

* Windows Server 2012 R2
* Windows Server 2016 

## <a name="next-steps"></a>Passaggi successivi
Leggere altre informazioni sulla [sicurezza](service-fabric-cluster-security.md), il [ridimensionamento](service-fabric-cluster-scaling-standalone.md) e l'[aggiornamento](service-fabric-cluster-upgrade-standalone.md) dei cluster autonomi.

Informazioni sulle [opzioni di supporto di Service Fabric](service-fabric-support.md).