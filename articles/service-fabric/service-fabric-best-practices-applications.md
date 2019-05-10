---
title: Azure Service Fabric application design le procedure consigliate | Microsoft Docs
description: Procedure consigliate per lo sviluppo di applicazioni di Service Fabric.
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
ms.date: 04/26/2019
ms.author: msfussell
ms.openlocfilehash: 55f043effc7cdb102acea856e89c58f660d0cde5
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65237748"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Azure Service Fabric application design le procedure consigliate

Questo articolo vengono fornite procedure consigliate per la compilazione di applicazioni e servizi in Service Fabric.
 
## <a name="get-familiar-with-service-fabric"></a>Acquisire familiarità con Service Fabric
* [Pertanto, si vuole informazioni su Service Fabric?](service-fabric-content-roadmap.md)
* Informazioni su [scenari applicativi di Service Fabric](service-fabric-application-scenarios.md)
* Quindi conoscere le scelte di modello di programmazione con [Cenni preliminari sul modello di programmazione di Service Fabric](service-fabric-choose-framework.md)



## <a name="application-design-guidance"></a>Indicazioni per la progettazione dell'applicazione
Acquisire familiarità con le [architettura generale](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) di un'applicazione di Service Fabric e i relativi [considerazioni di progettazione](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations).

### <a name="choose-an-api-gateway"></a>Scegliere un gateway API
Usare un servizio del gateway API che comunica ai servizi back-end che possono quindi essere aumentati. I servizi di gateway API più comuni usati sono:

