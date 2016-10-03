<properties
   pageTitle="Eseguire Chaos in ambiente controllato nei cluster di Service Fabric | Microsoft Azure"
   description="Uso delle API del servizio di fault injection e di analisi del cluster per la gestione di Chaos nel cluster."
   services="service-fabric"
   documentationCenter=".net"
   authors="motanv"
   manager="rsinha"
   editor="toddabel"/> 

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/19/2016"
   ms.author="motanv"/> 

# Eseguire Chaos in ambiente controllato nei cluster di Service Fabric
I sistemi distribuiti di grandi dimensioni come le infrastrutture cloud sono intrinsecamente inaffidabili. Azure Service Fabric consente agli sviluppatori di scrivere servizi affidabili anche per un'infrastruttura inaffidabile. Per scrivere servizi di qualità elevata, gli sviluppatori devono essere in grado di mettere alla prova un'infrastruttura inaffidabile in modo da testare la stabilità dei servizi.

Il servizio di fault injection e analisi del cluster offre agli sviluppatori la possibilità di causare azioni di errore e testare i servizi. Gli errori simulati indotti, tuttavia, possono arrivare solo fino a un certo punto. Per far avanzare ulteriormente il test, è possibile usare Chaos. Chaos simula in tutto il cluster continui errori interfoliati, normali e anomali, per lunghi periodi di tempo. Dopo la configurazione con la frequenza e il tipo di errori, il test può essere avviato o arrestato tramite le API C# o PowerShell, allo scopo di generare errori nel cluster e nel servizio. Durante l'esecuzione, Chaos genera eventi diversi che acquisiscono lo stato dell'esecuzione al momento. Ad esempio, ExecutingFaultsEvent contiene tutti gli errori che vengono eseguiti in quell'iterazione; ValidationFailedEvent contiene i dettagli di un errore rilevato durante la convalida del cluster. L'API GetChaosReportAsync può essere richiamata per ottenere il report delle esecuzioni Chaos.

## Errori indotti da Chaos
Chaos genera errori in tutto il cluster di Service Fabric e comprime in poche ore gli errori registrabili in mesi o anni. La combinazione di errori interfoliati con un'elevata frequenza di errori consente di trovare casi limite che altrimenti non verrebbero considerati. Questa applicazione di Chaos consente di ottenere un notevole miglioramento della qualità del codice del servizio. Chaos induce errori delle categorie seguenti:

 - Riavvio di un nodo
 - Riavvio di un pacchetto di codice distribuito
 - Rimozione di una replica
 - Riavvio di una replica
 - Spostamento di una replica primaria (configurabile)
 - Spostamento di una replica secondaria (configurabile)

Chaos esegue più iterazioni di errori; ogni iterazione consiste di errori e convalide cluster per il periodo di tempo specificato. È possibile configurare anche il tempo impiegato per la stabilizzazione del cluster e il completamento della convalida. Se viene rilevato un errore di convalida dei cluster, Chaos genera e mantiene un ValidationFailedEvent con il timestamp UTC e i dettagli dell'errore.

Si consideri, ad esempio, un'istanza di Chaos configurata per un'esecuzione della durata di un'ora e con un massimo di tre errori simultanei. Chaos genera tre errori e quindi convalida l'integrità del cluster e ripete il passaggio precedente finché non viene esplicitamente interrotto con l'API StopChaosAsync o dopo un'ora. Se il cluster non risulta integro durante un'iterazione, vale a dire che non si stabilizza entro un tempo configurato, Chaos genera un ValidationFailedEvent, che indica che si è verificato un errore e potrebbe essere necessario approfondire il problema.

Nella sua forma attuale, Chaos causa solo errori sicuri, il che implica che in assenza di errori esterni non si verifica mai una perdita del quorum o di dati.

