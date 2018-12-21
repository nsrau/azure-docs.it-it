---
title: Panoramica di Azure Service Fabric Mesh | Microsoft Docs
description: Informazioni su Azure Service Fabric Mesh. Service Fabric Mesh consente di distribuire e ridimensionare l'applicazione senza preoccuparsi delle esigenze infrastrutturali dell'applicazione.
services: service-fabric-mesh
keywords: ''
author: rwike77
ms.author: ryanwi
ms.date: 10/1/2018
ms.topic: overview
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: da5370e274aa1904f803227d8c85912a7d26c533
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2018
ms.locfileid: "53000650"
---
# <a name="what-is-service-fabric-mesh"></a>Informazioni su Service Fabric Mesh

Questo video offre una rapida panoramica del servizio Service Fabric Mesh.
> [!VIDEO https://www.youtube.com/embed/7qWeVGzAid0]

Azure Service Fabric Mesh è un servizio completamente gestito che consente agli sviluppatori di distribuire applicazioni di microservizi senza dover gestire macchine virtuali, archiviazione o connettività di rete. Le applicazioni ospitate in Service Fabric Mesh vengono eseguite e ridimensionate senza preoccuparsi dell'infrastruttura su cui sono basate.  Service Fabric Mesh è costituito da cluster di migliaia di computer.  Tutte le operazioni cluster non sono visibili dallo sviluppatore. È sufficiente caricare il codice e specificare le risorse necessarie, i requisiti di disponibilità e i limiti delle risorse.  Service Fabric Mesh alloca automaticamente l'infrastruttura e gestisce anche gli errori di infrastruttura, assicurandosi che le applicazioni siano a disponibilità elevata. È sufficiente preoccuparsi dell'integrità e della velocità di risposta dell'applicazione, ignorando l'infrastruttura.  

[!INCLUDE [preview note](./includes/include-preview-note.md)]

Questo articolo offre una panoramica dei vantaggi chiave di Service Fabric Mesh.

## <a name="great-developer-experience"></a>Esperienza ottimizzata per gli sviluppatori

Service Fabric Mesh supporta qualsiasi framework o linguaggio di programmazione eseguibile in un contenitore. Il supporto degli strumenti di Visual Studio 2017 e Visual Studio Code garantisce un'esperienza di modifica e debug avanzata per le applicazioni .NET e .NET Core. 

Con Service Fabric Mesh è possibile:

- Spostare facilmente applicazioni esistenti nei contenitori per modernizzare ed eseguire le applicazioni correnti su vasta scala.
- Creare e distribuire nuove applicazioni di microservizi su vasta scala in Azure.  Implementare l'integrazione con altri servizi di Azure o applicazioni esistenti in esecuzione nei contenitori. Ogni microservizio fa parte di un'applicazione sicura con isolamento rete e con criteri di governance delle risorse definiti per core CPU, memoria, spazio su disco e altro ancora.
- Implementare l'integrazione con applicazioni esistenti ed estenderle senza apportare modifiche alle applicazioni. Usare la propria rete virtuale per connettere l'applicazione esistente alla nuova applicazione.  
- Modernizzare le applicazioni di Servizi Cloud esistenti eseguendo la migrazione a Service Fabric Mesh.  

## <a name="simple-operational-lifecycle"></a>Ciclo di vita operativo semplice

È possibile gestire facilmente applicazioni in esecuzione, inclusi gli aggiornamenti delle applicazioni e il controllo delle versioni, il monitoraggio delle applicazioni e il debug in ambienti di produzione. Queste applicazioni possono essere costituite da un singolo microservizio o da più microservizi isolati nella propria rete. Le applicazioni vengono eseguite in modo efficiente e rapido in termini di distribuzione, posizionamento e tempi di failover.

Con Service Fabric Mesh è possibile:

- Distribuire e gestire le applicazioni senza dover effettuare il provisioning e gestire l'infrastruttura in modo esplicito.  Service Fabric Mesh esegue il provisioning, aggiorna, applica le patch e gestisce automaticamente l'infrastruttura sottostante.
- Configurare l'integrazione continua, usando gli strumenti integrati per creare pacchetti delle applicazioni e distribuirle facilmente.
- Sfruttare tutte le funzionalità delle risorse di Azure Resource Manager, ad esempio audit trail e [Controllo degli accessi in base al ruolo](/azure/role-based-access-control/overview), dal momento che tutte le risorse, come applicazioni, servizi, segreti e così via, distribuite nel servizio Service Fabric Mesh in Azure sono risorse di Azure Resource Manager.
- Distribuire e gestire risorse usando il [portale di Azure](https://portal.azure.com), i modelli di Resource Manager o le librerie dell'interfaccia della riga di comando di Azure o di PowerShell.
- Configurare il monitoraggio e gli avvisi di funzionamento usando [Application Insights](/azure/application-insights/) (o uno strumento a scelta) per acquisire le tracce operative e diagnostiche dalla piattaforma.
- Accedere alle informazioni di diagnostica dell'applicazione generate dal modello dell'applicazione usando [Application Insights](/azure/application-insights/) o uno strumento a scelta.
- Ottimizzare l'utilizzo delle risorse specificando regole di scalabilità automatica per i servizi nella definizione dell'applicazione.

## <a name="mission-critical-platform-capabilities"></a>Funzionalità strategiche della piattaforma

Service Fabric Mesh crea una raccolta di cluster che si estendono oltre le [zone di disponibilità di Azure](/azure/availability-zones/az-overview) e/o i confini delle aree geopolitiche. Per descrivere le applicazioni, si usa un set di finalità, ad esempio la scalabilità, i requisiti hardware, i requisiti di durabilità e i criteri di sicurezza.  Durante la distribuzione dell'applicazione Service Fabric Mesh individua il punto ottimale in cui eseguirla.

Con Service Fabric Mesh è possibile:

- Sfruttare i vantaggi di disponibilità elevata, riduzione/aumento della scalabilità, individuabilità, orchestrazione, routing dei messaggi, messaggistica affidabile, aggiornamenti senza tempo di inattività, gestione di sicurezza/segreti, ripristino di emergenza, gestione dello stato, gestione della configurazione e transazioni distribuite.
- Scegliere tra più modelli di applicazione durante la creazione di applicazioni.
- Usare le funzionalità della piattaforma esposte tramite endpoint REST utilizzando binding specifici del linguaggio generati con Swagger.
- Distribuire applicazioni nelle [zona di disponibilità](/azure/availability-zones/az-overview) e in più aree per l'affidabilità a livello geografico.
- Usare tutte le funzionalità di sicurezza e conformità offerte da Azure.

## <a name="next-steps"></a>Passaggi successivi

Bastano pochi passaggi per distribuire un progetto di esempio con Visual Studio. Per altre informazioni, vedere [Guida introduttiva: Creare e distribuire un'app Web in Azure Service Fabric Mesh](service-fabric-mesh-quickstart-dotnet-core.md). 

Risposte a [domande comuni](service-fabric-mesh-faq.md).


<!-- Links -->

[service-fabric-overview]: ../service-fabric/service-fabric-overview.md
