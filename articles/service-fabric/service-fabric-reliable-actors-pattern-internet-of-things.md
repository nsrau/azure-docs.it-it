
<properties
   pageTitle="Reliable Actors per Internet delle cose | Microsoft Azure"
   description="Service Fabric Reliable Actors costituisce il componente principale di un sistema che integra il front-end di un sistema di messaggistica in grado di supportare più protocolli di trasporto, tra cui HTTPS, MQTT e AMQP."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/14/2015"
   ms.author="vturecek"/>

# Modello di progettazione Reliable Actors: Internet delle cose
Poiché l'Internet delle cose è diventato una nuova tendenza, nonché uno dei principali progressi tecnologici nel campo dei dispositivi e dei servizi cloud, gli sviluppatori hanno iniziato a creare i propri sistemi sulla base dei componenti principali di questa nuova realtà. Il diagramma seguente illustra i principali scenari ottenuti con Azure Service Fabric Reliable Actors:

![Scenari principali di Service Fabric Reliable Actors][1]

Service Fabric Reliable Actors costituisce il componente principale (come un livello intermedio) di un sistema che integra il front-end di un sistema di messaggistica in grado di supportare più protocolli di trasporto, tra cui HTTPS, MQTT o AMQP, e comunica con gli attori che rappresentano i singoli dispositivi. Poiché gli attori possono mantenere lo stato, la modellazione dei flussi (soprattutto di quelli con stato) e la successiva aggregazione per dispositivo risultano operazioni molti semplici. Se i dati devono essere salvati in modo permanente, è possibile anche scaricare su richiesta o in base a un timer, mantenendo comunque gli ultimi N bit di dati in un'altra variabile per eventuali operazioni di ricerca.

Per mantenere lo stato attivo sul modello Reliable Actors, in questi esempi sono stati deliberatamente omessi i dettagli del livello relativo agli eventi e alla messaggistica, in modo che gli attori possano comunicare con i dispositivi . Esistono essenzialmente due scenari di questo tipo, in genere combinati insieme:

* **Raccolta dei dati di telemetria e di stato da uno o più dispositivi mantenendone lo stato**. Si considerino ad esempio decine di migliaia di trappole per topi (uno scenario cliente reale) che inviano dati per comunicare se sono stati catturati topi al proprio interno. I dati vengono aggregati per area geografica e, se in un'area è stata catturata una quantità sufficiente di topi, viene inviato un tecnico per pulire le trappole. Una trappola per topi come un attore? Sì. Un attore gruppo per area come un aggregatore? Certo.

* **Distribuzione della configurazione e del comportamento di un dispositivo a uno o più dispositivi**. Si pensi ai dispositivi domestici a energia solare in cui vengono inserite diverse configurazioni in base ai modelli di consumo e alla stagionalità.

## Dati di telemetria e raggruppamento dei dispositivi

Si consideri in primo luogo un caso in cui più dispositivi, ad esempio decine di migliaia, vengano raggruppati e ognuno di essi invii dati di telemetria al gruppo a cui è associato. Nell'esempio seguente, il cliente ha distribuito alcuni dispositivi in ogni area geografica. Nel momento in cui viene acceso, un dispositivo invia un messaggio **ActivateMe** con informazioni rilevanti come la posizione e la versione. L'attore associato al dispositivo (tramite il relativo ID) imposta lo stato iniziale per il dispositivo, ad esempio salvandone lo stato in locale (che è possibile debba essere salvato in modo permanente) e registrando un attore gruppo. Ai fini di questa simulazione, si assegna un gruppo casuale.

Nell'ambito del processo di inizializzazione, è possibile configurare il dispositivo recuperando i dati di configurazione da un attore gruppo o da un altro agente. In questo modo, i dispositivi non presentano inizialmente alcuna forma di intelligenza, che acquisiscono invece con la fase di inizializzazione. Al termine di questa fase, il dispositivo e l'attore sono pronti per inviare ed elaborare i dati di telemetria.

Tutti i dispositivi inviano periodicamente informazioni di telemetria agli attori corrispondenti. Se un attore è già attivato, viene usato quello stesso attore, altrimenti viene attivato. A questo punto, l'attore può recuperare lo stato da un archivio stabile, se necessario. Quando l'attore riceve informazioni di telemetria, le memorizza in una variabile locale.

Si procede in questo modo per semplificare l'esempio. In un'implementazione reale, le informazioni verrebbero probabilmente salvate in un archivio esterno per consentire al processo di monitorare e diagnosticare l'integrità e le prestazioni del dispositivo. I dati di telemetria, infine, vengono inviati all'attore gruppo a cui appartiene logicamente l'attore dispositivo.

