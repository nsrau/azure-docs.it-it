---
title: Eseguire Chaos nei cluster di Service Fabric | Documentazione Microsoft
description: Uso delle API del servizio di fault injection e di analisi del cluster per la gestione di Chaos nel cluster.
services: service-fabric
documentationcenter: .net
author: motanv
manager: rsinha
editor: toddabel
ms.assetid: 2bd13443-3478-4382-9a5a-1f6c6b32bfc9
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/19/2017
ms.author: motanv
translationtype: Human Translation
ms.sourcegitcommit: 1a9bec270650223cd40b3b60e5bc9fc7e212a207
ms.openlocfilehash: ea8f76d146bd630cc8cb0a417d8c090b656150a4


---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Eseguire Chaos in ambiente controllato nei cluster di Service Fabric
I sistemi distribuiti di grandi dimensioni come le infrastrutture cloud sono intrinsecamente inaffidabili. Azure Service Fabric consente agli sviluppatori di scrivere servizi affidabili anche per un'infrastruttura inaffidabile. Per scrivere servizi di qualità elevata, gli sviluppatori devono essere in grado di mettere alla prova un'infrastruttura inaffidabile in modo da testare la stabilità dei servizi.

Il servizio di fault injection e di analisi del cluster (noto anche come Servizio di analisi degli errori) offre agli sviluppatori la possibilità di causare azioni di errore per testare i servizi. Gli errori simulati indotti, tuttavia, possono arrivare solo fino a un certo punto. Per proseguire ulteriormente il test, è possibile usare Chaos.

Chaos simula in tutto il cluster continui errori interfoliati, normali e anomali, per lunghi periodi di tempo. Dopo aver configurato Chaos con la frequenza e il tipo di errori, è possibile avviarlo o arrestarlo attraverso le API C# o PowerShell allo scopo di generare errori nel cluster e nel servizio.

Durante l'esecuzione, Chaos genera eventi diversi che acquisiscono lo stato dell'esecuzione al momento. Ad esempio, ExecutingFaultsEvent contiene tutti gli errori che vengono eseguiti in quell'iterazione. ValidationFailedEvent contiene i dettagli di un errore rilevato durante la convalida del cluster. È possibile chiamare l'API GetChaosReportAsync per ottenere il report delle esecuzioni di Chaos.

## <a name="faults-induced-in-chaos"></a>Errori indotti da Chaos
Chaos genera errori in tutto il cluster di Service Fabric e comprime in poche ore gli errori riscontrati in mesi o anni. La combinazione di errori interfoliati con un'elevata frequenza di errori consente di trovare casi limite che altrimenti non verrebbero considerati. Questa applicazione di Chaos consente di ottenere un notevole miglioramento della qualità del codice del servizio.

Chaos induce errori delle categorie seguenti:

* Riavvio di un nodo
* Riavvio di un pacchetto di codice distribuito
* Rimozione di una replica
* Riavvio di una replica
* Spostamento di una replica primaria (configurabile)
* Spostamento di una replica secondaria (configurabile)

Chaos esegue più iterazioni. Ogni iterazione consiste in errori e convalide cluster per il periodo di tempo specificato. È possibile configurare anche il tempo impiegato per la stabilizzazione del cluster e il completamento della convalida. Se viene rilevato un errore di convalida dei cluster, Chaos genera e mantiene un ValidationFailedEvent con il timestamp UTC e i dettagli dell'errore.

Si consideri, ad esempio, un'istanza di Chaos configurata per un'esecuzione della durata di un'ora e con un massimo di tre errori simultanei. Chaos provoca tre errori, per poi convalidare l'integrità del cluster. Ripete il passaggio precedente finché non viene esplicitamente interrotto con l'API StopChaosAsync o dopo un'ora. Se in una qualsiasi iterazione il cluster diventa non integro, ovvero non si stabilizza entro un tempo configurato, Chaos genera ValidationFailedEvent. Questo evento indica che si è verificato un errore e potrebbe richiedersi un'ulteriore analisi.

Nella sua forma attuale, Chaos provoca solo errori sicuri. Ciò significa che, in assenza di errori esterni, non si verifica mai una perdita di quorum o di dati.

## <a name="important-configuration-options"></a>Opzioni di configurazione importanti
* **TimeToRun**: tempo totale di esecuzione di Chaos prima del completamento con esito positivo. È possibile arrestare Chaos prima sia trascorso il tempo di esecuzione indicato da TimeToRun tramite l'API StopChaos.
* **MaxClusterStabilizationTimeout**: tempo di attesa massimo perché il cluster diventi integro prima che venga eseguito un nuovo controllo. Questa attesa consiste nel ridurre il carico nel cluster durante il ripristino. I controlli eseguiti sono:
  * Se l'integrità del cluster è OK
  * Se l'integrità del servizio è OK
  * Se si ottiene la dimensione di set di replica di destinazione per la partizione di servizio
  * Che non esistano repliche InBuild
* **MaxConcurrentFaults**: numero massimo di errori simultanei indotti in ogni iterazione. A un numero più elevato corrisponde una maggiore aggressività di Chaos, che comporta combinazioni di transizione e failover più complessi. Chaos garantisce che in assenza di errori esterni non si verificherà una perdita di quorum o di dati, a prescindere da quanto è elevata la configurazione.
* **EnableMoveReplicaFaults**: abilita o disabilita gli errori che causano lo spostamento delle repliche primarie o secondarie. Questi errori sono disabilitati per impostazione predefinita.
* **WaitTimeBetweenIterations**: quantità di tempo di attesa tra due iterazioni, ad esempio dopo un ciclo di errori e la convalida corrispondente.
* **WaitTimeBetweenFaults**: quantità di tempo di attesa tra due errori consecutivi in un'iterazione.

## <a name="how-to-run-chaos"></a>Come eseguire Chaos
**C#:**

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
                // If a StoppedEvent is found, exit the loop.
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
**PowerShell:**

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



<!--HONumber=Nov16_HO3-->


