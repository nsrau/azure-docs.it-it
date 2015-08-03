<properties
   pageTitle="Diagnostica e monitoraggio delle prestazioni per Azure Service Fabric Actors"
   description="Questo articolo illustra le funzionalità di diagnostica e monitoraggio delle prestazioni disponibili nel runtime di Fabric Actors, inclusi gli eventi e i contatori delle prestazioni emessi dal runtime."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/21/2015"
   ms.author="abhisram"/>

# Diagnostica e monitoraggio delle prestazioni per Fabric Actors
Il runtime di Fabric Actors emette eventi [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) e [contatori delle prestazioni](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx) che forniscono informazioni utili per comprendere il funzionamento del runtime e per eseguire la risoluzione dei problemi e il monitoraggio delle prestazioni.

## Eventi EventSource
Il nome di EventSource per il runtime di Fabric Actors è "Microsoft-ServiceFabric-Actors". Gli eventi provenienti da questa origine vengono visualizzati nella finestra degli [eventi di diagnostica](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) quando l'applicazione attore viene sottoposta a [debug in Visual Studio](service-fabric-debugging-your-application.md).

Service Fabric consente inoltre di indirizzare questi eventi ad [Application Insights](http://azure.microsoft.com/services/application-insights/). Per altre informazioni, vedere l'articolo relativo alla [configurazione di Application Insights per Service Fabric](service-fabric-diagnostics-application-insights-setup.md).

Altri esempi di strumenti e tecnologie che consentono di raccogliere e/o visualizzare eventi EventSource sono [PerfView](http://www.microsoft.com/download/details.aspx?id=28567), [Diagnostica di Azure](../cloud-services-dotnet-diagnostics.md), [Semantic Logging](https://msdn.microsoft.com/library/dn774980.aspx) e [Microsoft TraceEvent Library](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

### Parole chiave
Tutti gli eventi appartenenti all'EventSource Fabric Actors sono associati a una o più parole chiave. Questo consente di filtrare gli eventi che vengono raccolti. I bit delle parole chiave definiti sono i seguenti:

|Bit|Descrizione|
|---|---|
|0x1|Set di eventi importanti che riepilogano il funzionamento del runtime di Fabric Actors|
|0x2|Set di eventi che descrivono le chiamate ai metodi degli attori. Per altre informazioni, vedere l'[argomento introduttivo sugli attori](service-fabric-reliable-actors-introduction.md#actors).|
|0x4|Set di eventi relativi allo stato dell'attore. Per altre informazioni, vedere l'argomento relativo agli [attori con stato](service-fabric-reliable-actors-introduction.md#stateful-actors).|
|0x8|Set di eventi correlati alla concorrenza basata su turni nell'attore. Per altre informazioni, vedere l'argomento relativo alla [concorrenza](service-fabric-reliable-actors-introduction.md#concurrency).|

## Contatori delle prestazioni
Il runtime di Fabric Actors definisce le categorie di contatori delle prestazioni seguenti.

|Categoria|Descrizione|
|---|---|
|Service Fabric Actor|Contatori specifici degli attori di Service Fabric, ad esempio per il tempo necessario per salvare lo stato di un attore.|
|Service Fabric Actor Method|Contatori specifici dei metodi implementati dagli attori di Service Fabric, ad esempio per la frequenza con cui viene richiamato un metodo di un attore.|

Ognuna di queste categorie include uno o più contatori.

L'applicazione [Performance Monitor di Windows](https://technet.microsoft.com/library/cc749249.aspx), disponibile per impostazione predefinita nel sistema operativo Windows, può essere usata per raccogliere e visualizzare i dati dei contatori delle prestazioni. [Diagnostica di Azure](../cloud-services-dotnet-diagnostics.md) rappresenta una valida alternativa per la raccolta di tali dati e il relativo caricamento nelle tabelle di Azure.

### Nomi delle istanze dei contatori delle prestazioni
Un cluster con un numero elevato di servizi attore o di partizioni di servizi attore disporrà di un numero considerevole di istanze di contatori delle prestazioni degli attori. I nomi di tali istanze possono facilitare l'identificazione della [partizione](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) specifica e del metodo dell'attore (se applicabile) a cui l'istanza è associata.

#### Categoria `Service Fabric Actor`
Per la categoria `Service Fabric Actor`, i nomi delle istanze dei contatori sono nel formato seguente:

`ServiceFabricPartitionID_ActorsRuntimeInternalID`

*ServiceFabricPartitionID* è la rappresentazione sotto forma di stringa dell'ID partizione di Service Fabric a cui è associata l'istanza del contatore delle prestazioni. L'ID partizione è un GUID e la relativa rappresentazione come stringa viene generata usando il metodo [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) con l'identificatore di formato "D".

*ActorRuntimeInternalID* è la rappresentazione sotto forma di stringa di un numero intero a 64 bit generato dal runtime di Fabric Actors per il proprio uso interno. Viene incluso nel nome dell'istanza del contatore delle prestazioni per assicurarne l'univocità ed evitare conflitti con altri nomi di istanze di contatori delle prestazioni. Agli utenti non serve tentare di interpretare questa parte del nome dell'istanza del contatore delle prestazioni.

Quello che segue è un esempio di un nome di istanza per un contatore appartenente alla categoria "Service Fabric Actor":

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046`

Nell'esempio precedente `2740af29-78aa-44bc-a20b-7e60fb783264` è la rappresentazione come stringa dell'ID partizione di Service Fabric e `635650083799324046` è l'ID a 64 bit generato per l'uso interno del runtime.

#### Categoria `Service Fabric Actor Method`
Per la categoria `Service Fabric Actor Method`, i nomi delle istanze dei contatori sono nel formato seguente:

`MethodName_ActorsRuntimeMethodId_ServiceFabricPartitionID_ActorsRuntimeInternalID`

*MethodName* è il nome del metodo dell'attore a cui è associata l'istanza del contatore delle prestazioni. Il formato del nome del metodo è determinato in base a una parte della logica del runtime di Fabric Actors che cerca di trovare un compromesso tra la leggibilità del nome e i vincoli relativi alla lunghezza massima dei nomi delle istanze dei contatori delle prestazioni in Windows.

*ActorsRuntimeMethodId* è la rappresentazione sotto forma di stringa di un numero intero a 32 bit generato dal runtime di Fabric Actors per il proprio uso interno. Viene incluso nel nome dell'istanza del contatore delle prestazioni per assicurarne l'univocità ed evitare conflitti con altri nomi di istanze di contatori delle prestazioni. Agli utenti non serve tentare di interpretare questa parte del nome dell'istanza del contatore delle prestazioni.

*ServiceFabricPartitionID* è la rappresentazione sotto forma di stringa dell'ID partizione di Service Fabric a cui è associata l'istanza del contatore delle prestazioni. L'ID partizione è un GUID e la relativa rappresentazione come stringa viene generata usando il metodo [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) con l'identificatore di formato "D".

*ActorRuntimeInternalID* è la rappresentazione sotto forma di stringa di un numero intero a 64 bit generato dal runtime di Fabric Actors per il proprio uso interno. Viene incluso nel nome dell'istanza del contatore delle prestazioni per assicurarne l'univocità ed evitare conflitti con altri nomi di istanze di contatori delle prestazioni. Agli utenti non serve tentare di interpretare questa parte del nome dell'istanza del contatore delle prestazioni.

Quello che segue è un esempio di un nome di istanza per un contatore appartenente alla categoria "Service Fabric Actor Method":

`ivoicemailboxactor.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486`

Nell'esempio precedente `ivoicemailboxactor.leavemessageasync` è il nome del metodo, `2` è l'ID a 32 bit generato per l'uso interno del runtime, `89383d32-e57e-4a9b-a6ad-57c6792aa521` è la rappresentazione come stringa dell'ID partizione di Service Fabric e `635650083804480486` è l'ID a 64 bit generato per l'uso interno del runtime.

## Elenco degli eventi e dei contatori delle prestazioni

### Eventi dei metodi degli attori e relativi contatori delle prestazioni
Il runtime di Fabric Actors emette gli eventi seguenti relativi ai [metodi degli attori](service-fabric-reliable-actors-introduction.md#actors).

|Nome evento|ID evento|Livello|Parole chiave|Descrizione|
|---|---|---|---|---|
|ActorMethodStart|7|Dettagliato|0x2|Il runtime di Actors sta per richiamare un metodo di un attore.|
|ActorMethodStop|8|Dettagliato|0x2|È terminata l'esecuzione di un metodo di un attore, ovvero la chiamata asincrona del runtime a tale metodo ha restituito il controllo e l'attività restituita dal metodo stesso è stata completata.|
|ActorMethodThrewException|9|Avviso|0x3|È stata generata un'eccezione durante l'esecuzione di un metodo di un attore, durante la chiamata asincrona del runtime a tale metodo o durante l'esecuzione dell'attività restituita dal metodo stesso. Questo evento indica un problema relativo al codice dell'attore che deve essere analizzato.|

Il runtime di Fabric Actors pubblica i contatori delle prestazioni seguenti relativi all'esecuzione dei metodi degli attori.

|Nome categoria|Nome contatore|Descrizione|
|---|---|---|
|Service Fabric Actor Method|Invocations/Sec|Numero di volte in cui il metodo del servizio attore viene richiamato al secondo|
|Service Fabric Actor Method|Average milliseconds per invocation|Tempo necessario per eseguire il metodo del servizio attore in millisecondi|
|Service Fabric Actor Method|Exceptions thrown/Sec|Numero di volte in cui il metodo del servizio attore ha generato un'eccezione al secondo|

### Eventi di concorrenza e relativi contatori delle prestazioni
Il runtime di Fabric Actors emette gli eventi seguenti relativi alla [concorrenza](service-fabric-reliable-actors-introduction.md#concurrency).

|Nome evento|ID evento|Livello|Parole chiave|Descrizione|
|---|---|---|---|---|
|ActorMethodCallsWaitingForLock|12|Dettagliato|0x8|Questo evento viene scritto all'inizio di ogni nuovo turno in un attore. Include il numero di chiamate dell'attore in sospeso, in attesa di acquisire il blocco per attore che applica la concorrenza basata su turni.|

Il runtime di Fabric Actors pubblica i contatori delle prestazioni seguenti relativi alla concorrenza.

|Nome categoria|Nome contatore|Descrizione|
|---|---|---|
|Service Fabric Actor|# of actor calls waiting for actor lock|Numero di chiamate dell'attore in sospeso, in attesa di acquisire il blocco per attore che applica la concorrenza basata su turni.|

### Eventi di gestione dello stato degli attori e relativi contatori delle prestazioni
Il runtime di Fabric Actors emette gli eventi seguenti relativi alla [gestione dello stato degli attori](service-fabric-reliable-actors-introduction.md#actor-state-management).

|Nome evento|ID evento|Livello|Parole chiave|Descrizione|
|---|---|---|---|---|
|ActorSaveStateStart|10|Dettagliato|0x4|Il runtime di Actors sta per salvare lo stato di un attore.|
|ActorSaveStateStop|11|Dettagliato|0x4|Il runtime di Actors ha finito di salvare lo stato di un attore.|

Il runtime di Fabric Actors pubblica i contatori delle prestazioni seguenti relativi alla gestione dello stato degli attori.

|Nome categoria|Nome contatore|Descrizione|
|---|---|---|
|Service Fabric Actor|Average milliseconds per save state operation|Tempo necessario per salvare lo stato dell'attore in millisecondi|

### Eventi relativi alle istanze di attori senza stato
Il runtime di Fabric Actors emette gli eventi seguenti relativi alle [istanze di attori senza stato](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-stateless-actors).

|Nome evento|ID evento|Livello|Parole chiave|Descrizione|
|---|---|---|---|---|
|ServiceInstanceOpen|3|Informazioni|0x1|Istanza dell'attore senza stato aperta. Ciò significa che gli attori per questa partizione possono essere creati all'interno dell'istanza ed eventualmente anche di altre istanze.|
|ServiceInstanceClose|4|Informazioni|0x1|Istanza dell'attore senza stato chiusa. Ciò significa che gli attori per questa partizione non verranno più creati all'interno dell'istanza. Non verranno recapitate nuove richieste agli attori già creati all'interno dell'istanza. Gli attori verranno distrutti dopo il completamento di qualsiasi richiesta in corso.|

### Eventi relativi alle repliche di attori con stato
Il runtime di Fabric Actors emette gli eventi seguenti relativi alle [repliche di attori con stato](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-stateful-actors).

|Nome evento|ID evento|Livello|Parole chiave|Descrizione|
|---|---|---|---|---|
|ReplicaChangeRoleToPrimary|1|Informazioni|0x1|Il ruolo della replica dell'attore con stato è cambiato e la replica è diventata primaria. Ciò significa che gli attori per questa partizione verranno creati all'interno della replica.|
|ReplicaChangeRoleFromPrimary|2|Informazioni|0x1|Il ruolo della replica dell'attore con stato è cambiato e la replica non è più primaria. Ciò significa che gli attori per questa partizione non verranno più creati all'interno della replica. Non verranno recapitate nuove richieste agli attori già creati all'interno della replica. Gli attori verranno distrutti dopo il completamento di qualsiasi richiesta in corso.|

### Eventi di attivazione e disattivazione degli attori
Il runtime di Fabric Actors emette gli eventi seguenti relativi all'[attivazione e disattivazione degli attori](service-fabric-reliable-actors-lifecycle.md).

|Nome evento|ID evento|Livello|Parole chiave|Descrizione|
|---|---|---|---|---|
|ActorActivated|5|Informazioni|0x1|Un attore è stato attivato.|
|ActorDeactivated|6|Informazioni|0x1|Un attore è stato disattivato.|

<!---HONumber=July15_HO4-->