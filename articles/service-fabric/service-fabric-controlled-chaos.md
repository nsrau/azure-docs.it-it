---
title: Eseguire Chaos nei cluster di Service Fabric | Documentazione Microsoft
description: Uso delle API del servizio di fault injection e di analisi del cluster per la gestione di Chaos nel cluster.
services: service-fabric
documentationcenter: .net
author: motanv
manager: anmola
editor: motanv
ms.assetid: 2bd13443-3478-4382-9a5a-1f6c6b32bfc9
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/10/2017
ms.author: motanv
ms.openlocfilehash: 9475774b99ee6bc01fb43ffc6fcddea025779c05
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Eseguire Chaos in ambiente controllato nei cluster di Service Fabric
I sistemi distribuiti di grandi dimensioni come le infrastrutture cloud sono intrinsecamente inaffidabili. Azure Service Fabric consente agli sviluppatori di scrivere servizi distribuiti affidabili in un'infrastruttura inaffidabile. Per scrivere servizi distribuiti affidabili in un'infrastruttura inaffidabile, gli sviluppatori devono poter testare la stabilità dei servizi quando nell'infrastruttura inaffidabile sottostante si verificano transizioni di stato complesse a causa di errori.

Il [servizio di fault injection e analisi del cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-testability-overview) (noto anche come servizio di analisi degli errori) offre agli sviluppatori la possibilità di causare errori per testare i servizi. Questi errori simulati mirati, ad esempio il [riavvio di una partizione](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricpartitionrestart?view=azureservicefabricps), consentono di generare le transizioni di stato più comuni. Gli errori simulati mirati sono tuttavia parziali per definizione e quindi possono non coprire i bug che si verificano solo in sequenze di transizioni di stato difficili da prevedere, lunghe e complesse. Per un test imparziale, è possibile usare Chaos.

Chaos simula errori periodici con interfoliazione, normali e anomali, nel cluster per lunghi periodi di tempo. Un errore normale è costituito da una serie di chiamate API di Service Fabric, ad esempio, il riavvio della replica. Questo errore è normale, poiché si tratta di una chiusura seguita da un'apertura in una replica. La rimozione di una replica e lo spostamento della replica primaria o della replica secondaria sono altri errori normali generati da Chaos. Sono errori anomali le uscite da processi, ad esempio il riavvio di un nodo o di un pacchetto di codice. 

Dopo aver configurato Chaos con la frequenza e la tipologia di errori, è possibile avviarlo attraverso le API C#, PowerShell o REST per iniziare a generare errori nel cluster e nei servizi. È possibile configurare Chaos per l'esecuzione per un periodo di tempo specifico (ad esempio per un'ora), dopo il quale Chaos si arresta automaticamente, oppure è possibile chiamare l'API StopChaos (C#, PowerShell o REST) per arrestarlo in qualsiasi momento.

> [!NOTE]
> Nella sua forma attuale, Chaos causa solo errori sicuri, il che implica che in assenza di errori esterni non si verifica mai una perdita del quorum o di dati.
>