## Opzioni di configurazione importanti
 - **TimeToRun**: tempo totale di esecuzione di Chaos prima del completamento con esito positivo. Chaos può essere arrestato prima sia trascorso il tempo indicato da TimeToRun tramite l'API StopChaos.
 - **MaxClusterStabilizationTimeout**: tempo massimo di attesa prima che il cluster risulti integro e possa essere nuovamente verificato; l'attesa è mirata a ridurre il carico nel cluster durante il ripristino. I controlli eseguiti sono
    - Se l'integrità del cluster è OK
    - L'integrità del servizio è OK
    - Si ottiene la dimensione di set di replica di destinazione per la partizione di servizio
    - Non esistono repliche InBuild
 - **MaxConcurrentFaults**: numero massimo di errori simultanei indotti in ogni iterazione. Maggiore è il numero, più aggressivo è Chaos e più complesse saranno le combinazioni di failover e transizioni. Chaos garantisce che in assenza di errori esterni non si verificherà una perdita di quorum o di dati, a prescindere da quanto è elevata la configurazione.
 - **EnableMoveReplicaFaults**: abilita o disabilita gli errori che causano lo spostamento delle repliche primarie o secondarie. Questi errori sono disabilitati per impostazione predefinita.
 - **WaitTimeBetweenIterations**: quantità di tempo di attesa tra due iterazioni, ad esempio dopo un ciclo di errori e la convalida corrispondente.
 - **WaitTimeBetweenFaults**: quantità di tempo di attesa tra due errori consecutivi in un'iterazione.

## Come eseguire Chaos
Esempio C#

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Fabric;

using System.Diagnostics;
using System.Fabric.Chaos.DataStructures;

class Program
{
    private class ChaosEventComparer : IEqualityComparer<ChaosEvent>
    {
        public bool Equals(ChaosEvent x, ChaosEvent y)
        {
            return x.TimeStampUtc.Equals(y.TimeStampUtc);
        }

        public int GetHashCode(ChaosEvent obj)
        {
            return obj.TimeStampUtc.GetHashCode();
        }
    }

    static void Main(string[] args)
    {
        var clusterConnectionString = "localhost:19000";
        using (var client = new FabricClient(clusterConnectionString))
        {
            var startTimeUtc = DateTime.UtcNow;
            var stabilizationTimeout = TimeSpan.FromSeconds(30.0);
            var timeToRun = TimeSpan.FromMinutes(60.0);
            var maxConcurrentFaults = 3;

            var parameters = new ChaosParameters(
                stabilizationTimeout,
                maxConcurrentFaults,
                true, /* EnableMoveReplicaFault */
                timeToRun);

            try
            {
                client.TestManager.StartChaosAsync(parameters).GetAwaiter().GetResult();
            }
            catch (FabricChaosAlreadyRunningException)
            {
                Console.WriteLine("An instance of Chaos is already running in the cluster.");
            }

            var filter = new ChaosReportFilter(startTimeUtc, DateTime.MaxValue);

            var eventSet = new HashSet<ChaosEvent>(new ChaosEventComparer());

            while (true)
            {
                var report = client.TestManager.GetChaosReportAsync(filter).GetAwaiter().GetResult();

                foreach (var chaosEvent in report.History)
                {
                    if (eventSet.add(chaosEvent))
                    {
                        Console.WriteLine(chaosEvent);
                    }
                }

                // When Chaos stops, a StoppedEvent is created.
                // If StoppedEvent is found, exit the loop.
                var lastEvent = report.History.LastOrDefault();

                if (lastEvent is StoppedEvent)
                {
                    break;
                }

                Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
            }
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

$events = @{}
$now = [System.DateTime]::UtcNow

Start-ServiceFabricChaos -TimeToRunMinute $timeToRun -MaxConcurrentFaults $concurrentFaults -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec

while($true)
{
    $stopped = $false
    $report = Get-ServiceFabricChaosReport -StartTimeUtc $now -EndTimeUtc ([System.DateTime]::MaxValue)

    foreach ($e in $report.History) {

        if(-Not ($events.Contains($e.TimeStampUtc.Ticks)))
        {
            $events.Add($e.TimeStampUtc.Ticks, $e)
            if($e -is [System.Fabric.Chaos.DataStructures.ValidationFailedEvent])
            {
                Write-Host -BackgroundColor White -ForegroundColor Red $e
            }
            else
            {
                if($e -is [System.Fabric.Chaos.DataStructures.StoppedEvent])
                {
                    $stopped = $true
                }

                Write-Host $e
            }
        }
    }

    if($stopped -eq $true)
    {
        break
    }

    Start-Sleep -Seconds 1
}

Stop-ServiceFabricChaos
```

<!---HONumber=AcomDC_0921_2016-->