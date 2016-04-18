<properties
   pageTitle="Test di failover e chaos | Microsoft Azure"
   description="Utilizzando i test chaos dell'infrastruttura di servizi e gli scenari dei test di failover per provocare gli errori e verificare l'affidabilità dei servizi."
   services="service-fabric"
   documentationCenter=".net"
   authors="anmolah"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/25/2016"
   ms.author="anmola"/>

# Scenari di testabilità
Sistemi distribuiti di grandi dimensioni come le infrastrutture cloud sono intrinsecamente inaffidabili. Azure Service Fabric offre agli sviluppatori la possibilità di scrivere servizi destinati ad essere eseguiti in infrastrutture inaffidabili. Per scrivere servizi di qualità elevata, gli sviluppatori devono essere in grado di mettere alla prova un'infrastruttura inaffidabile in modo da testarne la stabilità dei servizi.

Il servizio di analisi degli errori offre agli sviluppatori la possibilità di causare azioni di errore e testare i servizi in presenza di errori. Gli errori simulati indotti, tuttavia, possono arrivare solo fino a un certo punto. Per spingere il test oltre, è possibile usare gli scenari di testi disponibili in Service Fabric: test di chaos e test di failover. Questi scenari simulano in tutto il cluster continui errori interfoliati, normali e anomali, per lunghi periodi di tempo. Dopo la configurazione con la frequenza e il tipo di errori, il test può essere avviato tramite le API C# o PowerShell, allo scopo di generare errori nel cluster e nel servizio.

## Test chaos
Lo scenario chaos genera errori nell’intero cluster dell’infrastruttura di servizi. Lo scenario comprime in alcune ore gli errori che in genere si osservano in mesi o anni. La combinazione di errori interfoliati con un'elevata frequenza di errori consente di trovare casi limite che altrimenti non verrebbero considerati. In tal modo è possibile ottenere un notevole miglioramento della qualità del codice del servizio.

### Errori simulati nel test chaos
 - Riavvio di un nodo
 - Riavvio di un pacchetto di codice distribuito
 - Rimozione di una replica
 - Riavvio di una replica
 - Spostamento di una replica primaria (facoltativo)
 - Spostamento di una replica secondaria (facoltativo)

Il test chaos esegue più iterazioni di errori e di convalide cluster per il periodo di tempo specificato. È possibile configurare anche il tempo impiegato per la stabilizzazione del cluster e il completamento della convalida. Lo scenario ha esito negativo anche se si verifica un singolo errore nella convalida del cluster.

Si consideri, ad esempio, un test configurato per un'esecuzione della durata di un'ora e con un massimo di tre errori simultanei. Il test provocherà tre errori e quindi convaliderà l'integrità del cluster. Il test ripeterà il passaggio precedente fino a quando non sarà trascorsa un'ora o lo stato del cluster diventerà non integro. Se in una qualsiasi iterazione il cluster diventa non integro, ovvero non si stabilizza entro un tempo configurato, il test avrà esito negativo con un'eccezione. Questa eccezione indica che si è verificato un errore ed è necessaria un'ulteriore analisi.

Nella forma attuale, il motore di generazione di errori del test Chaos provoca solo errori sicuri. In assenza di errori esterni, quindi, non si verifica mai una perdita di quorum o di dati.

### Opzioni di configurazione importanti
 - **TimeToRun**: tempo totale per il quale il test verrà eseguito prima del completamento con esito positivo. Il test può essere completato in anticipo anziché produrre un errore di convalida.
 - **MaxClusterStabilizationTimeout**: tempo di attesa massimo perché il cluster diventi integro prima che ne venga dichiarato l'esito negativo. I controlli eseguiti verificano che il cluster e il servizio siano integri, che la dimensione del set di repliche di destinazione sia stata raggiunta per la partizione di servizio e che non siano presenti repliche InBuild.
 - **MaxConcurrentFaults**: numero massimo di errori simultanei indotti in ogni iterazione. Maggiore è il numero, più aggressivo è il test e più complesse saranno le combinazioni di failover e transizioni. Il test garantisce che in assenza di errori esterni non si verificherà una perdita di quorum o di dati, a prescindere da quanto è elevata la configurazione.
 - **EnableMoveReplicaFaults**: abilita o disabilita gli errori che causano lo spostamento delle repliche primarie o secondarie. Questi errori sono disabilitati per impostazione predefinita.
 - **WaitTimeBetweenIterations**: quantità di tempo di attesa tra due iterazioni, ad esempio dopo un ciclo di errori e la convalida corrispondente.

### Come eseguire il test chaos
Esempio C#

