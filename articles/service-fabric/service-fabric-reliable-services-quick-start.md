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

# Introduzione a Reliable Services di Service Fabric

Un'applicazione di Azure Service Fabric contiene uno o più servizi che consentono l'esecuzione del codice. Questa guida illustra come creare applicazioni di Service Fabric con e senza stato mediante [Reliable Services](service-fabric-reliable-services-introduction.md).

## Creare un servizio senza stato

Il servizio senza stato è il tipo di servizio di norma presente nelle applicazioni cloud. Viene considerato senza stato perché il servizio stesso non contiene dati che devono essere archiviati in modo affidabile o resi a disponibilità elevata. Se un'istanza di un servizio senza stato si arresta, il relativo stato interno viene perso. In questi tipi di servizio lo stato deve essere reso persistente mediante un archivio esterno, ad esempio tabelle di Azure o un database SQL, in modo da assicurare elevata disponibilità e affidabilità.

Avviare Visual Studio 2015 RC come amministratore e creare un nuovo progetto dell'applicazione di Service Fabric denominato *HelloWorld*:

![Uso della finestra di dialogo New Project per creare una nuova applicazione di Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

Creare quindi un progetto di servizio senza stato denominato *HelloWorldStateless*:

![Nella seconda finestra di dialogo creare un progetto di servizio senza stato](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject2.png)

La soluzione ora contiene due progetti:

 - *HelloWorld*. Si tratta del progetto *applicazione* contenente i *servizi*. Il progetto contiene anche il manifesto dell'applicazione che descrive l'applicazione stessa, oltre ad alcuni script di PowerShell che consentono di distribuirla.
 - *HelloWorldStateless*. Si tratta del progetto di servizio. Il progetto contiene l'implementazione del servizio senza stato.


## Implementare il servizio

Aprire il file **HelloWorldStateless.cs**nel progetto del servizio. In Service Fabric un servizio può eseguire qualsiasi tipo di logica di business. L'API del servizio fornisce due punti di ingresso per il codice:

 - Un metodo del punto di ingresso aperto denominato *RunAsync*, che consente di iniziare a eseguire qualsiasi carico di lavoro. Questi includono calcoli che richiedono un'esecuzione prolungata.

```C#
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}
```

 - Un punto di ingresso di comunicazione a cui è possibile collegare lo stack di comunicazione desiderato, come l'API Web ASP.NET. In questo punto è possibile iniziare a ricevere richieste da utenti e da altri servizi.

```C#
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}
```

Questa esercitazione si concentra sul metodo del punto di ingresso `RunAsync()`. In questo punto è possibile iniziare immediatamente a eseguire il codice. Il modello di progetto include un esempio di implementazione di `RunAsync()` che gestisce un conteggio incrementale.

> [AZURE.NOTE]Per informazioni dettagliate sull'uso di uno stack di comunicazione, vedere [Introduzione ai servizi API Web di Microsoft Azure Service Fabric con self-hosting OWIN](service-fabric-reliable-services-communication-webapi.md)


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

La piattaforma chiama questo metodo quando si inserisce un'istanza di un servizio pronta per l'esecuzione. Per un servizio senza stato, la piattaforma chiama il metodo quando l'istanza del servizio viene aperta. Viene fornito un token di annullamento che determina quando è necessario chiudere l'istanza del servizio. In Service Fabric questo ciclo di apertura e chiusura di un'istanza del servizio può verificarsi più volte per tutta la durata del servizio nel suo complesso. Questa situazione può verificarsi per vari motivi, tra cui:

- Il sistema sposta le istanze del servizio per il bilanciamento delle risorse.
- Si verificano errori nel codice.
- Viene aggiornato il sistema o l'applicazione.
- Si verifica un'interruzione nell'hardware sottostante.

Questa orchestrazione viene gestita dal sistema per assicurare l'elevata disponibilità e il corretto bilanciamento del servizio.

`RunAsync()` viene eseguito nella propria attività. Si noti che nel frammento di codice riportato in precedenza si è passati direttamente a un ciclo *while*. Non è necessario pianificare un'attività separata per il carico di lavoro. L'annullamento del carico di lavoro è un'operazione cooperativa coordinata dal token di annullamento fornito. Il sistema attende la fine dell'attività (per esito positivo, annullamento o errore) prima di continuare. È importante rispettare il token di annullamento, completare le operazioni e chiudere `RunAsync()` il più rapidamente possibile quando viene richiesto l'annullamento dal sistema.

In questo esempio di servizio senza stato il conteggio è archiviato in una variabile locale. Tuttavia, poiché si tratta di un servizio senza stato, il valore archiviato esiste soltanto per il ciclo di vita corrente dell'istanza del servizio. Quando si posta o si riavvia il servizio, il valore viene perso.

## Creare un servizio con stato

Service Fabric introduce un nuovo tipo di servizio con stato. Un servizio con stato può mantenere lo stato affidabile all'interno del servizio stesso, con percorso condiviso con il codice che lo usa. L'elevata disponibilità dello stato è assicurata da Service Fabric, senza dover rendere persistente lo stato mediante un archivio esterno.

Per convertire un valore del contatore da senza stato a elevata disponibilità e persistenza anche quando il servizio viene spostato o riavviato, è necessario un servizio con stato.

Nella stessa applicazione *HelloWorld* aggiungere un nuovo servizio facendo clic con il pulsante destro del mouse sul progetto applicazione e selezionare **Add Fabric Service**.

![Aggiungere un servizio all'applicazione di Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

Selezionare **Stateful Service** e assegnare il nome *HelloWorldStateful*. Fare clic su **OK**.

![Uso della finestra di dialogo New Project per creare un nuovo servizio di Service Fabric con stato](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

A questo punto l'applicazione dispone di due servizi: il servizio senza stato *HelloWorld* e il servizio con stato *HelloWorldStateful*.

Aprire **HelloWorldStateful.cs** in *HelloWorldStateful* che contiene il metodo RunAsync seguente:

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

        // Pause for one second before continuing processing.
        await Task.Delay(TimeSpan.FromSeconds(1), cancelServicePartitionReplica);
    }
}
```

### RunAsync

Un servizio con stato ha gli stessi punti di ingresso di un servizio senza stato. La differenza principale è la disponibilità di raccolte Reliable Collections e la gestione dello stato. `RunAsync()` opera in modo analogo nei servizi con stato e senza stato. In un servizio con stato la piattaforma esegue tuttavia operazioni aggiuntive per conto dell'utente prima di eseguire `RunAsync()`. Queste operazioni possono includere la verifica che la gestione dello stato e le raccolte Reliable Collections siano pronte per l'uso.

### Raccolte Reliable Collections e gestione dello stato

```C#
var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
```

*IReliableDictionary* è l'implementazione di un dizionario che consente di archiviare in modo affidabile lo stato del servizio. Fa parte delle raccolte [Reliable Collections](service-fabric-reliable-services-reliable-collections.md) integrate in Service Fabric. Grazie a Service Fabric e alle raccolte Reliable Collections è possibile archiviare i dati direttamente nel servizio, senza la necessità di un archivio esterno persistente. Questo approccio rende i dati a disponibilità elevata. A tale scopo Service Fabric crea e gestisce automaticamente più *repliche* del servizio. Offre anche un'API che consente di evitare le complessità di gestione di tali repliche e delle relative transizioni di stato.

Le raccolte Reliable Collections possono archiviare qualsiasi tipo .NET, inclusi quelli personalizzati, con alcuni avvertimenti:

 - Service Fabric rende lo stato altamente disponibile *replicandolo* sui diversi nodi e archiviandolo sul disco locale. Questo significa che tutte le informazioni archiviate nelle raccolte Reliable Collections devono essere *serializzabili*. Per impostazione predefinita, le raccolte Reliable Collections usano [DataContract](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractattribute%28v=vs.110%29.aspx) per la serializzazione. Quando si usa il serializzatore predefinito, è quindi importante assicurarsi che i tipi siano [supportati da Data Contract Serializer](https://msdn.microsoft.com/library/ms731923%28v=vs.110%29.aspx).

 - Quando si esegue il commit di transazioni nelle raccolte Reliable Collections, gli oggetti vengono replicati per assicurare disponibilità elevata. Gli oggetti archiviati nelle raccolte Reliable Collections vengono conservati nella memoria locale del servizio. Ciò significa che è presente un riferimento locale all'oggetto.

    È importante non apportare modifiche alle istanze locali degli oggetti senza prima eseguire un'operazione di aggiornamento sulla raccolta Reliable Collections in una transazione. Le modifiche apportate, infatti, non vengono replicate automaticamente.

La funzionalità di gestione dello stato gestisce automaticamente le raccolte Reliable Collections. In qualunque momento e in qualsiasi posizione del servizio è possibile chiedere semplicemente alla funzionalità di gestione dello stato una raccolta di Reliable Collections indicandone il nome. La funzionalità di gestione dello stato provvederà a restituire un riferimento. Non si consiglia di salvare riferimenti alle istanze di raccolte Reliable Collections in proprietà o variabili membri di classe. Prestare particolare attenzione per assicurarsi che il riferimento sia sempre impostato su un'istanza durante il ciclo di vita del servizio. La funzionalità di gestione dello stato, ottimizzata per le visite ripetute, gestisce queste operazioni per conto dell'utente.

### Operazioni transazionali e asincrone

```C#
using (ITransaction tx = this.StateManager.CreateTransaction())
{
    var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

    await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

    await tx.CommitAsync();
}
```

Le raccolte Reliable Collections includono molte delle operazioni corrispondenti di `System.Collections.Generic` e `System.Collections.Concurrent`, incluso LINQ. Tuttavia, le operazioni su Reliable Collections sono asincrone. Le operazioni di scrittura con le raccolte Reliable Collections sono infatti *replicate*. Per la disponibilità elevata, queste operazioni vengono inviate ad altre repliche del servizio su nodi diversi.

Le raccolte Reliable Collections supportano anche operazioni *transazionali*, consentendo di mantenere lo stato coerente tra diverse raccolte. È ad esempio possibile rimuovere un elemento di lavoro da una coda affidabile, eseguire un'operazione su tale elemento e salvare il risultato in un dizionario affidabile, il tutto all'interno di una singola transazione. Questa viene considerata un'operazione atomica e garantisce che l'intera operazione abbia esito positivo o esito negativo. Se si verifica un errore dopo aver rimosso l'elemento dalla coda ma prima di aver salvato il risultato, viene eseguito il rollback dell'intera transazione e l'elemento rimane nella coda per l'elaborazione.

## Eseguire l'applicazione

Tornare all'applicazione *HelloWorld*. È ora possibile compilare e distribuire i servizi. Quando si preme **F5**, l'applicazione verrà compilata e distribuita nel cluster locale.

Dopo che viene avviata l'esecuzione dei servizi, è possibile visualizzare gli eventi generati di Event Tracing for Windows (ETW) in una finestra degli **eventi di diagnostica**. Si noti che gli eventi visualizzati nell'applicazione provengono sia dal servizio senza stato sia dal servizio con stato. È possibile sospendere il flusso facendo clic sul pulsante **Pause**. Espandendo un messaggio è possibile esaminarne i dettagli.

>[AZURE.NOTE]Prima di eseguire l'applicazione, assicurarsi di avere un cluster di sviluppo locale in esecuzione. Per informazioni sulla configurazione dell'ambiente locale, vedere la guida introduttiva [Preparare l'ambiente di sviluppo](service-fabric-get-started.md).

![Visualizzare gli eventi di diagnostica in Visual Studio](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)


## Passaggi successivi

[Debug dell'applicazione di Service Fabric in Visual Studio](service-fabric-debugging-your-application.md)

[Introduzione ai servizi API Web di Service Fabric con self-hosting OWIN](service-fabric-reliable-services-communication-webapi.md)

[Reliable Collections](service-fabric-reliable-services-reliable-collections.md)

[Distribuire un'applicazione](service-fabric-deploy-remove-applications.md)

[Aggiornamento dell'applicazione](service-fabric-application-upgrade.md)

[Guida di riferimento per gli sviluppatori per Reliable Services](https://msdn.microsoft.com/library/azure/dn706529.aspx)

<!---HONumber=AcomDC_0121_2016-->