Durante l'esecuzione, Chaos genera eventi diversi che acquisiscono lo stato dell'esecuzione al momento. Un oggetto ExecutingFaultsEvent contiene ad esempio tutti gli errori eseguiti da Chaos nell'iterazione. Un oggetto ValidationFailedEvent contiene i dettagli di un errore di convalida (problemi di integrità o di stabilità) rilevato durante la convalida del cluster. È possibile chiamare l'API GetChaosReport (C#, PowerShell o REST) per ottenere il report delle esecuzioni di Chaos. Gli eventi vengono conservati in un oggetto [Reliable ​Dictionary](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-reliable-collections), che ha un criterio di troncamento influenzato da due configurazioni: **MaxStoredChaosEventCount** (valore predefinito 25000) e **StoredActionCleanupIntervalInSeconds** (valore predefinito 3600). A ogni intervallo indicato da *StoredActionCleanupIntervalInSeconds* Chaos esegue un controllo e tutti gli eventi *MaxStoredChaosEventCount*, escluso il più recente, vengono eliminati dall'oggetto Reliable ​Dictionary.

## <a name="faults-induced-in-chaos"></a>Errori indotti da Chaos
Chaos genera errori in tutto il cluster di Service Fabric e comprime in poche ore gli errori riscontrati in mesi o anni. Grazie alla combinazione di errori con interfoliazione e un'elevata frequenza di errori è possibile trovare casi limite che altrimenti non verrebbero considerati. Questa applicazione di Chaos consente di ottenere un notevole miglioramento della qualità del codice del servizio.

Chaos induce errori delle categorie seguenti:

* Riavvio di un nodo
* Riavvio di un pacchetto di codice distribuito
* Rimozione di una replica
* Riavvio di una replica
* Spostamento di una replica primaria (configurabile)
* Spostamento di una replica secondaria (configurabile)

Chaos esegue più iterazioni. Ogni iterazione consiste in errori e convalide cluster per il periodo di tempo specificato. È possibile configurare anche il tempo impiegato per la stabilizzazione del cluster e il completamento della convalida. Se viene rilevato un errore di convalida dei cluster, Chaos genera e mantiene un ValidationFailedEvent con il timestamp UTC e i dettagli dell'errore. Si consideri, ad esempio, un'istanza di Chaos configurata per un'esecuzione della durata di un'ora e con un massimo di tre errori simultanei. Chaos provoca tre errori, per poi convalidare l'integrità del cluster. Ripete il passaggio precedente finché non viene esplicitamente interrotto con l'API StopChaosAsync o dopo un'ora. Se in una qualsiasi iterazione il cluster diventa non integro, ovvero non si stabilizza o non diventa integro entro il tempo definito da MaxClusterStabilizationTimeout, Chaos genera un evento ValidationFailedEvent. Questo evento indica che si è verificato un errore e potrebbe richiedersi un'ulteriore analisi.

Per sapere quali errori sono stati indotti da Chaos, è possibile usare l'API GetChaosReport (PowerShell, C# o REST). L'API ottiene il segmento successivo del report di Chaos in base al token di continuazione passato o all'intervallo di tempo passato. È possibile specificare ContinuationToken per ottenere il segmento successivo del report di Chaos oppure è possibile specificare l'intervallo di tempo tramite StartTimeUtc ed EndTimeUtc, ma non è possibile specificare sia ContinuationToken che l'intervallo di tempo nella stessa chiamata. Quando ci sono più di 100 eventi di Chaos, il report di Chaos viene restituito in segmenti e ogni segmento contiene non più di 100 eventi.

## <a name="important-configuration-options"></a>Opzioni di configurazione importanti
* **TimeToRun**: tempo totale di esecuzione di Chaos prima del completamento con esito positivo. È possibile arrestare Chaos prima sia trascorso il tempo di esecuzione indicato da TimeToRun tramite l'API StopChaos.

* **MaxClusterStabilizationTimeout**: tempo di attesa massimo perché il cluster diventi integro prima che venga generato un evento ValidationFailedEvent. Questa attesa consiste nel ridurre il carico nel cluster durante il ripristino. I controlli eseguiti sono:
  * Se l'integrità del cluster è OK
  * Se l'integrità del servizio è OK
  * Se si ottiene la dimensione di set di replica di destinazione per la partizione di servizio
  * Che non esistano repliche InBuild
* **MaxConcurrentFaults**: numero massimo di errori simultanei indotti in ogni iterazione. Maggiore è il numero, più aggressivo è Chaos e più complesse saranno le combinazioni di failover e transizioni di stato a cui viene sottoposto il cluster. 

> [!NOTE]
> Indipendentemente da quanto è alto il valore di *MaxConcurrentFaults*, Chaos garantisce che in assenza di errori esterni non si verifichi una perdita di quorum o di dati.
>

* **EnableMoveReplicaFaults**: abilita o disabilita gli errori che causano lo spostamento delle repliche primarie o secondarie. Questi errori sono disabilitati per impostazione predefinita.
* **WaitTimeBetweenIterations**: quantità di tempo di attesa tra due iterazioni. Indica il tempo durante cui Chaos rimane in pausa dopo l'esecuzione di un ciclo di errori e il completamento della convalida dell'integrità del cluster corrispondente. Maggiore è il valore, minore è la frequenza media di inserimento degli errori.
* **WaitTimeBetweenFaults**: quantità di tempo di attesa tra due errori consecutivi in una singola iterazione. Maggiore è il valore, minore sarà la concorrenza degli errori, ovvero la loro sovrapposizione.
* **ClusterHealthPolicy**: criteri di integrità del cluster usati per convalidare l'integrità del cluster tra iterazioni di Chaos. Se si verifica un errore di integrità del cluster o un'eccezione imprevista durante l'esecuzione dell'errore, Chaos attende 30 minuti prima del successivo controllo di integrità, per fornire al cluster il tempo di recupero.
* **Context**: raccolta di coppie chiave-valore di tipo (string, string). La mappa può essere usata per registrare le informazioni sull'esecuzione di Chaos. Non possono esserci più di 100 coppie di questo tipo e ogni stringa (chiave o valore) può essere costituita da un massimo di 4095 caratteri. Questa mappa viene impostata dalla funzione di avvio dell'esecuzione di Chaos per l'archiviazione facoltativa del contesto dell'esecuzione specifica.

## <a name="how-to-run-chaos"></a>Come eseguire Chaos

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

            // The maximum amount of time to wait for all cluster entities to become stable and healthy. 
            // Chaos executes in iterations and at the start of each iteration it validates the health of cluster entities. 
            // During validation if a cluster entity is not stable and healthy within MaxClusterStabilizationTimeoutInSeconds, Chaos generates a validation failed event.
            var maxClusterStabilizationTimeout = TimeSpan.FromSeconds(30.0);

            var timeToRun = TimeSpan.FromMinutes(60.0);

            // MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration. 
            // Chaos executes in iterations and two consecutive iterations are separated by a validation phase. 
            // The higher the concurrency, the more aggressive the injection of faults -- inducing more complex series of states to uncover bugs. 
            // The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
            var maxConcurrentFaults = 3;

            // Describes a map, which is a collection of (string, string) type key-value pairs. The map can be used to record information about
            // the Chaos run. There cannot be more than 100 such pairs and each string (key or value) can be at most 4095 characters long.
            // This map is set by the starter of the Chaos run to optionally store the context about the specific run.
            var startContext = new Dictionary<string, string>{{"ReasonForStart", "Testing"}};

            // Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the lower the fault injection rate.
            var waitTimeBetweenIterations = TimeSpan.FromSeconds(10);

            // Wait time (in seconds) between consecutive faults within a single iteration. 
            // The larger the value, the lower the overlapping between faults and the simpler the sequence of state transitions that the cluster goes through. 
            // The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
            var waitTimeBetweenFaults = TimeSpan.Zero;

            // Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
            var clusterHealthPolicy = new ClusterHealthPolicy
            {
                ConsiderWarningAsError = false,
                MaxPercentUnhealthyApplications = 100,
                MaxPercentUnhealthyNodes = 100
            };
            
            var parameters = new ChaosParameters(
                maxClusterStabilizationTimeout,
                maxConcurrentFaults,
                true, /* EnableMoveReplicaFault */
                timeToRun,
                startContext,
                waitTimeBetweenIterations,
                waitTimeBetweenFaults,
                clusterHealthPolicy);

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

            string continuationToken = null;

            while (true)
            {
                ChaosReport report;
                try
                {
                    report = string.IsNullOrEmpty(continuationToken)
                        ? client.TestManager.GetChaosReportAsync(filter).GetAwaiter().GetResult()
                        : client.TestManager.GetChaosReportAsync(continuationToken).GetAwaiter().GetResult();
                }
                catch (Exception e)
                {
                    if (e is FabricTransientException)
                    {
                        Console.WriteLine("A transient exception happened: '{0}'", e);
                    }
                    else if(e is TimeoutException)
                    {
                        Console.WriteLine("A timeout exception happened: '{0}'", e);
                    }
                    else
                    {
                        throw;
                    }

                    Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
                    continue;
                }

                continuationToken = report.ContinuationToken;

                foreach (var chaosEvent in report.History)
                {
                    if (eventSet.Add(chaosEvent))
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

```powershell
$clusterConnectionString = "localhost:19000"
$timeToRunMinute = 60

# The maximum amount of time to wait for all cluster entities to become stable and healthy. 
# Chaos executes in iterations and at the start of each iteration it validates the health of cluster entities. 
# During validation if a cluster entity is not stable and healthy within MaxClusterStabilizationTimeoutInSeconds, Chaos generates a validation failed event.
$maxClusterStabilizationTimeSecs = 30

# MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration. 
# Chaos executes in iterations and two consecutive iterations are separated by a validation phase. 
# The higher the concurrency, the more aggressive the injection of faults -- inducing more complex series of states to uncover bugs. 
# The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
$maxConcurrentFaults = 3

# Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the lower the fault injection rate.
$waitTimeBetweenIterationsSec = 10

# Wait time (in seconds) between consecutive faults within a single iteration. 
# The larger the value, the lower the overlapping between faults and the simpler the sequence of state transitions that the cluster goes through. 
# The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
$waitTimeBetweenFaultsSec = 0

# Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
$clusterHealthPolicy = new-object -TypeName System.Fabric.Health.ClusterHealthPolicy
$clusterHealthPolicy.MaxPercentUnhealthyNodes = 100
$clusterHealthPolicy.MaxPercentUnhealthyApplications = 100
$clusterHealthPolicy.ConsiderWarningAsError = $False

# Describes a map, which is a collection of (string, string) type key-value pairs. The map can be used to record information about
# the Chaos run. There cannot be more than 100 such pairs and each string (key or value) can be at most 4095 characters long.
# This map is set by the starter of the Chaos run to optionally store the context about the specific run.
$context = @{"ReasonForStart" = "Testing"}

Connect-ServiceFabricCluster $clusterConnectionString

$events = @{}
$now = [System.DateTime]::UtcNow

Start-ServiceFabricChaos -TimeToRunMinute $timeToRunMinute -MaxConcurrentFaults $maxConcurrentFaults -MaxClusterStabilizationTimeoutSec $maxClusterStabilizationTimeSecs -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ClusterHealthPolicy $clusterHealthPolicy

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
                Write-Host $e
                # When Chaos stops, a StoppedEvent is created.
                # If a StoppedEvent is found, exit the loop.
                if($e -is [System.Fabric.Chaos.DataStructures.StoppedEvent])
                {
                    return
                }
            }
        }
    }

    Start-Sleep -Seconds 1
}

```
