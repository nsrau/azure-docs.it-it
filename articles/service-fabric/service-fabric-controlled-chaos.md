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
ms.date: 05/11/2017
ms.author: motanv
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 00f703cf9e727cd5981c4f8254fc11330e41a470
ms.contentlocale: it-it
ms.lasthandoff: 05/12/2017


---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Eseguire Chaos in ambiente controllato nei cluster di Service Fabric
I sistemi distribuiti di grandi dimensioni come le infrastrutture cloud sono intrinsecamente inaffidabili. Azure Service Fabric consente agli sviluppatori di scrivere servizi distribuiti affidabili in un'infrastruttura inaffidabile. Per scrivere servizi distribuiti affidabili in un'infrastruttura inaffidabile, gli sviluppatori devono poter testare la stabilità dei servizi quando nell'infrastruttura inaffidabile sottostante si verificano transizioni di stato complesse a causa di errori.

Il [servizio di fault injection e analisi del cluster](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-testability-overview) (noto anche come servizio di analisi degli errori) offre agli sviluppatori la possibilità di causare errori per testare i servizi. Questi errori simulati mirati, ad esempio il [riavvio di una partizione](https://docs.microsoft.com/en-us/powershell/module/servicefabric/start-servicefabricpartitionrestart?view=azureservicefabricps), consentono di generare le transizioni di stato più comuni. Gli errori simulati mirati sono tuttavia parziali per definizione e quindi possono non coprire i bug che si verificano solo in sequenze di transizioni di stato difficili da prevedere, lunghe e complesse. Per un test imparziale, è possibile usare Chaos.

Chaos simula errori periodici con interfoliazione, normali e anomali, nel cluster per lunghi periodi di tempo. Dopo aver configurato Chaos con la frequenza e la tipologia di errori, è possibile avviarlo attraverso le API C# o PowerShell per iniziare a generare errori nel cluster e nei servizi. È possibile configurare Chaos per l'esecuzione per un periodo di tempo specifico (ad esempio per un'ora), dopo il quale Chaos si arresta automaticamente oppure è possibile chiamare l'API StopChaos (C# o PowerShell) per arrestarlo in qualsiasi momento.

> [!NOTE]
> Nella sua forma attuale, Chaos causa solo errori sicuri, il che implica che in assenza di errori esterni non si verifica mai una perdita del quorum o di dati.
>

Durante l'esecuzione, Chaos genera eventi diversi che acquisiscono lo stato dell'esecuzione al momento. Un oggetto ExecutingFaultsEvent contiene ad esempio tutti gli errori eseguiti da Chaos nell'iterazione. Un oggetto ValidationFailedEvent contiene i dettagli di un errore di convalida (problemi di integrità o di stabilità) rilevato durante la convalida del cluster. È possibile richiamare l'API GetChaosReport (C# or PowerShell) per ottenere il report delle esecuzioni di Chaos. Gli eventi vengono conservati in un oggetto [Reliable ​Dictionary](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-reliable-services-reliable-collections), che ha un criterio di troncamento influenzato da due configurazioni: **MaxStoredChaosEventCount** (valore predefinito 25000) e **StoredActionCleanupIntervalInSeconds** (valore predefinito 3600). A ogni intervallo indicato da *StoredActionCleanupIntervalInSeconds* Chaos esegue un controllo e tutti gli eventi *MaxStoredChaosEventCount*, escluso il più recente, vengono eliminati dall'oggetto Reliable ​Dictionary.

## <a name="faults-induced-in-chaos"></a>Errori indotti da Chaos
Chaos genera errori in tutto il cluster di Service Fabric e comprime in poche ore gli errori riscontrati in mesi o anni. Grazie alla combinazione di errori con interfoliazione e un'elevata frequenza di errori è possibile trovare casi limite che altrimenti non verrebbero considerati. Questa applicazione di Chaos consente di ottenere un notevole miglioramento della qualità del codice del servizio.

Chaos induce errori delle categorie seguenti:

* Riavvio di un nodo
* Riavvio di un pacchetto di codice distribuito
* Rimozione di una replica
* Riavvio di una replica
* Spostamento di una replica primaria (configurabile)
* Spostamento di una replica secondaria (configurabile)

Chaos esegue più iterazioni. Ogni iterazione consiste in errori e convalide cluster per il periodo di tempo specificato. È possibile configurare anche il tempo impiegato per la stabilizzazione del cluster e il completamento della convalida. Se viene rilevato un errore di convalida dei cluster, Chaos genera e mantiene un ValidationFailedEvent con il timestamp UTC e i dettagli dell'errore. Si consideri, ad esempio, un'istanza di Chaos configurata per un'esecuzione della durata di un'ora e con un massimo di tre errori simultanei. Chaos provoca tre errori, per poi convalidare l'integrità del cluster. Ripete il passaggio precedente finché non viene esplicitamente interrotto con l'API StopChaosAsync o dopo un'ora. Se in una qualsiasi iterazione il cluster diventa non integro, ovvero non si stabilizza entro il tempo definito da MaxClusterStabilizationTimeout, Chaos genera un evento ValidationFailedEvent. Questo evento indica che si è verificato un errore e potrebbe richiedersi un'ulteriore analisi.

Per ottenere gli errori indotti da Chaos, è possibile usare l'API GetChaosReport (PowerShell o C#). L'API ottiene il segmento successivo del report di Chaos in base al token di continuazione passato o all'intervallo di tempo passato. È possibile specificare ContinuationToken per ottenere il segmento successivo del report di Chaos oppure è possibile specificare l'intervallo di tempo tramite StartTimeUtc ed EndTimeUtc, ma non è possibile specificare sia ContinuationToken che l'intervallo di tempo nella stessa chiamata. Quando ci sono più di 100 eventi di Chaos, il report di Chaos viene restituito in segmenti e ogni segmento contiene non più di 100 eventi.

## <a name="important-configuration-options"></a>Opzioni di configurazione importanti
* **TimeToRun**: tempo totale di esecuzione di Chaos prima del completamento con esito positivo. È possibile arrestare Chaos prima sia trascorso il tempo di esecuzione indicato da TimeToRun tramite l'API StopChaos.

> [!NOTE]
> Chaos potrebbe essere ancora in esecuzione dopo che il tempo indicato da *TimeToRun* è trascorso e può essere necessario tempo aggiuntivo (MaxClusterStabilizationTime + MaxConcurrentFaults * WaitTimeBetweenFaults + WaitTimeBetweenIterations) per l'arresto automatico.
>

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

