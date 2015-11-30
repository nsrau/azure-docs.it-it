<properties
   pageTitle="Guida introduttiva a Reliable Services | Microsoft Azure"
   description="Introduzione alla creazione di un’applicazione dell’infrastruttura di servizi di Microsoft Azure con i servizi con e senza stato."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="jessebenson"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/15/2015"
   ms.author="vturecek"/>

# Guida introduttiva a Reliable Services dell’infrastruttura di servizi di Microsoft Azure

Un'applicazione dell’infrastruttura di servizi contiene uno o più servizi che consentono l'esecuzione del codice. In questa guida viene illustrato come creare applicazioni dell’infrastruttura di servizio con e senza stato mediante [Reliable Services](service-fabric-reliable-services-introduction.md).

## Creare un servizio senza stato

Il servizio senza stato è il tipo di servizio attualmente più presente nelle applicazioni cloud. Un servizio viene considerato senza stato se non contiene dati che devono essere archiviati in modo affidabile o con disponibilità elevata. In altri termini, in caso di arresto di un'istanza di un servizio senza stato l'intero stato interno viene perso. In questi tipi di servizi lo stato deve essere reso persistente mediante un archivio esterno, ad esempio tabelle di Azure o un database SQL, in modo da assicurare elevata disponibilità e affidabilità.

Avviare Visual Studio 2015 RC come **Amministratore** e creare un nuovo progetto **applicazione dell’infrastruttura di servizi** denominato *HelloWorld*:

