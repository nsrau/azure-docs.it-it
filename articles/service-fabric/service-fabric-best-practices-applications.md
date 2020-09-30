---
title: Procedure consigliate per la progettazione di applicazioni Service Fabric Azure
description: Procedure consigliate e considerazioni di progettazione per lo sviluppo di applicazioni e servizi con Service Fabric di Azure.
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: ddf846e9e3ac6add7cf3f584b702de5accfb22af
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91538499"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Procedure consigliate per la progettazione di applicazioni Service Fabric Azure

Questo articolo fornisce indicazioni sulle procedure consigliate per la creazione di applicazioni e servizi in Azure Service Fabric.
 
## <a name="get-familiar-with-service-fabric"></a>Acquisire familiarità con Service Fabric
* [Per informazioni su Service Fabric](service-fabric-content-roadmap.md) , vedere l'articolo.
* Scopri di più sugli [scenari Service Fabric applicazioni](service-fabric-application-scenarios.md).
* Per informazioni sulle scelte dei modelli di programmazione, vedere [Panoramica del modello di programmazione Service Fabric](service-fabric-choose-framework.md).



## <a name="application-design-guidance"></a>Linee guida per la progettazione di applicazioni
Acquisire familiarità con l' [architettura generale](/azure/architecture/reference-architectures/microservices/service-fabric) delle applicazioni Service Fabric e le relative [considerazioni di progettazione](/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations).

### <a name="choose-an-api-gateway"></a>Scegliere un gateway API
Usare un servizio gateway API che comunica con i servizi back-end che possono quindi essere scalati orizzontalmente. I servizi del gateway API più comuni usati sono:

- [Gestione API di Azure](./service-fabric-api-management-overview.md), [integrato con Service Fabric](./service-fabric-tutorial-deploy-api-management.md).
- [Hub](../iot-hub/index.yml) eventi di Azure o [Hub eventi di Azure](../event-hubs/index.yml), usando [ServiceFabricProcessor](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs.ServiceFabricProcessor) per leggere le partizioni dell'hub eventi.
- [Træfik il proxy inverso](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric), usando il [provider di Service fabric di Azure](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/).
- [Gateway applicazione Azure](../application-gateway/index.yml).

   > [!NOTE] 
   > Il gateway applicazione Azure non è integrato direttamente con Service Fabric. Gestione API di Azure è in genere la scelta migliore.
- Il gateway applicazione Web [ASP.NET Core](./service-fabric-reliable-services-communication-aspnetcore.md) personalizzato.

### <a name="stateless-services"></a>Servizi senza stato
È consigliabile iniziare sempre creando servizi senza stato usando [Reliable Services](./service-fabric-reliable-services-introduction.md) e archiviando lo stato in un database di azure, Azure Cosmos DB o archiviazione di Azure. Lo stato esternalizzato è l'approccio più familiare per la maggior parte degli sviluppatori. Questo approccio consente anche di sfruttare le funzionalità di query nell'archivio.  

### <a name="when-to-use-stateful-services"></a>Quando usare i servizi con stato
Si considerino i servizi con stato quando si dispone di uno scenario di bassa latenza ed è necessario tenere i dati vicini al calcolo. Alcuni scenari di esempio includono dispositivi gemelli digitali, stato del gioco, stato della sessione, memorizzazione nella cache dei dati di un database e flussi di lavoro a esecuzione prolungata per tenere traccia delle chiamate ad altri servizi.

Decidere l'intervallo di tempo di conservazione dei dati:

- **Dati memorizzati nella cache**. Usare la memorizzazione nella cache quando la latenza per gli archivi esterni costituisce un problema. Usare un servizio con stato come cache di dati o prendere in considerazione l'uso di [SoCreate open source Service Fabric cache distribuita](https://github.com/SoCreate/service-fabric-distributed-cache). In questo scenario non è necessario preoccuparsi se si perdono tutti i dati nella cache.
- **Dati con limiti temporali**. In questo scenario, è necessario mantenere i dati vicini per il calcolo per un certo periodo di tempo per la latenza, ma è possibile perdere i dati in caso di *emergenza*. Ad esempio, in molte soluzioni Internet, i dati devono essere vicini al calcolo, ad esempio quando viene calcolata la temperatura media negli ultimi giorni, ma se i dati vengono persi, i punti dati specifici registrati non sono importanti. Inoltre, in questo scenario non si è in genere interessati al backup dei singoli punti dati. È possibile eseguire il backup solo dei valori medi calcolati che vengono scritti periodicamente nell'archiviazione esterna.  
- **Dati a lungo termine**. Reliable Collections può archiviare i dati in modo permanente. In questo caso, tuttavia, è necessario [prepararsi per il ripristino di emergenza](./service-fabric-disaster-recovery.md), inclusa la [configurazione dei criteri di backup periodici](./service-fabric-backuprestoreservice-configure-periodic-backup.md) per i cluster. In pratica, si configura cosa accade se il cluster viene eliminato in un'emergenza, in cui è necessario creare un nuovo cluster e come distribuire le nuove istanze dell'applicazione e recuperare dal backup più recente.

