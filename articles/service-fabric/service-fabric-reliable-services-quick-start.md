<properties
   pageTitle="Guida introduttiva a Reliable Services di Microsoft Azure Service Fabric"
   description="Service Fabric supporta sia servizi senza stato sia servizi con stato. Questo articolo illustra i passaggi della creazione di un'applicazione di Service Fabric mediante servizi con e senza stato."
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
   ms.date="06/09/2015"
   ms.author="vturecek"/>

# Guida introduttiva a Reliable Services di Microsoft Azure Service Fabric

Un'applicazione di Service Fabric contiene uno o più servizi che consentono l'esecuzione del codice. Questa esercitazione illustra i passaggi della creazione dell'applicazione "Hello World" di Service Fabric sia con stato sia senza stato usando il [modello di programmazione *Reliable Services*](../Service-Fabric/service-fabric-reliable-services-introduction.md).

Il servizio senza stato è il tipo di servizio attualmente più presente nelle applicazioni cloud. Un servizio viene considerato senza stato se non contiene dati che devono essere archiviati in modo affidabile o con disponibilità elevata. In altri termini, in caso di arresto di un'istanza di un servizio senza stato l'intero stato interno viene perso. In questi tipi di servizi lo stato deve essere reso persistente mediante un archivio esterno, ad esempio tabelle di Azure o un database SQL, in modo da assicurare elevata disponibilità e affidabilità.

Service Fabric introduce un nuovo tipo di servizio con stato. Si tratta di un servizio che mantiene lo stato affidabile all'interno del servizio stesso, nel percorso condiviso con il codice che lo utilizza. L'elevata disponibilità dello stato è assicurata da Service Fabric, senza necessità di rendere persistente lo stato mediante un archivio esterno.

In questa esercitazione viene implementato un servizio senza stato e un servizio con stato che mantiene un contatore interno. Nel servizio senza stato, quando si riavvia o si sposta il servizio il valore del contatore viene perso. Nel servizio con stato, invece, lo stato del contatore viene reso affidabile da Service Fabric. In questo modo, se l'esecuzione del servizio viene arrestata per qualsiasi motivo durante il conteggio, quest'ultimo può essere ripreso esattamente dal punto in cui era stato interrotto.

## Creare un servizio senza stato

L'esercitazione inizia con un servizio senza stato.

Avviare Visual Studio 2015 RC come **Amministratore** e creare un nuovo progetto di **servizio di Service Fabric senza stato** denominato *HelloWorld*:

![Usare la finestra di dialogo New Project per creare un nuovo servizio di Service Fabric senza stato](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

Nella soluzione creata vengono visualizzati due progetti:

 + **HelloWorldApplication**: il progetto *Application* contenente i *servizi*. Il progetto contiene anche il manifesto dell'applicazione che descrive l'applicazione stessa, oltre ad alcuni script di PowerShell che consentono di distribuirla.
 + **HelloWorld**: il progetto del servizio, contenente l'implementazione del servizio senza stato.


## Implementare il servizio

Aprire il file **HelloWorld.cs** nel progetto del servizio. In Service Fabric un servizio può eseguire qualsiasi tipo di logica di business. L'API del servizio fornisce due punti di ingresso per il codice:

 - Un metodo del punto di ingresso aperto denominato *RunAsync*, che consente di iniziare a eseguire qualsiasi carico di lavoro, ad esempio calcoli che richiedono un'esecuzione prolungata.

```C#

protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}

```

 - Un punto di ingresso di comunicazione in cui è possibile collegare lo stack di comunicazione scelto, ad esempio API Web, e che consente di iniziare a ricevere richieste dagli utenti o da altri servizi.

```C#

protected override ICommunicationListener CreateCommunicationListener()
{
    ...
}

```

In questa esercitazione verrà preso in esame il metodo del punto di ingresso `RunAsync()`, che consente di iniziare immediatamente a eseguire il codice. Il modello di progetto include un esempio di implementazione di `RunAsync()` che gestisce un conteggio incrementale.

> [AZURE.NOTE]Per informazioni dettagliate sull'utilizzo di uno stack di comunicazione, vedere [Introduzione ai servizi delle API di Service Fabric di Microsoft Azure con self-hosting OWIN](service-fabric-reliable-services-communication-webapi.md)


### RunAsync

```C#

protected override async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following with your own logic.

    int iterations = 0;
    while (!cancellationToken.IsCancellationRequested)
    {
        ServiceEventSource.Current.ServiceMessage(this, "Working-{0}", iterations++);
        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
}

```

La piattaforma chiama questo metodo quando si inserisce un'istanza del servizio pronta per l'esecuzione. Per i servizi senza stato, la piattaforma chiama il metodo quando l'istanza del servizio viene aperta. Viene fornito un token di annullamento che determina quando è necessario chiudere l'istanza del servizio. In Service Fabric questo ciclo di apertura e chiusura dell'istanza di un servizio può essere eseguito diverse volte durante il ciclo di vita dell'intero servizio. Il sistema può infatti spostare le istanze del servizio in diverse posizioni per bilanciare le risorse, quando si verificano degli errori, durante gli aggiornamenti dell'applicazione o del sistema o in caso di guasto dell'hardware sottostante. Questa orchestrazione viene gestita dal sistema con lo scopo di assicurare l'elevata disponibilità e il corretto bilanciamento del servizio.

`RunAsync()` viene eseguito nella propria attività. Si noti che nel frammento di codice si entra direttamente in un ciclo. Non è necessario pianificare un'attività separata per il carico di lavoro. L'annullamento del carico di lavoro è un'operazione cooperativa coordinata dal token di annullamento fornito. Prima di procedere, il sistema attende la fine dell'attività \(esito positivo, annullamento o errore\). Quindi, quando l'annullamento viene richiesto dal sistema, è *importante* rispettare il token di annullamento, terminare qualsiasi operazione e chiudere `RunAsync()` il più rapidamente possibile.

In questo esempio di servizio senza stato il conteggio è archiviato in una variabile locale. Tuttavia, poiché si tratta di un servizio senza stato, il valore archiviato esiste soltanto per il ciclo di vita corrente dell'istanza del servizio. Quando si posta o si riavvia il servizio, il valore viene perso.

## Creare un servizio con stato

Per assicurare la disponibilità elevata e la persistenza del valore del contatore anche quando il servizio viene spostato o riavviato, è necessario un servizio con stato.

Nella stessa applicazione **HelloWorld** aggiungere un nuovo servizio facendo clic con il pulsante destro del mouse sul progetto dell'applicazione e selezionare "New Fabric Service"

![Aggiungere un servizio all'applicazione di Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

Selezionare **Service Fabric Stateful Service** e assegnare il nome "HelloWorldStateful". Fare clic su **Aggiungi**.

![Usare la finestra di dialogo New Project per creare un nuovo servizio di Service Fabric con stato](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

A questo punto l'applicazione dispone di due servizi: il servizio senza stato *HelloWorld* e il servizio con stato *HelloWorldStateful*. Aprire **HelloWorldStateful.cs** in *HelloWorldStateful*:

```C#

protected override async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following with your own logic.
    IReliableDictionary<string, long>  myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

    while (!cancellationToken.IsCancellationRequested)
    {
        using (ITransaction tx = this.StateManager.CreateTransaction())
        {
            var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");
            ServiceEventSource.Current.ServiceMessage(
                this,
                "Current Counter Value: {0}",
                result.HasValue ? result.Value.ToString() : "Value does not exist.");

            await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

            await tx.CommitAsync();
        }

        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
}

```

### RunAsync

Un servizio con stato ha gli stessi punti di ingresso di un servizio senza stato. La differenza principale consiste nella disponibilità di raccolte *Reliable Collections* e di *State Manager*. In un servizio con stato `RunAsync()` opera in modo simile a un servizio senza stato, ad eccezione del fatto che in un servizio con stato la piattaforma esegue operazioni aggiuntive per conto dell'utente prima di eseguire `RunAsync()`. Ad esempio, assicura che sia possibile utilizzare *State Manager* e le raccolte *Reliable Collections*.

### Raccolte Reliable Collections e State Manager

```C#

IReliableDictionary<string, long> myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

```

L'interfaccia **IReliableDictionary** rappresenta un dizionario che consente di archiviare lo stato nel servizio localmente e in modo affidabile ed è parte delle raccolte [Reliable Collections](service-fabric-reliable-services-reliable-collections.md) integrate di Service Fabric. Service Fabric assicura la disponibilità elevata del servizio e dello stato archiviati nelle raccolte Reliable Collections. Grazie a Service Fabric e alle raccolte Reliable Collections è ora possibile archiviare i dati direttamente nel servizio, senza necessità di un archivio esterno persistente. Service Fabric ottiene questo risultato creando e gestendo più *repliche* del servizio per conto dell'utente. Fornisce inoltre un'API che consente di evitare la complessità di gestione di tali repliche e delle relative transizioni di stato.

Le raccolte Reliable Collections possono archiviare qualsiasi tipo .NET, inclusi quelli personalizzati, con un paio di avvertimenti:

 1. Service Fabric rende lo stato altamente disponibile *replicandolo* sui diversi nodi e archiviandolo sul disco locale. Questo significa che tutte le informazioni archiviate in una raccolta Reliable Collections devono essere *serializzabili*. Per impostazione predefinita, le raccolte Reliable Collections usano [DataContract](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractattribute%28v=vs.110%29.aspx) per la serializzazione. Quando si usa il serializzatore predefinito, è quindi importante assicurarsi che i tipi siano [supportati da Data Contract Serializer](https://msdn.microsoft.com/library/ms731923%28v=vs.110%29.aspx).

 2. Quando si esegue il commit di una transazione in una raccolta Reliable Collections, gli oggetti vengono replicati per assicurare disponibilità elevata. Gli oggetti archiviati nelle raccolte Reliable Collections vengono mantenuti nella memoria locale del servizio. Questo significa che l'utente dispone di un riferimento locale all'oggetto. È importante non apportare modifiche alle istanze locali degli oggetti senza prima eseguire un'operazione di aggiornamento sulla raccolta Reliable Collections in una transazione. Le modifiche apportate, infatti, non vengono replicate automaticamente.

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

Le raccolte Reliable Collections includono molte delle stesse operazioni delle controparti System.Collections.Generic e System.Collections.Concurrent, incluso LINQ. Le operazioni delle raccolte Reliable Collections, tuttavia, sono asincrone, poiché le operazioni di scrittura su queste raccolte vengono *replicate*. In altri termini, l'operazione viene inviata alle altre repliche del servizio su nodi differenti per assicurare elevata disponibilità.

Le raccolte Reliable Collections supportano anche operazioni *transazionali*, consentendo di mantenere lo stato coerente tra diverse raccolte. Ad esempio, è possibile rimuovere un elemento di lavoro da un oggetto ReliableQueue, eseguire un'operazione su tale elemento e salvare il risultato in un oggetto ReliableDictionary, il tutto all'interno di una singola transazione. Questo processo viene gestito come operazione atomica, garantendo che quest'ultima avrà per intero esito positivo oppure negativo. Di conseguenza, se si verifica un errore dopo che l'elemento è stato rimosso dalla coda ma prima che sia stato possibile salvare il risultato, l'intera transazione viene sottoposta a roll back e l'elemento rimane nella coda per essere elaborato.

## Eseguire l'applicazione

È ora possibile compilare e distribuire i servizi. Premere **F5** per compilare e distribuire l'applicazione nel cluster locale. Una volta che i servizi sono in esecuzione, è possibile visualizzare gli eventi ETW generati nella finestra relativa agli **eventi di diagnostica**. Si noti che nell'applicazione vengono visualizzati eventi provenienti sia dal servizio senza stato sia dal servizio con stato. È possibile sospendere il flusso facendo clic sul pulsante *Pause* e quindi espandere un messaggio per analizzarne i dettagli.

> [AZURE.NOTE]Prima di eseguire l'applicazione, assicurarsi di avere un cluster di sviluppo locale in esecuzione. Per informazioni sulla configurazione dell'ambiente locale, vedere l'articolo relativo alla [configurazione dell'ambiente di sviluppo Service Fabric](service-fabric-get-started.md).

![Visualizzare gli eventi di diagnostica in Visual Studio](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)


## Passaggi successivi

[Debug dell'applicazione di Service Fabric in Visual Studio](service-fabric-debugging-your-application.md)

[Introduzione ai servizi delle API Web di Microsoft Azure Service Fabric con self-hosting OWIN](service-fabric-reliable-services-communication-webapi.md)

[Reliable Collections](service-fabric-reliable-services-reliable-collections.md)

[Gestire un servizio di Service Fabric](service-fabric-manage-your-service-index.md)

[Guida di riferimento per gli sviluppatori per Reliable Services](https://msdn.microsoft.com/library/azure/dn706529.aspx)
 

<!---HONumber=58_postMigration-->