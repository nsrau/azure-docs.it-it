<properties
   pageTitle="Uso avanzato del modello di programmazione Reliable Services | Microsoft Azure"
   description="Informazioni sull'uso avanzato del modello di programmazione Reliable Services di Service Fabric per una maggiore flessibilità nei servizi."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor="masnider"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/28/2016"
   ms.author="jesseb"/>

# Uso avanzato del modello di programmazione Reliable Services
Service Fabric di Azure semplifica la scrittura e la gestione di servizi affidabili con e senza stato. In questa guida verrà illustrato l'uso avanzato del modello di programmazione Reliable Services per ottenere più controllo e flessibilità sui servizi. Prima di leggere questa guida, acquisire familiarità con il [modello di programmazione Reliable Services](service-fabric-reliable-services-introduction.md).

## Classi base per i servizi senza stato
La classe base StatelessService include CreateServiceInstanceListeners() e RunAsync() e dovrebbe essere sufficiente per la maggior parte dei servizi senza stato. La classe StatelessServiceBase sottostante a StatelessService espone gli eventi aggiuntivi del ciclo di vita del servizio. È possibile derivare da StatelessServiceBase se è necessario ulteriore controllo o flessibilità. Per altre informazioni, vedere la documentazione di riferimento per gli sviluppatori relativa a [StatelessService](https://msdn.microsoft.com/library/microsoft.servicefabric.services.runtime.statelessservice.aspx) e [StatelessServiceBase](https://msdn.microsoft.com/library/microsoft.servicefabric.services.runtime.statelessservicebase.aspx).

- `void OnInitialize(StatelessServiceInitializiationParameters)` OnInitialize è il primo metodo chiamato da Service Fabric. Vengono fornite informazioni relative all'inizializzazione del servizio, ad esempio il nome del servizio, l'ID di partizione, l'ID istanza e le informazioni sul pacchetto di codice. In questa fase non deve essere eseguita alcuna elaborazione complessa. Le operazioni di inizializzazione di lunga durata devono essere eseguite in OnOpenAsync.

- `Task OnOpenAsync(IStatelessServicePartition, CancellationToken)` OnOpenAsync viene chiamato quando l'istanza del servizio senza stato sta per essere usata. In questo momento è possibile avviare attività estese di inizializzazione del servizio.

- `Task OnCloseAsync(CancellationToken)` OnCloseAsync viene chiamato quando l'istanza del servizio senza stato sta per essere arrestata normalmente. Ciò può verificarsi quando il codice del servizio viene aggiornato, l'istanza del servizio viene spostata a causa del bilanciamento del carico o viene rilevato un errore temporaneo. OnCloseAsync può essere usato per chiudere in modo sicuro tutte le risorse, arrestare qualsiasi elaborazione in background, completare il salvataggio dello stato esterno o chiudere le connessioni esistenti.

- `void OnAbort()` OnAbort viene chiamato quando l'istanza del servizio senza stato viene arrestata in modo forzato. Questo metodo in genere viene chiamato quando viene rilevato un errore permanente sul nodo o quando Service Fabric non è in grado di gestire in modo affidabile il ciclo di vita dell'istanza del servizio a causa di errori interni.

## Classi base per i servizi con stato
La classe base StatefulService dovrebbe essere sufficiente per la maggior parte dei servizi con stato. Analogamente ai servizi senza stato, la classe StatefulServiceBase sottostante a StatefulService espone gli eventi aggiuntivi del ciclo di vita del servizio. Consente anche di fornire un provider di stato personalizzato e affidabile e, facoltativamente, di supportare listener di comunicazione nelle repliche secondarie. È possibile derivare da StatefulServiceBase se è necessario ulteriore controllo o flessibilità. Per altre informazioni, vedere la documentazione di riferimento per gli sviluppatori relativa a [StatefulService](https://msdn.microsoft.com/library/microsoft.servicefabric.services.runtime.statefulservice.aspx) e [StatefulServiceBase](https://msdn.microsoft.com/library/microsoft.servicefabric.services.runtime.statefulservicebase.aspx).

- `Task OnChangeRoleAsync(ReplicaRole, CancellationToken)` OnChangeRoleAsync viene chiamato quando il servizio con stato modifica i ruoli, ad esempio su primario o secondario. Alle repliche primarie viene assegnato lo stato di scrittura (sono autorizzate a creare le raccolte Reliable Collections e a scriverci). Alle repliche secondarie viene assegnato lo stato di lettura (possono solo leggere dalle raccolte Reliable Collections esistenti). È possibile avviare o aggiornare attività in background in risposta alle modifiche dei ruoli, come l'esecuzione di convalida di sola lettura, la generazione di report o il data mining in una replica secondaria.

- `IStateProviderReplica CreateStateProviderReplica()` Un servizio con stato deve disporre di un provider di stato affidabile. StatefulService usa la classe ReliableStateManager, che include le raccolte Reliable Collections (ad esempio, dizionari e code). È possibile eseguire l'override di questo metodo per configurare la classe ReliableStateManager passando un ReliableStateManagerConfiguration al relativo costruttore. È quindi possibile fornire serializzatori di stato personalizzati, specificare che cosa accade quando i dati vanno persi e configurare i provider di replica/stato.

StatefulServiceBase garantisce anche gli stessi quattro eventi del ciclo di vita di StatelessServiceBase, con la stessa semantica e gli stessi casi d'uso:

- `void OnInitialize(StatefulServiceInitializiationParameters)`
- `Task OnOpenAsync(IStatefulServicePartition, CancellationToken)`
- `Task OnCloseAsync(CancellationToken)`
- `void OnAbort()`

## Passaggi successivi
Per argomenti più avanzati relativi a Service Fabric, vedere gli articoli seguenti:

- [Configurazione di Reliable Services con stato](service-fabric-reliable-services-configuration.md)

- [Introduzione all'integrità di Service Fabric](service-fabric-health-introduction.md)

- [Utilizzo dei report di integrità del sistema per la risoluzione dei problemi](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

- [Panoramica dei vincoli di posizionamento](service-fabric-placement-constraint.md)

<!---HONumber=AcomDC_0204_2016-->