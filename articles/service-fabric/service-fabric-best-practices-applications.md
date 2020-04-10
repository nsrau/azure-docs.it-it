---
title: Procedure consigliate per la progettazione di applicazioni di Azure Service FabricAzure Service Fabric application design best practices
description: Procedure consigliate e considerazioni di progettazione per lo sviluppo di applicazioni e servizi con Azure Service Fabric.Best practices and design considerations for developing applications and services using Azure Service Fabric.
author: markfussell
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: mfussell
ms.openlocfilehash: 56df6e28940eb15597a3d6bccca3f85e5f690f89
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991655"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Procedure consigliate per la progettazione di applicazioni di Azure Service FabricAzure Service Fabric application design best practices

Questo articolo fornisce indicazioni sulle procedure consigliate per la creazione di applicazioni e servizi in Azure Service Fabric.This article provides best practice guidance for building applications and services on Azure Service Fabric.
 
## <a name="get-familiar-with-service-fabric"></a>Acquisire familiarità con Service Fabric
* Leggi l'articolo [Quindi vuoi saperne di più su Service Fabric?](service-fabric-content-roadmap.md)
* Informazioni sugli [scenari di applicazione di Service Fabric](service-fabric-application-scenarios.md).
* Comprendere le scelte del modello di programmazione leggendo Cenni preliminari sul modello di [programmazione di Service Fabric](service-fabric-choose-framework.md).



