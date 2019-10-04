---
title: Procedure consigliate per la progettazione di applicazioni Azure Service Fabric | Microsoft Docs
description: Procedure consigliate per lo sviluppo di applicazioni Service Fabric.
services: service-fabric
documentationcenter: .net
author: markfussell
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/18/2019
ms.author: mfussell
ms.openlocfilehash: eec5daf0100d527886a508f5adbdb2b0e3010b09
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262271"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Procedure consigliate per la progettazione di applicazioni Service Fabric Azure

Questo articolo fornisce indicazioni sulle procedure consigliate per la creazione di applicazioni e servizi in Azure Service Fabric.
 
## <a name="get-familiar-with-service-fabric"></a>Acquisire familiarità con Service Fabric
* [Per informazioni su Service Fabric](service-fabric-content-roadmap.md) , vedere l'articolo.
* Scopri di più sugli [scenari Service Fabric applicazioni](service-fabric-application-scenarios.md).
* Per informazioni sulle scelte dei modelli di programmazione, vedere [Panoramica del modello di programmazione Service Fabric](service-fabric-choose-framework.md).



## <a name="application-design-guidance"></a>Linee guida per la progettazione di applicazioni
Acquisire familiarità con l' [architettura generale](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) delle applicazioni Service Fabric e le relative [considerazioni di progettazione](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations).

### <a name="choose-an-api-gateway"></a>Scegliere un gateway API
Usare un servizio gateway API che comunica con i servizi back-end che possono quindi essere scalati orizzontalmente. I servizi del gateway API più comuni usati sono:

- [Gestione API di Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-api-management-overview), [integrato con Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-api-management).
- [Hub](https://docs.microsoft.com/azure/iot-hub/) eventi di Azure o [Hub eventi di Azure](https://docs.microsoft.com/azure/event-hubs/), usando [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor) per leggere le partizioni dell'hub eventi.
- [Træfik il proxy inverso](https://blogs.msdn.microsoft.com/azureservicefabric/2018/04/05/intelligent-routing-on-service-fabric-with-traefik/), usando il [provider di Service fabric di Azure](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/).
- [Gateway applicazione di Azure](https://docs.microsoft.com/azure/application-gateway/).

   > [!NOTE] 
   > Il gateway applicazione Azure non è integrato direttamente con Service Fabric. Gestione API di Azure è in genere la scelta migliore.
- Il gateway applicazione Web [ASP.NET Core](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-aspnetcore) personalizzato.

### <a name="stateless-services"></a>Servizi senza stato
È consigliabile iniziare sempre creando servizi senza stato usando [Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction) e archiviando lo stato in un database di azure, Azure Cosmos DB o archiviazione di Azure. Lo stato esternalizzato è l'approccio più familiare per la maggior parte degli sviluppatori. Questo approccio consente anche di sfruttare le funzionalità di query nell'archivio.  

### <a name="when-to-use-stateful-services"></a>Quando usare i servizi con stato
Si considerino i servizi con stato quando si dispone di uno scenario di bassa latenza ed è necessario tenere i dati vicini al calcolo. Alcuni scenari di esempio includono dispositivi gemelli digitali, stato del gioco, stato della sessione, memorizzazione nella cache dei dati di un database e flussi di lavoro a esecuzione prolungata per tenere traccia delle chiamate ad altri servizi.

Decidere l'intervallo di tempo di conservazione dei dati:

- **Dati memorizzati nella cache**. Usare la memorizzazione nella cache quando la latenza per gli archivi esterni costituisce un problema. Usare un servizio con stato come cache di dati o prendere in considerazione l'uso di [SoCreate open source Service Fabric cache distribuita](https://github.com/SoCreate/service-fabric-distributed-cache). In questo scenario non è necessario preoccuparsi se si perdono tutti i dati nella cache.
- **Dati con limiti temporali**. In questo scenario, è necessario mantenere i dati vicini per il calcolo per un certo periodo di tempo per la latenza, ma è possibile perdere i dati in caso di *emergenza*. Ad esempio, in molte soluzioni Internet, i dati devono essere vicini al calcolo, ad esempio quando viene calcolata la temperatura media negli ultimi giorni, ma se i dati vengono persi, i punti dati specifici registrati non sono importanti. Inoltre, in questo scenario non si è in genere interessati al backup dei singoli punti dati. È possibile eseguire il backup solo dei valori medi calcolati che vengono scritti periodicamente nell'archiviazione esterna.  
- **Dati a lungo termine**. Reliable Collections può archiviare i dati in modo permanente. In questo caso, tuttavia, è necessario [prepararsi per il ripristino di emergenza](https://docs.microsoft.com/azure/service-fabric/service-fabric-disaster-recovery), inclusa la [configurazione dei criteri di backup periodici](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-configure-periodic-backup) per i cluster. In pratica, si configura cosa accade se il cluster viene eliminato in un'emergenza, in cui è necessario creare un nuovo cluster e come distribuire le nuove istanze dell'applicazione e recuperare dal backup più recente.

Risparmio sui costi e miglioramento della disponibilità:
- È possibile ridurre i costi usando i servizi con stato perché non si incorrono i costi di accesso ai dati e delle transazioni dall'archivio remoto e perché non è necessario usare un altro servizio, ad esempio cache di Azure per Redis.
- L'uso di servizi con stato principalmente per l'archiviazione e non per il calcolo è costoso e non è consigliabile. Si pensi ai servizi con stato come calcolo con archiviazione locale a basso costo.
- Rimuovendo le dipendenze da altri servizi, è possibile migliorare la disponibilità del servizio. La gestione dello stato con la disponibilità elevata nel cluster isola da altri tempi di inattività del servizio o problemi di latenza.

## <a name="how-to-work-with-reliable-services"></a>Come usare Reliable Services
Service Fabric Reliable Services consente di creare facilmente servizi con e senza stato. Per ulteriori informazioni, vedere [Introduzione a reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction).
- Rispettare sempre il [token di annullamento](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-lifecycle#stateful-service-primary-swaps) nel `RunAsync()` metodo per i servizi con e senza stato e `ChangeRole()` il metodo per i servizi con stato. In caso contrario, Service Fabric non sa se il servizio può essere chiuso. Se, ad esempio, non si rispetta il token di annullamento, è possibile che si verifichino tempi di aggiornamento dell'applicazione molto più lunghi.
-   Aprire e chiudere i [listener di comunicazione](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication) in modo tempestivo e rispettare i token di annullamento.
-   Non combinare mai codice di sincronizzazione con codice asincrono. Ad esempio, non usare `.GetAwaiter().GetResult()` nelle chiamate asincrone. Usare async fino a questo *punto* nello stack di chiamate.

## <a name="how-to-work-with-reliable-actors"></a>Come usare Reliable Actors
Service Fabric Reliable Actors consente di creare facilmente attori virtuali con stato. Per ulteriori informazioni, vedere [Introduzione a reliable Actors](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction).

- Prendere in considerazione l'uso della messaggistica pub/sub tra gli attori per la scalabilità dell'applicazione. Gli strumenti che forniscono questo servizio includono [SoCreate open source Service Fabric pub/sub](https://service-fabric-pub-sub.socreate.it/) e il [bus di servizio di Azure](https://docs.microsoft.com/azure/service-bus/).
- Rendere lo stato dell'attore il più [granulare possibile](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices).
- Gestire il [ciclo di vita dell'attore](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices). Eliminare gli attori se non verranno più usati. L'eliminazione di attori non necessari è particolarmente importante quando si usa il [provider di stato volatile](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#state-persistence-and-replication), perché tutto lo stato viene archiviato in memoria.
- A causa della [concorrenza basata su turni](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction#concurrency), gli attori vengono utilizzati in modo ottimale come oggetti indipendenti. Non creare grafici di chiamate a metodi sincroni multiattore (ognuno dei quali più probabilmente diventa una chiamata di rete separata) o creare richieste di attori circolari. Questi influiscono significativamente sulle prestazioni e sulla scalabilità.
- Non combinare codice di sincronizzazione con codice asincrono. Usare Async in modo coerente per evitare problemi di prestazioni.
- Non eseguire chiamate con esecuzione prolungata negli attori. Le chiamate con esecuzione prolungata bloccano altre chiamate allo stesso attore, a causa della concorrenza basata su turni.
- Se si sta comunicando con altri servizi usando [Service Fabric comunicazione remota](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-remoting) e si sta `ServiceProxyFactory`creando un, creare la Factory a livello di [servizio Actor](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-using) e *non* a livello di attore.


## <a name="application-diagnostics"></a>Diagnostica applicazioni
Approfondire l'aggiunta della [registrazione dell'applicazione](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) nelle chiamate al servizio. Consente di diagnosticare gli scenari in cui i servizi vengono chiamati reciprocamente. Ad esempio, quando un chiama B chiama C chiama D, la chiamata potrebbe non riuscire in qualsiasi posizione. Se la registrazione non è sufficiente, è difficile diagnosticare gli errori. Se i servizi eseguono troppe registrazioni a causa di volumi di chiamate, assicurarsi di registrare almeno gli errori e gli avvisi.

## <a name="iot-and-messaging-applications"></a>Applicazioni e messaggistica
Quando si leggono i messaggi dall' [Hub Azure](https://docs.microsoft.com/azure/iot-hub/) o hub [eventi](https://docs.microsoft.com/azure/event-hubs/)di Azure, usare [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor). ServiceFabricProcessor si integra con Service Fabric Reliable Services per mantenere lo stato di lettura dalle partizioni dell'hub eventi e inserisce i nuovi messaggi nei servizi tramite il `IEventProcessor::ProcessEventsAsync()` metodo.


## <a name="design-guidance-on-azure"></a>Linee guida per la progettazione in Azure
* Visitare il [centro architetture di Azure](https://docs.microsoft.com/azure/architecture/microservices/) per indicazioni sulla progettazione per la creazione di microservizi [in Azure](https://docs.microsoft.com/azure/architecture/microservices/).

* Per informazioni sull' [uso di Service Fabric nei servizi di gioco](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf), vedere [Introduzione ad Azure per i giochi](https://docs.microsoft.com/gaming/azure/) .
