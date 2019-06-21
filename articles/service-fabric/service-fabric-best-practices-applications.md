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
ms.date: 06/18/2019
ms.author: msfussell
ms.openlocfilehash: 30d696337061ade6b79c7ec0e4c4de67651f0dad
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203448"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Azure Service Fabric application design le procedure consigliate

Questo articolo vengono fornite procedure consigliate per la compilazione di applicazioni e servizi in Azure Service Fabric.
 
## <a name="get-familiar-with-service-fabric"></a>Acquisire familiarità con Service Fabric
* Leggere il [in modo che si vuole informazioni su Service Fabric?](service-fabric-content-roadmap.md) articolo.
* Conoscenza [scenari di applicazione di Service Fabric](service-fabric-application-scenarios.md).
* Comprendere le scelte di modello di programmazione, leggere [Cenni preliminari sul modello di programmazione di Service Fabric](service-fabric-choose-framework.md).



## <a name="application-design-guidance"></a>Indicazioni per la progettazione dell'applicazione
Acquisire familiarità con le [architettura generale](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) applicazioni di Service Fabric e i relativi [considerazioni di progettazione](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations).

### <a name="choose-an-api-gateway"></a>Scegliere un gateway API
Usare un servizio del gateway API che comunica ai servizi back-end che possono quindi essere aumentati. I servizi di gateway API più comuni usati sono:

- [Gestione API di Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-api-management-overview), ovvero [integrato con Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-api-management).
- [IoT Hub di Azure](https://docs.microsoft.com/azure/iot-hub/) oppure [hub eventi di Azure](https://docs.microsoft.com/azure/event-hubs/), utilizzando la [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor) per leggere da partizioni di Hub eventi.
- [Proxy inverso Træfik](https://blogs.msdn.microsoft.com/azureservicefabric/2018/04/05/intelligent-routing-on-service-fabric-with-traefik/), usando il [provider di Azure Service Fabric](https://docs.traefik.io/configuration/backends/servicefabric/).
- [Gateway applicazione di Azure](https://docs.microsoft.com/azure/application-gateway/).

   > [!NOTE] 
   > Gateway applicazione di Azure non è direttamente integrato in Service Fabric. Gestione API di Azure è in genere la scelta più indicata.
- Il proprio personalizzate [ASP.NET Core](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-aspnetcore) gateway applicazione con web.

### <a name="stateless-services"></a>Servizi senza stato
È consigliabile sempre iniziare compilando i servizi senza stato tramite [Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction) e l'archiviazione dello stato in un database di Azure, Azure Cosmos DB o archiviazione di Azure. Stato esternalizzato è l'approccio più familiare per la maggior parte degli sviluppatori. Questo approccio consente inoltre di sfruttare i vantaggi delle funzionalità di query sull'archivio.  

### <a name="when-to-use-stateful-services"></a>Quando usare i servizi con stato
Quando si dispone di uno scenario per una bassa latenza ed è necessario mantenere i dati più vicino di calcolo, prendere in considerazione i servizi con stato. Alcuni scenari di esempio includono i dispositivi gemelli digitale IoT, lo stato del gioco, lo stato della sessione, la memorizzazione nella cache i dati da un database e i flussi di lavoro a esecuzione prolungata per tenere traccia di chiamate ad altri servizi.

Scegliere l'intervallo di tempo di conservazione dei dati:

- **Dati memorizzati nella cache**. Usare la memorizzazione nella cache quando la latenza in archivi esterni è un problema. Usare un servizio con stato come la cache dei dati oppure provare a usare il [open source SoCreate Service Fabric distribuito Cache](https://github.com/SoCreate/service-fabric-distributed-cache). In questo scenario, non è necessario prestare attenzione in caso di perdita di tutti i dati nella cache.
- **Data di scadenza**. In questo scenario, è necessario mantenere i dati per il calcolo per un periodo di tempo per la latenza, ma è possibile permettersi di perdere i dati in un *emergenza*. Ad esempio, in molte soluzioni IoT, i dati devono essere vicino al calcolo, ad esempio quando viene calcolata la media delle temperature negli ultimi giorni, ma se questi dati vengono persi, i punti dati specifici registrati non sono importanti. Inoltre, in questo scenario non è in genere importante sul backup di singoli punti dati. Solo il backup di valori calcolati di Media che periodicamente vengono scritte nell'archiviazione esterna.  
- **Dati a lungo termine**. Le raccolte Reliable collections possono archiviare in modo permanente i dati. Ma in questo caso è necessario [prepararsi per il ripristino di emergenza](https://docs.microsoft.com/azure/service-fabric/service-fabric-disaster-recovery), tra cui [configurare i criteri di backup periodici](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-configure-periodic-backup) per i cluster. In effetti, configurare che cosa accade se il cluster viene eliminato definitivamente in caso di emergenza, in cui è necessario creare un nuovo cluster e come distribuire le nuove istanze dell'applicazione e ripristinare dal backup più recente.

Risparmiare sui costi e migliorare la disponibilità:
- È possibile ridurre i costi di transazioni e i costi utilizzando i servizi con stato perché non si incorre l'accesso ai dati dall'archivio remoto e perché non è necessario utilizzare un altro servizio, come Cache di Azure per Redis.
- Con servizi con stato principalmente per l'archiviazione e non per il calcolo è dispendiosa e non è consigliabile. I servizi con stato può essere paragonato a risorse di calcolo con economica archiviazione locale.
- Rimuovendo le dipendenze da altri servizi, è possibile migliorare la disponibilità del servizio. La gestione dello stato con disponibilità elevata nel cluster si isola da altri tempi di inattività del servizio o problemi di latenza.

## <a name="how-to-work-with-reliable-services"></a>Come usare i servizi Reliable Services
Reliable Services di Service Fabric consente di creare facilmente servizi con e senza stati. Per altre informazioni, vedere la [Introduzione a Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction).
- Rispetta sempre il [token di annullamento](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-lifecycle#stateful-service-primary-swaps) nel `RunAsync()` metodo per i servizi con e senza stati e il `ChangeRole()` metodo per i servizi con stati. In caso contrario, Service Fabric non sa se il servizio può essere chiusa. Ad esempio, se si non rispetta il token di annullamento, possono verificarsi molto più lunghi tempi di aggiornamento dell'applicazione.
-   Apertura e chiusura [listener di comunicazione](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication) in modo tempestivo e rispetta il token di annullamento.
-   Non combinare mai il codice di sincronizzazione con il codice asincrono. Ad esempio, non usare `.GetAwaiter().GetResult()` nelle chiamate asincrone. Usare l'approccio asincrono *completamente* attraverso lo stack di chiamate.

## <a name="how-to-work-with-reliable-actors"></a>Come usare Reliable Actors
Service Fabric Reliable Actors consente di creare con facilità gli attori con stati, virtuali. Per altre informazioni, vedere la [Introduzione a Reliable Actors](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction).

- Considerare seriamente l'utilizzo di messaggistica tra gli attori per il ridimensionamento dell'applicazione di pubblicazione/sottoscrizione. Gli strumenti che offrono questo servizio includono il [open source SoCreate Service Fabric Pub/Sub](https://service-fabric-pub-sub.socreate.it/) e [Bus di servizio di Azure](https://docs.microsoft.com/azure/service-bus/).
- Verificare lo stato degli attori [più granulare possibile](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices).
- Gestire le [ciclo di vita dell'attore](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices). Se non intende usarli di nuovo, eliminare gli attori. L'eliminazione di attori non necessari è particolarmente importante quando si usa la [provider di stato volatile](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#state-persistence-and-replication), perché tutti gli stati vengono archiviati in memoria.
- Causa della loro [concorrenza basata su turni](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction#concurrency), gli attori sono particolarmente utile come oggetti indipendenti. Non creare grafici delle chiamate al metodo sincrono, multi-attore (ognuno dei quali probabilmente diventa una chiamata di rete separato) o le richieste degli attori circolare. Questi influirà in modo significativo le prestazioni e scalabilità.
- Non combinare codice di sincronizzazione con il codice asincrono. Usare l'approccio asincrono in modo coerente per evitare problemi di prestazioni.
- Non eseguire chiamate a esecuzione prolungata in actors. Le chiamate a esecuzione prolungata blocca altre chiamate all'attore stesso, a causa della concorrenza basata su turni.
- Se sta comunicando con altri servizi usando [comunicazione remota di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-remoting) e si sta creando un `ServiceProxyFactory`, creare la factory nel [servizio actor](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-using) livello e *non* a livello di attore.


## <a name="application-diagnostics"></a>Diagnostica applicazioni
Accurati sull'aggiunta [la registrazione delle applicazioni](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) nelle chiamate al servizio. Aiuterà a diagnosticare gli scenari in cui i servizi comunicano tra loro. Ad esempio, quando un chiamate a chiama B, C chiama D, la chiamata potrebbe non riuscire in un punto qualsiasi. Se non si dispone di sufficiente la registrazione, gli errori sono difficili da diagnosticare. Se i servizi eseguono l'accesso eccessivo a causa di volumi di chiamate, assicurarsi di registrare almeno gli errori e avvisi.

## <a name="iot-and-messaging-applications"></a>Applicazioni di messaggistica e IoT
Quando si leggono i messaggi dal [IoT Hub di Azure](https://docs.microsoft.com/azure/iot-hub/) oppure [hub eventi di Azure](https://docs.microsoft.com/azure/event-hubs/), usare [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor). ServiceFabricProcessor si integra con Service Fabric Reliable Services per mantenere lo stato di lettura dall'hub eventi partizioni e inserisce i nuovi messaggi ai servizi tramite il `IEventProcessor::ProcessEventsAsync()` (metodo).


## <a name="design-guidance-on-azure"></a>Linee guida di progettazione in Azure
* Visitare il [centro architetture Azure](https://docs.microsoft.com/azure/architecture/microservices/) per indicazioni di progettazione sulla [creazione di microservizi in Azure](https://docs.microsoft.com/azure/architecture/microservices/).

* Visita [iniziare a usare Azure per i giochi](https://docs.microsoft.com/gaming/azure/) per indicazioni di progettazione sulla [usando Service Fabric in servizi per i giochi](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf).