Risparmio sui costi e miglioramento della disponibilità:
- È possibile ridurre i costi usando i servizi con stato perché non si incorrono i costi di accesso ai dati e delle transazioni dall'archivio remoto e perché non è necessario usare un altro servizio, ad esempio cache di Azure per Redis.
- L'uso di servizi con stato principalmente per l'archiviazione e non per il calcolo è costoso e non è consigliabile. Si pensi ai servizi con stato come calcolo con archiviazione locale a basso costo.
- Rimuovendo le dipendenze da altri servizi, è possibile migliorare la disponibilità del servizio. La gestione dello stato con la disponibilità elevata nel cluster isola da altri tempi di inattività del servizio o problemi di latenza.

## <a name="how-to-work-with-reliable-services"></a>Come usare Reliable Services
Service Fabric Reliable Services consente di creare facilmente servizi con e senza stato. Per ulteriori informazioni, vedere [Introduzione a reliable Services](./service-fabric-reliable-services-introduction.md).
- Rispettare sempre il [token di annullamento](./service-fabric-reliable-services-lifecycle.md#stateful-service-primary-swaps) nel `RunAsync()` metodo per i servizi con e senza stato e il `ChangeRole()` metodo per i servizi con stato. In caso contrario, Service Fabric non sa se il servizio può essere chiuso. Se, ad esempio, non si rispetta il token di annullamento, è possibile che si verifichino tempi di aggiornamento dell'applicazione molto più lunghi.
-    Aprire e chiudere i [listener di comunicazione](./service-fabric-reliable-services-communication.md) in modo tempestivo e rispettare i token di annullamento.
-    Non combinare mai codice di sincronizzazione con codice asincrono. Ad esempio, non usare `.GetAwaiter().GetResult()` nelle chiamate asincrone. Usare async fino a questo *punto* nello stack di chiamate.

## <a name="how-to-work-with-reliable-actors"></a>Come usare Reliable Actors
Service Fabric Reliable Actors consente di creare facilmente attori virtuali con stato. Per ulteriori informazioni, vedere [Introduzione a reliable Actors](./service-fabric-reliable-actors-introduction.md).

- Prendere in considerazione l'uso della messaggistica pub/sub tra gli attori per la scalabilità dell'applicazione. Gli strumenti che forniscono questo servizio includono [SoCreate open source Service Fabric pub/sub](https://service-fabric-pub-sub.socreate.it/) e il [bus di servizio di Azure](/azure/service-bus/).
- Rendere lo stato dell'attore il più [granulare possibile](./service-fabric-reliable-actors-state-management.md#best-practices).
- Gestire il [ciclo di vita dell'attore](./service-fabric-reliable-actors-state-management.md#best-practices). Eliminare gli attori se non verranno più usati. L'eliminazione di attori non necessari è particolarmente importante quando si usa il [provider di stato volatile](./service-fabric-reliable-actors-state-management.md#state-persistence-and-replication), perché tutto lo stato viene archiviato in memoria.
- A causa della [concorrenza basata su turni](./service-fabric-reliable-actors-introduction.md#concurrency), gli attori vengono utilizzati in modo ottimale come oggetti indipendenti. Non creare grafici di chiamate a metodi sincroni multiattore (ognuno dei quali più probabilmente diventa una chiamata di rete separata) o creare richieste di attori circolari. Questi influiscono significativamente sulle prestazioni e sulla scalabilità.
- Non combinare codice di sincronizzazione con codice asincrono. Usare Async in modo coerente per evitare problemi di prestazioni.
- Non eseguire chiamate con esecuzione prolungata negli attori. Le chiamate con esecuzione prolungata bloccano altre chiamate allo stesso attore, a causa della concorrenza basata su turni.
- Se si sta comunicando con altri servizi usando [Service Fabric comunicazione remota](./service-fabric-reliable-services-communication-remoting.md) e si sta creando un `ServiceProxyFactory` , creare la Factory a livello di [servizio Actor](./service-fabric-reliable-actors-using.md) e *non* a livello di attore.


## <a name="application-diagnostics"></a>Diagnostica applicazioni
Approfondire l'aggiunta della [registrazione dell'applicazione](./service-fabric-diagnostics-event-generation-app.md) nelle chiamate al servizio. Consente di diagnosticare gli scenari in cui i servizi vengono chiamati reciprocamente. Ad esempio, quando un chiama B chiama C chiama D, la chiamata potrebbe non riuscire in qualsiasi posizione. Se la registrazione non è sufficiente, è difficile diagnosticare gli errori. Se i servizi eseguono troppe registrazioni a causa di volumi di chiamate, assicurarsi di registrare almeno gli errori e gli avvisi.

## <a name="iot-and-messaging-applications"></a>Applicazioni e messaggistica
Quando si leggono i messaggi dall' [Hub Azure](../iot-hub/index.yml) o hub [eventi](../event-hubs/index.yml)di Azure, usare  [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/ServiceFabricProcessor). ServiceFabricProcessor si integra con Service Fabric Reliable Services per mantenere lo stato di lettura dalle partizioni dell'hub eventi e inserisce i nuovi messaggi nei servizi tramite il `IEventProcessor::ProcessEventsAsync()` metodo.


## <a name="design-guidance-on-azure"></a>Linee guida per la progettazione in Azure
* Visitare il [centro architetture di Azure](/azure/architecture/microservices/) per indicazioni sulla progettazione per la [creazione di microservizi in Azure](/azure/architecture/microservices/).

* Per informazioni sull' [uso di Service Fabric nei servizi di gioco](/gaming/azure/reference-architectures/multiplayer-synchronous-sf), vedere [Introduzione ad Azure per i giochi](/gaming/azure/) .