## Esempio di codice per Internet delle cose: telemetria

```csharp
public interface IThing : IActor
{
    Task ActivateMe(string region, int version);
    Task SendTelemetryAsync(ThingTelemetry telemetry);
}

[DataContract]
class ThingState
{
	[DataMember]
	public List<ThingTelemetry> _telemetry;
	[DataMember]
	public ThingInfo _deviceInfo;
	[DataMember]
	long _deviceGroupId;
}

public class Thing : StatefulActor<ThingState>, IThing
{

    protected override Task OnActivateAsync()
    {
        State._telemetry = new List<ThingTelemetry>();
        State._deviceGroupId = -1; // not activated
        return base.OnActivateAsync();
    }

    public Task SendTelemetryAsync(ThingTelemetry telemetry)
    {
        State._telemetry.Add(telemetry); // saving data at the device level
        if (State._deviceGroupId != -1)
        {
            var deviceGroup = ActorProxy.Create<IThingGroup>(State._deviceGroupId);
            return deviceGroup.SendTelemetryAsync(telemetry); // sending telemetry data for aggregation
        }
        return Task.FromResult(true);
    }

    public Task ActivateMe(string region, int version)
    {
        State._deviceInfo = new ThingInfo()
        {
            DeviceId = this.GetPrimaryKeyLong(),
            Region = region,
            Version = version
        };

        // based on the info, assign a group... for demonstration we are assigning a random group
        State._deviceGroupId = new Random().Next(10, 12);

        var deviceGroup = ActorProxy.Create<IThingGroup>(State._deviceGroupId);
        return deviceGroup.RegisterDevice(State._deviceInfo);
    }
}
```

A livello di gruppo, l'obiettivo di questo esempio è monitorare i dispositivi del gruppo e aggregare i dati di telemetria per produrre avvisi per i tecnici. In questo caso, il cliente intende inviare dei tecnici nelle aree in cui è presente un certo numero di dispositivi difettosi. Il cliente, ovviamente, vuole anche limitare i costi riducendo al minimo i tempi di spostamento. Per questo motivo, ogni attore gruppo mantiene uno stato aggregato dei dispositivi difettosi in ogni area. Quando questo numero raggiunge una determinata soglia, il cliente invia un tecnico nell'area per sostituire o riparare i dispositivi difettosi.

Le sezioni seguente illustrano nel dettaglio questo processo.

## Esempio di codice per Internet delle cose: raggruppamento e aggregazione

```csharp
public interface IThingGroup : IActor
{
    Task RegisterDevice(ThingInfo deviceInfo);
    Task UnregisterDevice(ThingInfo deviceInfo);
    Task SendTelemetryAsync(ThingTelemetry telemetry);
}

[DataContract]
class ThingGroupState
{
    [DataMember]
    public List<ThingInfo> _devices;
    [DataMember]
    public Dictionary<string, int> _faultsPerRegion;
    [DataMember]
    public List<ThingInfo> _faultyDevices;
}

public class ThingGroup : StatefulActor<ThingGroupState>, IThingGroup
{

    protected override Task OnActivateAsync()
    {
        State._devices = new List<ThingInfo>();
        State._faultsPerRegion = new Dictionary<string, int>();
        State._faultyDevices = new List<ThingInfo>();

        return base.OnActivateAsync();
    }

    public Task RegisterDevice(ThingInfo deviceInfo)
    {
        State._devices.Add(deviceInfo);
        return Task.FromResult(true);
    }

    public Task UnregisterDevice(ThingInfo deviceInfo)
    {
        State._devices.Remove(deviceInfo);
        return Task.FromResult(true);
    }

    public Task SendTelemetryAsync(ThingTelemetry telemetry)
    {
        if (telemetry.DevelopedFault)
        {
            if (false == _faultsPerRegion.ContainsKey(telemetry.Region))
            {
                State._faultsPerRegion[telemetry.Region] = 0;
            }
            State._faultsPerRegion[telemetry.Region]++;
            State._faultyDevices.Add(_devices.Where(d => d.DeviceId == telemetry.DeviceId).FirstOrDefault());

            if (State._faultsPerRegion[telemetry.Region] > _devices.Count(d => d.Region == telemetry.Region) / 3)
            {
                Console.WriteLine("Sending an engineer to repair/replace devices in {0}", telemetry.Region);
                foreach(var device in State._faultyDevices.Where(d => d.Region == telemetry.Region).ToList())
                {
                    Console.WriteLine("\t{0}", device);
                }
            }
        }

        return Task.FromResult(true);
    }
}
```

