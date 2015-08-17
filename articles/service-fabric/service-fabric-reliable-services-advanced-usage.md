<properties
   pageTitle="Uso avanzato del modello di programmazione Reliable Services di Service Fabric"
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
   ms.date="06/09/2015"
   ms.author="jesseb"/>

# Uso avanzato del modello di programmazione Reliable Services
Service Fabric semplifica la scrittura e la gestione di servizi affidabili con e senza stato. In questa guida verrà illustrato l'uso avanzato del modello di programmazione Reliable Services per ottenere più controllo e flessibilità sui servizi. Prima di leggere questa guida, acquisire familiarità con il [modello di programmazione Reliable Services](service-fabric-reliable-services-introduction.md).

## Classi base dei servizi senza stato
La classe base StatelessService include CreateCommunicationListener() e RunAsync() e dovrebbe essere sufficiente per la maggior parte dei servizi senza stato. La classe StatelessServiceBase sottostante a StatelessService espone gli eventi aggiuntivi del ciclo di vita del servizio. Per altre informazioni, vedere la documentazione di riferimento per gli sviluppatori relativa a [StatelessService](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.services.statelessservice.aspx) e [StatelessServiceBase](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.services.statelessservicebase.aspx).

- `void OnInitialize(StatelessServiceInitializiationParameters)` OnInitialize è il primo metodo chiamato da Service Fabric. Vengono fornite informazioni relative all'inizializzazione del servizio, ad esempio il nome del servizio, l'ID di partizione, l'ID istanza e le informazioni sul pacchetto di codice. In questa fase non deve essere eseguita alcuna elaborazione complessa. Le operazioni di inizializzazione di lunga durata devono essere eseguite in OnOpenAsync.

- `Task OnOpenAsync(IStatelessServicePartition, CancellationToken)` OnOpenAsync viene chiamato quando l'istanza del servizio senza stato sta per essere usata. In questo momento è possibile avviare attività estese di inizializzazione del servizio.

- `Task OnCloseAsync(CancellationToken)` OnCloseAsync viene chiamato quando l'istanza del servizio senza stato sta per essere arrestata normalmente. Ciò può verificarsi quando il codice del servizio viene aggiornato, l'istanza del servizio viene spostata a causa del bilanciamento del carico o viene rilevato un errore temporaneo. OnCloseAsync può essere usato per chiudere in modo sicuro tutte le risorse, arrestare qualsiasi elaborazione in background, completare il salvataggio dello stato esterno o chiudere le connessioni esistenti.

- `void OnAbort()` OnAbort viene chiamato quando l'istanza del servizio senza stato viene arrestata in modo forzato. Questo metodo in genere viene chiamato quando viene rilevato un errore permanente sul nodo o quando Service Fabric non è in grado di gestire in modo affidabile il ciclo di vita dell'istanza del servizio a causa di errori interni.

## Classi base dei servizi con stato
La classe base StatefulService dovrebbe essere sufficiente per la maggior parte dei servizi con stato. Analogamente ai servizi senza stato, la classe StatefulServiceBase sottostante a StatefulService espone gli eventi aggiuntivi del ciclo di vita del servizio. Consente anche di specificare un provider di stato personalizzato e affidabile e, facoltativamente, di supportare listener di comunicazione nelle repliche secondarie. Per altre informazioni, vedere la documentazione di riferimento per gli sviluppatori relativa a [StatefulService](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.services.statefulservice.aspx) e [StatefulServiceBase](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.services.statefulservicebase.aspx).

- `Task OnChangeRoleAsync(ReplicaRole, CancellationToken)` OnChangeRoleAsync viene chiamato quando il servizio con stato modifica i ruoli, ad esempio in repliche primarie o secondarie. Alle repliche primarie viene assegnato lo stato di scrittura (sono autorizzate a creare e scrivere nelle raccolte Reliable Collections), mentre alle repliche secondarie viene assegnato lo stato di lettura (sono autorizzate solo a leggere dalle raccolte Reliable Collections esistenti). È possibile avviare o aggiornare attività in background in risposta alle modifiche dei ruoli, come l'esecuzione di convalida di sola lettura, la generazione di report o il data mining in una replica secondaria.

- `IStateProviderReplica CreateStateProviderReplica()` Un servizio con stato deve disporre di un provider di stato affidabile. StatefulService usa la classe ReliableStateManager, che include le raccolte Reliable Collections (ad esempio, dizionari e code). È possibile fornire un proprio provider se si desidera gestire autonomamente lo stato o estendere le funzionalità di uno dei provider di stato incorporati.

- `bool EnableCommunicationListenerOnSecondary { get; }` Per impostazione predefinita, i listener di comunicazione vengono creati solo su repliche primarie. StatefulService e StatefulServiceBase consentono di eseguire l'override di questa proprietà per permettere la creazione di listener di comunicazione su repliche secondarie. È possibile consentire alle repliche secondarie di gestire le richieste di sola lettura per migliorare la velocità effettiva in carichi di lavoro con intensa attività di lettura.

    > [AZURE.NOTE]È responsabilità dell'utente garantire che le repliche secondarie non tentino di creare o scrivere nelle raccolte Reliable Collections. I tentativi di scrivere su repliche secondarie genereranno un'eccezione che, se non gestita, provocherà la chiusura e la riapertura della replica.

StatefulServiceBase garantisce anche gli stessi quattro eventi del ciclo di vita di StatelessServiceBase, con la stessa semantica e gli stessi casi d'uso:

- `void OnInitialize(StatelessServiceInitializiationParameters)`
- `Task OnOpenAsync(IStatelessServicePartition, CancellationToken)`
- `Task OnCloseAsync(CancellationToken)`
- `void OnAbort()`

## Passaggi successivi
Per argomenti più avanzati relativi a Service Fabric, vedere gli articoli seguenti.

- [Configurazione di Reliable Services con stato](service-fabric-reliable-services-configuration.md)

- [Introduzione all'integrità di Service Fabric](../service-fabric/service-fabric-health-introduction.md)

- [Uso dei report sull'integrità del sistema per la risoluzione dei problemi](../service-fabric/service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

- [Panoramica dei vincoli di posizionamento](../service-fabric/service-fabric-placement-constraint.md)

- [Proteggere il traffico di replica dei servizi con stato in Azure Service Fabric](../service-fabric/service-fabric-replication-security.md)
 

<!---HONumber=August15_HO6-->