```csharp
using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";

        Console.WriteLine("Starting Chaos Test Scenario...");
        try
        {
            RunChaosTestScenarioAsync(clusterConnection).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Chaos Test Scenario did not complete: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Chaos Test Scenario completed.");
        return 0;
    }

    static async Task RunChaosTestScenarioAsync(string clusterConnection)
    {
        TimeSpan maxClusterStabilizationTimeout = TimeSpan.FromSeconds(180);
        uint maxConcurrentFaults = 3;
        bool enableMoveReplicaFaults = true;

        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);

        // The chaos test scenario should run at least 60 minutes or until it fails.
        TimeSpan timeToRun = TimeSpan.FromMinutes(60);
        ChaosTestScenarioParameters scenarioParameters = new ChaosTestScenarioParameters(
          maxClusterStabilizationTimeout,
          maxConcurrentFaults,
          enableMoveReplicaFaults,
          timeToRun);

        // Other related parameters:
        // Pause between two iterations for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenIterations = TimeSpan.FromSeconds(30);
        // Pause between concurrent actions for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenFaults = TimeSpan.FromSeconds(10);

        // Create the scenario class and execute it asynchronously.
        ChaosTestScenario chaosScenario = new ChaosTestScenario(fabricClient, scenarioParameters);

        try
        {
            await chaosScenario.ExecuteAsync(CancellationToken.None);
        }
        catch (AggregateException ae)
        {
            throw ae.InnerException;
        }
    }
}
```

PowerShell

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```


## Test di failover

Lo scenario di test di failover è una versione dello scenario di test chaos destinata a una specifica partizione del servizio. Verifica l'effetto del failover in una specifica partizione di servizio senza interessare gli altri servizi. Una volta configurato con informazioni sulla partizione di destinazione e altri parametri, il test viene eseguito come strumento lato client che usa API C# o PowerShell per generare errori per una partizione di servizio. Lo scenario ripete una sequenza di errori simulati e test di convalida del servizio, mentre viene eseguita la logica di business per fornire un carico di lavoro. Un errore nella convalida del servizio indica un problema necessita di ulteriore analisi.

### Errori simulati nel test di failover
- Riavvio di un pacchetto di codice distribuito in cui è ospitata la partizione
- Rimozione di una replica primaria o secondaria o di un'istanza senza stato
- Riavvio di una replica primaria o secondaria (in caso di servizio persistente)
- Spostamento di una replica primaria
- Spostamento di una replica secondaria
- Riavvio della partizione

Il test di failover provoca un errore scelto e quindi esegue la convalida del servizio per garantirne la stabilità. Il test di failover provoca un solo errore per volta, anziché i possibili errori multipli generati dal test chaos. Se dopo ogni errore la partizione di servizio non si stabilizza entro il timeout configurato, il test ha esito negativo. Il test provoca solo errori sicuri. In assenza di errori esterni, quindi, non si verifica una perdita di quorum o di dati.

### Opzioni di configurazione importanti
 - **PartitionSelector**: oggetto selettore che specifica la partizione che deve essere impostata come destinazione.
 - **TimeToRun**: tempo totale per il quale il test verrà eseguito prima del completamento.
 - **MaxServiceStabilizationTimeout**: tempo di attesa massimo perché il cluster diventi integro prima che ne venga dichiarato l'esito negativo. I controlli eseguiti verificano che il cluster sia integro, che la dimensione del set di repliche di destinazione sia stata raggiunta per tutte le partizioni e che non siano presenti repliche InBuild.
 - **WaitTimeBetweenFaults**: tempo di attesa tra ogni ciclo di errore e di convalida.

### Come eseguire il test di failover

**C#**

```csharp
using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");

        Console.WriteLine("Starting Chaos Test Scenario...");
        try
        {
            RunFailoverTestScenarioAsync(clusterConnection, serviceName).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Chaos Test Scenario did not complete: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Chaos Test Scenario completed.");
        return 0;
    }

    static async Task RunFailoverTestScenarioAsync(string clusterConnection, Uri serviceName)
    {
        TimeSpan maxServiceStabilizationTimeout = TimeSpan.FromSeconds(180);
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);

        // The chaos test scenario should run at least 60 minutes or until it fails.
        TimeSpan timeToRun = TimeSpan.FromMinutes(60);
        FailoverTestScenarioParameters scenarioParameters = new FailoverTestScenarioParameters(
          randomPartitionSelector,
          timeToRun,
          maxServiceStabilizationTimeout);

        // Other related parameters:
        // Pause between two iterations for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenIterations = TimeSpan.FromSeconds(30);
        // Pause between concurrent actions for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenFaults = TimeSpan.FromSeconds(10);

        // Create the scenario class and execute it asynchronously.
        FailoverTestScenario failoverScenario = new FailoverTestScenario(fabricClient, scenarioParameters);

        try
        {
            await failoverScenario.ExecuteAsync(CancellationToken.None);
        }
        catch (AggregateException ae)
        {
            throw ae.InnerException;
        }
    }
}
```


**PowerShell**

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/SampleApp/SampleService"

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindSingleton
```

<!---HONumber=AcomDC_0406_2016-->