## <a name="application-design-guidance"></a>Linee guida per la progettazione delle applicazioni
Acquisire familiarità con [l'architettura generale](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) delle applicazioni Service Fabric e con le relative [considerazioni di progettazione.](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations)

### <a name="choose-an-api-gateway"></a>Scegliere un gateway APIChoose an API gateway
Usare un servizio gateway API che comunica con i servizi back-end che possono quindi essere scalati orizzontalmente. I servizi gateway API più comuni usati sono:The most common API gateway services used are:

- [Gestione API di Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-api-management-overview), [integrata con Service Fabric.](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-api-management)
- [Hub IoT di Azure](https://docs.microsoft.com/azure/iot-hub/) o [Hub eventi](https://docs.microsoft.com/azure/event-hubs/)di Azure , usando [ServiceFabricProcessor](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs.ServiceFabricProcessor) per leggere dalle partizioni dell'hub eventi.
- [Proxy inverso di ](https://blogs.msdn.microsoft.com/azureservicefabric/2018/04/05/intelligent-routing-on-service-fabric-with-traefik/), utilizzando il [provider di Azure Service Fabric](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/).
- [Gateway applicazione di Azure](https://docs.microsoft.com/azure/application-gateway/).

   > [!NOTE] 
   > Azure Application Gateway isn't directly integrated with Service Fabric. Gestione API di Azure è in genere la scelta preferita.
- Il proprio gateway applicazione Web [ASP.NET Core](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-aspnetcore) personalizzato.

### <a name="stateless-services"></a>Servizi apolidi
È consigliabile iniziare sempre creando servizi senza stato usando Reliable Services e archiviando lo stato in un database di Azure, un database Cosmos di Azure o Archiviazione di Azure.We recommended that you always start by building [stateless services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction) by using Reliable Services and storing state in an Azure database, Azure Cosmos DB, or Azure Storage. Lo stato esternamente è l'approccio più familiare per la maggior parte degli sviluppatori. Questo approccio consente inoltre di sfruttare le funzionalità di query nell'archivio.  

### <a name="when-to-use-stateful-services"></a>Quando usare i servizi con statoWhen to use stateful services
Considerare i servizi con stato quando si dispone di uno scenario per bassa latenza ed è necessario mantenere i dati vicini al calcolo. Alcuni scenari di esempio includono i dispositivi gemelli digitali IoT, lo stato del gioco, lo stato della sessione, la memorizzazione nella cache dei dati da un database e i flussi di lavoro a esecuzione prolungata per tenere traccia delle chiamate ad altri servizi.

Decidere l'intervallo di tempo di conservazione dei dati:

- **Dati memorizzati nella cache**. Utilizzare la memorizzazione nella cache quando la latenza negli archivi esterni è un problema. Utilizzare un servizio con stato come cache di dati oppure prendere in considerazione l'utilizzo della [cache distribuita SoCreate Service Fabric open source](https://github.com/SoCreate/service-fabric-distributed-cache). In questo scenario, non è necessario preoccuparsi se si perdono tutti i dati nella cache.
- **Dati associati al tempo**. In questo scenario, è necessario mantenere i dati vicini al calcolo per un periodo di tempo per la latenza, ma è possibile permetterti di perdere i dati in caso di *emergenza.* Ad esempio, in molte soluzioni IoT, i dati devono essere vicini al calcolo, ad esempio quando viene calcolata la temperatura media negli ultimi giorni, ma se questi dati vengono persi, i punti dati specifici registrati non sono così importanti. Inoltre, in questo scenario non è in genere necessario eseguire il backup dei singoli punti dati. È sufficiente eseguire il backup dei valori medi calcolati scritti periodicamente nell'archiviazione esterna.  
- **Dati a lungo termine**. Raccolte affidabili possono memorizzare i dati in modo permanente. In questo caso, tuttavia, è necessario prepararsi per il ripristino di [emergenza,](https://docs.microsoft.com/azure/service-fabric/service-fabric-disaster-recovery) [inclusa la configurazione dei criteri](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-configure-periodic-backup) di backup periodici per i cluster. In effetti, è possibile configurare cosa accade se il cluster viene eliminato in caso di emergenza, in cui è necessario creare un nuovo cluster e come distribuire nuove istanze dell'applicazione ed eseguire il ripristino dal backup più recente.

Risparmia sui costi e migliora la disponibilità:
- È possibile ridurre i costi usando i servizi con stato perché non si incorre in costi di accesso ai dati e transazioni dall'archivio remoto e perché non è necessario usare un altro servizio, ad esempio Cache di Azure per Redis.You can reduce costs by using stateful services because you don't be data access and transactions costs from the remote store, and because you don't need to use another service, like Azure Cache for Redis.
- L'uso di servizi con stato principalmente per l'archiviazione e non per il calcolo è costoso e non è consigliabile. Considerare i servizi con stato come calcolo con archiviazione locale a basso costo.
- Rimuovendo le dipendenze da altri servizi, è possibile migliorare la disponibilità del servizio. La gestione dello stato con disponibilità membro nel cluster consente di isolare l'utente da altri tempi di inattività del servizio o problemi di latenza.

## <a name="how-to-work-with-reliable-services"></a>Come utilizzare Reliable Services
Service Fabric Reliable Services consente di creare facilmente servizi senza stato e con stato. Per ulteriori informazioni, vedere [l'introduzione a Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction).
- Rispettare sempre il token `RunAsync()` di [annullamento](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-lifecycle#stateful-service-primary-swaps) nel metodo `ChangeRole()` per i servizi senza stato e con stato e il metodo per i servizi con stato. In caso contrario, Service Fabric non sa se il servizio può essere chiuso. Ad esempio, se non si rispetta il token di annullamento, possono verificarsi tempi di aggiornamento dell'applicazione molto più lunghi.
-    Aprire e chiudere [i listener](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication) di comunicazione in modo tempestivo e rispettare i token di annullamento.
-    Non combinare mai il codice di sincronizzazione con il codice asincrono. Ad esempio, non `.GetAwaiter().GetResult()` usare nelle chiamate asincrone. Utilizzare async *fino* allo stack di chiamate.

## <a name="how-to-work-with-reliable-actors"></a>Come lavorare con Reliable Actors
Service Fabric Reliable Actors consente di creare facilmente attori virtuali con stato. Per ulteriori informazioni, vedere [l'introduzione a Reliable Actors](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction).

- Considerare seriamente l'utilizzo della messaggistica pub/sub tra gli attori per la scalabilità dell'applicazione. Gli strumenti che forniscono questo servizio includono [l'open-source SoCreate Service Fabric Pub/Sub](https://service-fabric-pub-sub.socreate.it/) e il bus di servizio di [Azure.](https://docs.microsoft.com/azure/service-bus/)
- Rendere lo stato dell'attore il [più granulare possibile.](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices)
- Gestire il [ciclo di vita dell'attore.](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices) Eliminare gli attori se non avete intenzione di usarli di nuovo. L'eliminazione di attori non necessari è particolarmente importante quando si utilizza il [provider di stato volatile](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#state-persistence-and-replication), poiché tutto lo stato viene archiviato in memoria.
- A causa della [concorrenza basata su turni,](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction#concurrency)gli attori vengono utilizzati come oggetti indipendenti. Non creare grafici di chiamate al metodo sincrone multi-attore (ognuna delle quali diventa molto probabilmente una chiamata di rete separata) o creare richieste di attori circolari. Questi influiranno in modo significativo sulle prestazioni e sulla scalabilità.
- Non combinare il codice di sincronizzazione con il codice asincrono. Usare async in modo coerente per evitare problemi di prestazioni.
- Non fare chiamate di lunga durata in attori. Le chiamate a esecuzione prolungata bloccheranno altre chiamate allo stesso attore, a causa della concorrenza basata su turni.
- Se si comunica con altri servizi utilizzando la comunicazione remota `ServiceProxyFactory`di Service [Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-remoting) e si crea un oggetto , creare la factory a livello di [attore-servizio](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-using) e *non* a livello di attore.


## <a name="application-diagnostics"></a>Diagnostica applicazioni
Completa l'aggiunta della [registrazione](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) delle applicazioni nelle chiamate di servizio. Ti aiuterà a diagnosticare gli scenari in cui i servizi si chiamano a vicenda. Ad esempio, quando A chiama B chiama C chiama D, la chiamata potrebbe non riuscire ovunque. Se la registrazione non è sufficiente, è difficile diagnosticare gli errori. Se i servizi registrano troppo a causa dei volumi di chiamata, assicurarsi di registrare almeno errori e avvisi.

## <a name="iot-and-messaging-applications"></a>Applicazioni IoT e di messaggistica
Quando si leggono messaggi [dall'hub IoT](https://docs.microsoft.com/azure/iot-hub/) di Azure o dagli [hub eventi](https://docs.microsoft.com/azure/event-hubs/)di Azure , usare [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/ServiceFabricProcessor). ServiceFabricProcessor si integra con Service Fabric Reliable Services per mantenere lo stato di lettura dalle `IEventProcessor::ProcessEventsAsync()` partizioni dell'hub eventi e invia nuovi messaggi ai servizi tramite il metodo .


## <a name="design-guidance-on-azure"></a>Indicazioni sulla progettazione di AzureDesign guidance on Azure
* Visitare il [Centro architettura](https://docs.microsoft.com/azure/architecture/microservices/) di Azure per istruzioni sulla progettazione di [microservizi in Azure.](https://docs.microsoft.com/azure/architecture/microservices/)

* Per istruzioni sulla progettazione dell'uso di Service Fabric nei servizi di gioco, vedere [Introduzione ad Azure for Gaming](https://docs.microsoft.com/gaming/azure/) per le indicazioni sulla progettazione di Service [Fabric.](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)