Il processo, come si vede, è relativamente semplice. Dopo l'esecuzione di alcuni semplici test, l'output avrà l'aspetto seguente:

```
Sending an engineer to repair/replace devices in Richmond
    Device = 33 Region = Richmond Version = 4
    Device = 79 Region = Richmond Version = 5
    Device = 89 Region = Richmond Version = 3
    Device = 63 Region = Richmond Version = 2
    Device = 85 Region = Richmond Version = 4
```

Si può pensare che sarebbe stato meglio se i dispositivi fossero stati raggruppati per area. È possibile decidere a propria discrezione la modalità di raggruppamento e/o partizione dei dispositivi: per posizione geografica, tipo, versione, tenant o altri criteri.

È presente tuttavia un punto che merita particolare attenzione: lo stato del dispositivo rispetto a quanto indicato da report e analisi. È necessario evitare query di tipo fan-out durante la compilazione degli attori report: la creazione di istanze non necessarie e una diminuzione delle prestazioni sono solo due degli svantaggi che comporterebbero. Ecco perché l'illustrazione del modello è stata resa esplicita. Si consigliano quindi i due approcci seguenti per la creazione di report:

* Usare un attore a livello di gruppo, ad esempio un aggregatore, per mantenere una visualizzazione per il gruppo. Lasciare quindi che ogni attore invii dinamicamente all'attore gruppo solo i dati pertinenti. L'attore a livello di gruppo può essere quindi usato per visualizzare lo stato dei dispositivi nel gruppo.

* Gestire un archivio esplicito progettato per la creazione di report. Un aggregatore può memorizzare i dati nel buffer e inviarli periodicamente a un archivio di report per poter eseguire su di essi operazioni di query e analisi.

## Funzionamento dei dispositivi
Questa sezione illustra invece le operazioni eseguite sui dispositivi. Analogamente ai dispositivi, anche gli attori possono esporre interfacce operative che consentano agli amministratori di eseguire operazioni sui dispositivi. Si supponga, ad esempio, che un amministratore voglia aggiungere una nuova configurazione a un gruppo di dispositivi domestici a energia solare (altro scenario reale), basata sui cambiamenti stagionali e geografici.

In questo caso, l'idea è quella di poter disporre di un controllo granulare sia su ogni dispositivo, usando attori "Thing", sia sulle operazioni di gruppo, usando attori "ThingGroup". Questa idea è praticabile non solo se si stanno aggregando dati provenienti da altri dispositivi, ad esempio dati di telemetria, ma anche se si stanno inviando nuovi dati, come nella configurazione di un elevato numero di dispositivi. La piattaforma si occupa della distribuzione degli attori dispositivo e dello scambio di messaggi tra di essi, che avviene in modo totalmente trasparente per lo sviluppatore.

Nel caso di comunicazioni M2M (machine-to-machine), è possibile anche usare l'interazione diretta attore-attore o il modello hub e spoke illustrato nella [sezione relativa alle reti distribuite e ai grafici](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md). Per gli scenari di tipo M2M, ad esempio, è possibile modellare un attore "Directory/Index" per un gruppo di dispositivi, che consenta loro di trovare e scambiarsi messaggi come illustrato di seguito:

![Modello di un attore "Directory/Index" per un gruppo di dispositivi][2]

Il modello Service Fabric Reliable Actors si occupa anche della durata degli attori. È quindi lecito domandarsi: se si hanno dispositivi sempre attivi e dispositivi connessi occasionalmente, perché mantenere in memoria un attore che si occupa di un dispositivo progettato per connettersi ogni 14 ore? Service Fabric consente di salvare e ripristinare lo stato del dispositivo dove e quando si preferisce.

Sempre più clienti considereranno quindi Service Fabric Reliable Actors un componente essenziale per le proprie implementazioni di tipo Internet delle cose.

## Passaggi successivi
[Modello: Smart Cache](service-fabric-reliable-actors-pattern-smart-cache.md)

[Modello: Reti distribuite e grafici](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Modello: Governance delle risorse](service-fabric-reliable-actors-pattern-resource-governance.md)

[Modello: Composizione dei servizi con stato](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Modello: Calcolo distribuito](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Alcuni anti-modelli](service-fabric-reliable-actors-anti-patterns.md)

[Introduzione a Service Fabric Actors](service-fabric-reliable-actors-introduction.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-internet-of-things/internet-of-things-1.png
[2]: ./media/service-fabric-reliable-actors-pattern-internet-of-things/internet-of-things-2.png

<!---HONumber=AcomDC_0121_2016-->