![Usare la finestra di dialogo New Project per creare una nuova applicazione dell’infrastruttura di servizi](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

Creare quindi un progetto **servizio senza stato** denominato *HelloWorldStateless*:

![Nella seconda finestra di dialogo, creare un servizio senza stato](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject2.png)

La soluzione ora contiene 2 progetti:

 + **HelloWorld**: si tratta del progetto *applicazione* contenente i *servizi*. Il progetto contiene anche il manifesto dell'applicazione che descrive l'applicazione stessa, oltre ad alcuni script di PowerShell che consentono di distribuirla.
 + **HelloWorldStateless** si tratta del progetto del servizio, contenente l'implementazione del servizio senza stato.


## Implementare il servizio

Aprire il file **HelloWorldStateless.cs**nel progetto del servizio. In Service Fabric un servizio può eseguire qualsiasi tipo di logica di business. L'API del servizio fornisce due punti di ingresso per il codice:

 - Un metodo del punto di ingresso aperto denominato *RunAsync*, che consente di iniziare a eseguire qualsiasi carico di lavoro, ad esempio calcoli che richiedono un'esecuzione prolungata.

```C#
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}
```

 - Un punto di ingresso di comunicazione in cui è possibile collegare lo stack di comunicazione scelto, ad esempio API Web, e che consente di iniziare a ricevere richieste dagli utenti o da altri servizi.

```C#
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}
```

In questa esercitazione verrà preso in esame il metodo del punto di ingresso `RunAsync()`, che consente di iniziare immediatamente a eseguire il codice. Il modello di progetto include un esempio di implementazione di `RunAsync()` che gestisce un conteggio incrementale.

> [AZURE.NOTE]Per informazioni dettagliate sull'utilizzo di uno stack di comunicazione, vedere [Introduzione ai servizi delle API di Service Fabric di Microsoft Azure con self-hosting OWIN](service-fabric-reliable-services-communication-webapi.md)


### RunAsync

```C#
protected override async Task RunAsync(CancellationToken cancelServiceInstance)
{
    // TODO: Replace the following sample code with your own logic.

    int iterations = 0;
    // This service instance continues processing until the instance is terminated.
    while (!cancelServiceInstance.IsCancellationRequested)
    {

        // Log what the service is doing
        ServiceEventSource.Current.ServiceMessage(this, "Working-{0}", iterations++);

        // Pause for 1 second before continue processing.
        await Task.Delay(TimeSpan.FromSeconds(1), cancelServiceInstance);
    }
}
```

La piattaforma chiama questo metodo quando si inserisce un'istanza del servizio pronta per l'esecuzione. Per i servizi senza stato, la piattaforma chiama il metodo quando l'istanza del servizio viene aperta. Viene fornito un token di annullamento che determina quando è necessario chiudere l'istanza del servizio. Nell'infrastruttura del servizio, questo ciclo di apertura-chiusura di un'istanza del servizio può verificarsi più volte per tutta la durata del servizio nel suo complesso per vari motivi, tra cui:

- Il sistema può spostare le istanze del servizio per il bilanciamento delle risorse.
- Si sono verificati errori nel codice.
- Durante gli aggiornamenti di sistema o dell’applicazione.
- Quando si verifica un'interruzione hardware sottostante.

Questa orchestrazione viene gestita dal sistema con lo scopo di assicurare l'elevata disponibilità e il corretto bilanciamento del servizio.

`RunAsync()` viene eseguito nella propria **attività**. Si noti che nel frammento di codice si entra direttamente in un ciclo **while**. Non è necessario pianificare un'attività separata per il carico di lavoro. L'annullamento del carico di lavoro è un'operazione cooperativa coordinata dal token di annullamento fornito. Il sistema attende la fine dell'attività (per esito positivo, annullamento o errore) prima di continuare. È **importante** rispettare il token di annullamento, completare le operazioni e chiudere `RunAsync()` più rapidamente possibile quando viene richiesto l'annullamento dal sistema.

In questo esempio di servizio senza stato il conteggio è archiviato in una variabile locale. Tuttavia, poiché si tratta di un servizio senza stato, il valore archiviato esiste soltanto per il ciclo di vita corrente dell'istanza del servizio. Quando si posta o si riavvia il servizio, il valore viene perso.

## Creare un servizio con stato

L’infrastruttura di servizi introduce un nuovo tipo di servizio con stato. Si tratta di un servizio che mantiene lo stato affidabile all'interno del servizio stesso, nel percorso condiviso con il codice che lo utilizza. L'elevata disponibilità dello stato è assicurata da Service Fabric, senza necessità di rendere persistente lo stato mediante un archivio esterno.

Per convertire il valore del contatore da senza stato a disponibilità elevata e persistenza anche quando il servizio viene spostato o riavviato, è necessario un servizio con stato.

Nella stessa applicazione **HelloWorld** aggiungere un nuovo servizio facendo clic con il pulsante destro del mouse sul progetto applicazione e selezionare **New Fabric Service**.

![Aggiungere un servizio all'applicazione di Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

Selezionare **Service Fabric Stateful Service** e assegnare il nome "HelloWorldStateful". Fare clic su **Aggiungi**.

![Usare la finestra di dialogo New Project per creare un nuovo servizio di infrastruttura di servizi con stato](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

A questo punto l'applicazione dispone di due servizi: il servizio senza stato *HelloWorld* e il servizio con stato *HelloWorldStateful*.

Aprire **HelloWorldStateful.cs** in *HelloWorldStateful* che contiene il metodo `RunAsync` seguente:

```C#
protected override async Task RunAsync(CancellationToken cancelServicePartitionReplica)
{
    // TODO: Replace the following sample code with your own logic.

    // Gets (or creates) a replicated dictionary called "myDictionary" in this partition.
    var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

    // This partition's replica continues processing until the replica is terminated.
    while (!cancelServicePartitionReplica.IsCancellationRequested)
    {

        // Create a transaction to perform operations on data within this partition's replica.
        using (var tx = this.StateManager.CreateTransaction())
        {

            // Try to read a value from the dictionary whose key is "Counter-1".
            var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

            // Log whether the value existed or not.
            ServiceEventSource.Current.ServiceMessage(this, "Current Counter Value: {0}",
                result.HasValue ? result.Value.ToString() : "Value does not exist.");

            // If the "Counter-1" key doesn't exist, set its value to 0
            // else add 1 to its current value.
            await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

            // Committing the transaction serializes the changes and writes them to this partition's secondary replicas.
            // If an exception is thrown before calling CommitAsync, the transaction aborts, all changes are 
            // discarded, and nothing is sent to this partition's secondary replicas.
            await tx.CommitAsync();
        }

        // Pause for 1 second before continue processing.
        await Task.Delay(TimeSpan.FromSeconds(1), cancelServicePartitionReplica);
    }
}
```

### RunAsync

Un servizio con stato ha gli stessi punti di ingresso di un servizio senza stato. La differenza principale consiste nella disponibilità di raccolte *Reliable Collections* e di *State Manager*. In un servizio con stato `RunAsync()` opera in modo simile a un servizio senza stato, ad eccezione del fatto che in un servizio con stato la piattaforma esegue operazioni aggiuntive per conto dell'utente prima di eseguire `RunAsync()`. Ad esempio, assicura che sia possibile utilizzare *State Manager* e le raccolte *Reliable Collections*.

### Raccolte Reliable Collections e State Manager

```C#
var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
```

**IReliableDictionary** è l'implementazione di un dizionario che consente di archiviare in modo affidabile lo stato del servizio. Fa parte delle raccolte [Reliable Collections](service-fabric-reliable-services-reliable-collections.md) integrate dell’infrastruttura di servizi. Grazie a Service Fabric e alle raccolte Reliable Collections è ora possibile archiviare i dati direttamente nel servizio, senza necessità di un archivio esterno persistente, rendendo i dati a disponibilità elevata. Service Fabric ottiene questo risultato creando e gestendo più *repliche* del servizio per conto dell'utente. Fornisce inoltre un'API che consente di evitare la complessità di gestione di tali repliche e delle relative transizioni di stato.

Le raccolte Reliable Collections possono archiviare qualsiasi tipo .NET, inclusi quelli personalizzati, con un paio di avvertimenti:

 1. Service Fabric rende lo stato altamente disponibile *replicandolo* sui diversi nodi e archiviandolo sul disco locale. Questo significa che tutte le informazioni archiviate in una raccolta Reliable Collections devono essere *serializzabili*. Per impostazione predefinita, le raccolte Reliable Collections usano [DataContract](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractattribute%28v=vs.110%29.aspx) per la serializzazione. Quando si usa il serializzatore predefinito, è quindi importante assicurarsi che i tipi siano [supportati da Data Contract Serializer](https://msdn.microsoft.com/library/ms731923%28v=vs.110%29.aspx).

 2. Quando si esegue il commit di una transazione in una raccolta Reliable Collections, gli oggetti vengono replicati per assicurare disponibilità elevata. Gli oggetti archiviati nelle raccolte Reliable Collections vengono mantenuti nella memoria locale del servizio. Questo significa che l'utente dispone di un riferimento locale all'oggetto.

    È importante non apportare modifiche alle istanze locali degli oggetti senza prima eseguire un'operazione di aggiornamento sulla raccolta Reliable Collections in una transazione. Le modifiche apportate, infatti, non vengono replicate automaticamente.

*StateManager* gestisce le raccolte Reliable Collections per conto dell'utente. In qualunque momento e in qualsiasi posizione del servizio è possibile chiedere semplicemente a StateManager una raccolta di Reliable Collections indicandone il nome: StateManager provvederà a restituire un riferimento. Salvare i riferimenti a istanze di raccolte Reliable Collections in variabili o proprietà membri della classe non è consigliabile. È necessario prestare particolare attenzione per assicurare che il riferimento sia impostato su un'istanza in qualsiasi momento del ciclo di vita del servizio. StateManager, ottimizzato per le visite ripetute, gestisce queste operazioni per conto dell'utente.

### Operazioni transazionali e asincrone

```C#
using (ITransaction tx = this.StateManager.CreateTransaction())
{
    var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

    await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

    await tx.CommitAsync();
}
```

Le raccolte Reliable Collections includono molte delle stesse operazioni di `System.Collections.Generic` e `System.Collections.Concurrent` corrispondenti, incluso LINQ. Tuttavia, le operazioni su Reliable Collections sono asincrone. Ciò accade poiché le operazioni di scrittura su queste raccolte vengono *replicate*. In altri termini, l'operazione viene inviata alle altre repliche del servizio su nodi differenti per assicurare la disponibilità elevata.

Le raccolte Reliable Collections supportano anche operazioni *transazionali*, consentendo di mantenere lo stato coerente tra diverse raccolte. Ad esempio, è possibile rimuovere un elemento di lavoro da un oggetto ReliableQueue, eseguire un'operazione su tale elemento e salvare il risultato in un oggetto ReliableDictionary, il tutto all'interno di una singola transazione. Questo processo viene gestito come operazione atomica, garantendo che quest'ultima avrà per intero esito positivo oppure negativo. Di conseguenza, se si verifica un errore dopo che l'elemento è stato rimosso dalla coda ma prima che sia stato possibile salvare il risultato, l'intera transazione viene sottoposta a roll back e l'elemento rimane nella coda per essere elaborato.

## Eseguire l'applicazione

Tornare all’applicazione *HelloWorld*. È ora possibile compilare e distribuire i servizi. Premere **F5** per compilare e distribuire l'applicazione nel cluster locale.

Una volta che i servizi sono in esecuzione, è possibile visualizzare gli eventi ETW generati nella finestra relativa agli **eventi di diagnostica**. Si noti che nell'applicazione vengono visualizzati eventi provenienti sia dal servizio senza stato sia dal servizio con stato. È possibile sospendere il flusso facendo clic sul pulsante *Pause* e quindi espandere un messaggio per analizzarne i dettagli.

>[AZURE.NOTE]Prima di eseguire l'applicazione, assicurarsi di avere un cluster di sviluppo locale in esecuzione. Per informazioni sulla configurazione dell'ambiente locale, vedere l'articolo relativo alla [configurazione dell'ambiente di sviluppo Service Fabric](service-fabric-get-started.md).

![Visualizzare gli eventi di diagnostica in Visual Studio](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)


## Passaggi successivi

[Debug dell'applicazione di Service Fabric in Visual Studio](service-fabric-debugging-your-application.md)

[Introduzione ai servizi delle API Web di Microsoft Azure Service Fabric con self-hosting OWIN](service-fabric-reliable-services-communication-webapi.md)

[Reliable Collections](service-fabric-reliable-services-reliable-collections.md)

[Gestire un servizio di Service Fabric](service-fabric-manage-your-service-index.md)

[Guida di riferimento per gli sviluppatori per Reliable Services](https://msdn.microsoft.com/library/azure/dn706529.aspx)

<!---HONumber=Nov15_HO4-->