- [Gestione API di Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-api-management-overview), ovvero [integrato con Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-api-management)
- [IoT Hub di Azure](https://docs.microsoft.com/azure/iot-hub/) oppure [hub eventi di Azure](https://docs.microsoft.com/azure/event-hubs/) utilizzando le [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor) per leggere da partizioni di Hub eventi
- [Proxy inverso Træfik](https://blogs.msdn.microsoft.com/azureservicefabric/2018/04/05/intelligent-routing-on-service-fabric-with-traefik/) utilizzando il [provider di Azure Service Fabric](https://docs.traefik.io/configuration/backends/servicefabric/)
- [Gateway applicazione di Azure](https://docs.microsoft.com/azure/application-gateway/) Nota: questo non è direttamente integrato in Service Fabric e gestione API di Azure è in genere la scelta migliore
- Creane una personalizzata [ASP.NET Core](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-aspnetcore) gateway applicazione web

### <a name="choose-stateless-services"></a>È possibile scegliere servizi senza stati
È consigliabile sempre iniziare dai servizi senza stato compilazione utilizzando [Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction) memorizzazione dello stato in un Database di Azure, Azure COSMOS DB o archiviazione di Azure. Con stato esternalizzato è l'approccio più familiare per la maggior parte degli sviluppatori e consente di sfruttare i vantaggi delle funzionalità di query sull'archivio.  

### <a name="when-to-choose-stateful-services"></a>Quando scegliere i servizi con stato
Quando si dispone di uno scenario per una bassa latenza ed è necessario mantenere i dati più vicino di calcolo, prendere in considerazione i servizi con stato. Alcuni esempi includono i dispositivi gemelli digitale IoT, lo stato del gioco, lo stato della sessione, la memorizzazione nella cache i dati da un database e a esecuzione prolungata tenere traccia di chiamate ad altri servizi dei flussi di lavoro.

Decidere l'intervallo di tempo di conservazione dei dati:

- Dati memorizzati nella cache - è possibile utilizzare la memorizzazione nella cache in cui la latenza in archivi esterni un problema. Usare un servizio con stato, come i tuoi dati memorizza nella cache o provare a usare il [reso open source SoCreate service-fabric--cache distribuita](https://github.com/SoCreate/service-fabric-distributed-cache). In questo scenario, è possibile perdere tutti i dati nella cache e non è importante.
- Data di scadenza - è necessario mantenere i dati per il calcolo per la latenza per un periodo di tempo, ma tali dati possono permettersi di andranno persi un *emergenza* scenario. Ad esempio, in molte soluzioni IoT i dati devono essere vicino al calcolo, ad esempio il calcolo della temperatura media negli ultimi giorni, tuttavia, se questi dati vengono persi, i dati specifici punti registrate non è importante. Anche in questo scenario è in genere indifferente sui backup di singoli punti dati, solo dei valori di medie calcolati che vengono scritte nell'archiviazione esterna periodicamente.  
- Dati a lungo termine - raccolte Reliable collections possono archiviare i dati in modo permanente. Tuttavia, in questo caso è necessario [prepararsi per il ripristino di emergenza](https://docs.microsoft.com/azure/service-fabric/service-fabric-disaster-recovery) inclusi [configurazione dei criteri di backup periodici](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-configure-periodic-backup) per i cluster. In effetti, configurare che cosa accade se il cluster viene eliminato in uno scenario di emergenza, in cui è necessario creare un nuovo cluster e come distribuire le nuove istanze dell'applicazione e ripristinare dal backup più recente.

Risparmiare sui costi e migliorare la disponibilità:
- È possibile ridurre i costi con i servizi con stato perché non è necessario sostenere costi di accesso e le transazioni dei dati dall'archivio remoto e non è necessario usare un altro servizio, ad esempio Redis di Azure.
- Con servizi con stato principalmente per l'archiviazione e non per il calcolo è dispendiosa e non è consigliata. Prendere in considerazione i servizi con stato come risorse di calcolo con economica archiviazione locale.
- Rimuovendo le dipendenze da altri servizi, è possibile migliorare la disponibilità del servizio. Con lo stato gestito con disponibilità elevata nel cluster si isola da altri tempi di inattività del servizio o problemi di latenza.

## <a name="how-to-properly-work-with-reliable-services"></a>Come per il corretto funzionamento con Reliable Services
Reliable Services consentono di creare facilmente servizi con e senza stati. Lettura di [Introduzione a Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction)
- Rispetta sempre il [token di annullamento](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-lifecycle#stateful-service-primary-swaps) nel `RunAsync()` metodo per i servizi con e senza stati e il `ChangeRole()` metodo per i servizi con stati. In caso contrario, Service Fabric non sa se il servizio può essere chiusa. Ad esempio, non rispetta il token di annullamento può comportare tempi di aggiornamento dell'applicazione molto più tempi.
-   Apertura e chiusura [listener di comunicazione](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication) in modo tempestivo e rispetta il token di annullamento.
-   Non combinare mai il codice di sincronizzazione con il codice asincrono. Ad esempio, non usare `.GetAwaiter().GetResult()` nelle chiamate asincrone; deve essere asincrono *completamente* attraverso lo stack di chiamate.

## <a name="how-to-properly-work-with-reliable-actors"></a>Come per il corretto funzionamento di Reliable Actors
Reliable Actors consente di creare con facilità gli attori con stati, virtuali. Lettura di [Introduzione a Reliable Actors](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction)

- Consigliabile prendere in considerazione pub/sub scambio di messaggi tra gli attori per il ridimensionamento dell'applicazione. Ad esempio, il [reso open source SoCreate pub/sub](https://service-fabric-pub-sub.socreate.it/) oppure [Bus di servizio di Azure](https://docs.microsoft.com/azure/service-bus/).
- Verificare lo stato degli attori [più granulare possibile](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices).
- Gestire le [ciclo di vita dell'attore](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices). Se non intende usarli mai più, eliminare gli attori. Ciò vale soprattutto quando si usa la [VolatileState provider](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#state-persistence-and-replication) come tutti gli stati vengono archiviati in memoria.
- A causa dell'errore loro [concorrenza basato su turni](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction#concurrency) gli attori sono particolarmente utile come oggetti indipendenti. Non creare grafici di chiamate al metodo sincrono, multi-attore (ognuno dei quali probabilmente diventa una chiamata di rete separato) o avere le richieste degli attori circolare; Questi influirà in modo significativo le prestazioni e scalabilità.
- Non combinare codice di sincronizzazione con il codice asincrono; deve essere completamente asincrona, per evitare problemi di prestazioni.
- Non eseguire chiamate esecuzione prolungata in actors, bloccherà altre chiamate all'attore stesso, a causa della concorrenza basata su turni.
- Se la comunicazione con altri servizi che utilizzano [comunicazione remota di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-remoting) e si sta creando un `ServiceProxyFactory`, quindi creare la factory nel [servizio actor](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-using) livello e *non*a livello di attore.


## <a name="application-diagnostics"></a>Diagnostica applicazioni
- Approfonditamente le distribuzioni aggiungendo [la registrazione delle applicazioni](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) nelle chiamate al servizio. È utile in diagnostica di scenari in cui i servizi comunicano tra loro. Ad esempio, quando -> B -> C -> D la chiamata potrebbe non riuscire in qualsiasi punto. Se non è sufficiente la registrazione, è difficile da diagnosticare. Se i servizi eseguono l'accesso eccessivo a causa di volumi di chiamate, almeno assicurarsi di registrare gli errori e avvisi.

## <a name="iot-and-messaging-applications"></a>Applicazioni di messaggistica e IoT
Durante la lettura di messaggi da [IoT Hub di Azure](https://docs.microsoft.com/azure/iot-hub/) oppure [hub eventi di Azure](https://docs.microsoft.com/azure/event-hubs/) usare [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor) che si integra con Service Fabric Reliable Services per mantenere il lo stato di lettura dall'Hub eventi partiziona e inserisce i nuovi messaggi ai servizi tramite il `IEventProcessor::ProcessEventsAsync()` (metodo).


## <a name="design-guidance-on-azure"></a>Linee guida di progettazione in Azure
* Visitare il [centro architetture Azure](https://docs.microsoft.com/azure/architecture/microservices/) per indicazioni di progettazione su [creazione di microservizi in Azure](https://docs.microsoft.com/azure/architecture/microservices/)

* Visita [iniziare a usare Azure per i giochi](https://docs.microsoft.com/gaming/azure/) per indicazioni di progettazione su [usando Service Fabric in servizi per i giochi](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)
