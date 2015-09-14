<properties
   pageTitle="Azione di testabilità | Microsoft Azure"
	description="In questo articolo vengono illustrate le azioni di Testabilità trovate in Infrastruttura di servizi di Microsoft Azure."
	services="service-fabric"
	documentationCenter=".net"
	authors="heeldin"
	manager="timlt"
	editor=""/>

<tags
   ms.service="service-fabric"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="NA"
	ms.date="08/31/2015"
	ms.author="heeldin;motanv"/>

# Azioni di Testabilità
Per simulare un'infrastruttura affidabile, Infrastruttura di servizi fornisce agli sviluppatori modi per simulare diversi errori e transizioni di stato del mondo reale. Questi sono esposti come azioni di Testabilità. Le azioni sono le API di basso livello che causano un fault injection specifico, la transizione dello stato o la convalida. Con la combinazione di queste azioni, uno sviluppatore di servizi può scrivere scenari di test completi per i servizi.

Infrastruttura di servizi fornisce alcuni scenari di test comuni predefiniti composti da queste azioni. Si consiglia di utilizzare questi scenari predefiniti che vengono scelti con attenzione, per testare le transizioni di stato comuni e i casi di errore. Tuttavia, è possibile utilizzare azioni per creare scenari di test personalizzati quando si desidera aggiungere la copertura per scenari che non rientrano ancora negli scenari integrati o che sono personalizzati per l'applicazione.

Implementazione C# delle azioni trovate nell'assembly System.Fabric.Testability.dll. Il modulo PowerShell Testability si trova nell'assembly Microsoft.ServiceFabric.Testability.Powershell.dll. Come parte dell'installazione del runtime, il modulo ServiceFabricTestability PowerShell viene installato per consentire un utilizzo semplice.

## Azioni di errore normali e anomale
Le azioni di Testabilità sono classificate in due bucket principali:

* Errori anomali: questi errori simulano errori come il riavvio del computer e gli arresti anomali dei processi. In caso di errori di questo tipo, il contesto di esecuzione del processo si interrompe in modo brusco. Ciò significa che non è possibile eseguire alcuna pulizia dello stato prima che l'applicazione venga avviata nuovamente.

* Errori normali: questi errori simulano azioni normali come gli spostamenti delle repliche e le eliminazioni attivate dal bilanciamento del carico. In questi casi, il servizio riceve la notifica dello stato di chiusura e può eseguire la pulizia dello stato prima di uscire.

Per una convalida migliore in termini di qualità, eseguire il carico di lavoro del servizio e del business, includendo diversi errori normali e anomali. Gli errori anomali danno luogo a scenari in cui il processo di servizio si chiude improvvisamente nel corso di un flusso di lavoro. In tal modo viene verificato il percorso di ripristino dopo che la replica del servizio viene ripristinata in Infrastruttura di servizi. Ciò consentirà di verificare la coerenza dei dati e se lo stato del servizio viene conservato correttamente dopo gli errori. L'altro set di errori, vale a dire gli errori normali verificano che il servizio risponda correttamente alle repliche che vengono spostate da Infrastruttura di servizi. Ciò consente di verificare la gestione dell'annullamento nel metodo RunAsync. Il servizio deve controllare che il token Cancellation sia impostato, salvarne correttamente lo stato e chiudere il metodo RunAsync.

## Elenco delle azioni di Testabilità

| Actions | Descrizione | API gestita | Cmdlet di PowerShell | Errori normali/anomali |
|---------|-------------|-------------|-------------------|------------------------------|
|CleanTestState| Rimuove lo stato di tutti i test dal cluster in caso di arresto anomalo del driver di test. | CleanTestStateAsync | Remove-ServiceFabricTestState | Non applicabile |
| InvokeDataLoss | Provoca la perdita di dati in una partizione del servizio. | InvokeDataLossAsync | Invoke-ServiceFabricPartitionDataLoss | Normale |
| InvokeQuorumLoss | Inserisce una partizione del servizio con stato data in una perdita di quorum. | InvokeQuorumLossAsync | Invoke-ServiceFabricQuorumLoss | Normale |
| Move Primary | Sposta la replica primaria specificata del servizio con stato al nodo cluster specificato. | MovePrimaryAsync | Move-ServiceFabricPrimaryReplica | Normale |
| Move Secondary | Sposta la replica secondaria corrente di un servizio con stato a un nodo di cluster diverso. | MoveSecondaryAsync | Move-ServiceFabricSecondaryReplica | Normale |
| RemoveReplica | Simula un errore di replica tramite la rimozione di una replica da un cluster. In tal modo la replica verrà chiusa e passata al ruolo 'None', rimuovendo tutto lo stato dal cluster. | RemoveReplicaAsync | Remove-ServiceFabricReplica | Normale |
| RestartDeployedCodePackage | Simula un errore di processo del pacchetto di codice mediante il riavvio di un pacchetto di codice distribuito in un nodo in un cluster. In tal modo il processo del pacchetto di codice verrà interrotto, e di conseguenza verranno riavviate tutte le repliche del servizio utente in hosting in tale processo. | RestartDeployedCodePackageAsync | Restart-ServiceFabricDeployedCodePackage | Anomalo |
| RestartNode | Simula un errore del nodo cluster Infrastruttura di servizi tramite il riavvio di un nodo. | RestartNodeAsync | Restart-ServiceFabricNode | Anomalo |
| RestartPartition | Simula uno scenario di blackout del datacenter o del cluster mediante il riavvio di alcune o tutte le repliche di una partizione. | RestartPartitionAsync | Restart-ServiceFabricPartition | Normale |
| RestartReplica | Simula un errore di replica mediante il riavvio di una replica persistente in un cluster, la chiusura della replica e quindi la riapertura. | RestartReplicaAsync | Restart-ServiceFabricReplica | Normale |
| StartNode | Avvia un nodo in un cluster già stato arrestato. | StartNodeAsync | Start-ServiceFabricNode | Non applicabile |
| StopNode | Simula l’errore in un nodo mediante l’arresto di un nodo in un cluster. Il nodo resterà inattivo fino a quando non viene chiamato StartNode. | StopNodeAsync | Stop-ServiceFabricNode | Anomalo |
| ValidateApplication | Convalida la disponibilità e l’integrità di tutti i servizi Infrastruttura di servizi all’interno dell’applicazione, in genere dopo aver causato un errore nel sistema. | ValidateApplicationAsync | Test-ServiceFabricApplication | Non applicabile |
| ValidateService | Convalida la disponibilità e l’integrità di un servizio Infrastruttura di servizi, in genere dopo aver causato un errore nel sistema. | ValidateServiceAsync | Test-ServiceFabricService | Non applicabile |

## Esecuzione di un'azione di Testabilità con Powershell

In questa esercitazione viene illustrato come eseguire un'azione di Testabilità con PowerShell. Si apprenderà come eseguire un'azione Testabilità in un cluster locale (aka. una casella) o in un cluster Azure. Microsoft.Fabric.Testability.Powershell.dll , il modulo di Testabilità PowerShell, viene installato automaticamente quando si installa Microsoft Service Fabric MSI. Il modulo viene installato automaticamente quando si apre un prompt PowerShell.

Sezioni dell'esercitazione:

- [Eseguire un'azione su un cluster di una casella](#run-an-action-against-a-one-box-cluster)
- [Eseguire un'azione su un cluster di Azure](#run-an-action-against-an-azure-cluster)

### Eseguire un'azione su un cluster di una casella

Per eseguire un'azione di Testabilità su un cluster locale, è necessario innanzitutto connettersi al cluster e aprire il prompt di PowerShell in modalità amministratore. Esaminiamo l’azione **Restart-ServiceFabricNode**.

```powershell
Restart-ServiceFabricNode -NodeName Node1 -CompletionMode DoNotVerify
```

Qui l’azione **Restart-ServiceFabricNode** viene eseguita in un nodo denominato "Node1" e la modalità di completamento specifica che non deve essere verificato se l’azione di riavvio abbia effettivamente avuto esito positivo. Specificando la modalità di completamento come "Verify" verrà verificato se l’azione di riavvio abbia effettivamente avuto esito positivo. Anziché specificare direttamente il nodo mediante il relativo nome, è possibile specificarlo tramite una chiave di partizione e il tipo di replica, in come indicato di seguito:

```powershell
Restart-ServiceFabricNode -ReplicaKindPrimary  -PartitionKindNamed -PartitionKey Partition3 -CompletionMode Verify
```


```powershell
$connection = "localhost:19000"
$nodeName = "Node1"

Connect-ServiceFabricCluster $connection
Restart-ServiceFabricNode -NodeName $nodeName -CompletionMode DoNotVerify
```

**Restart-ServiceFabricNode** deve essere utilizzata per riavviare un nodo Infrastruttura di servizi in un cluster. In tal modo, il processo Fabric.exe verrà ignorato e tutte le repliche del servizio di sistema e del servizio utente in hosting in quel nodo verranno riavviate. L’uso di questa API per il test del servizio consente di rilevare bug lungo i percorsi di ripristino del failover. Consente di simulare gli errori dei nodi nel cluster.

Nella seguente schermata viene mostrato il comando Testability **Restart-ServiceFabricNode** in azione.

![](media/service-fabric-testability-actions/Restart-ServiceFabricNode.png)

Nell'output del primo *Get ServiceFabricNode* (un cmdlet dal modulo PowerShell ServiceFabric) viene illustrato che il cluster locale dispone di cinque nodi: da Node.1 a Node.5; quindi dopo l'esecuzione dell'azione di Testabilità (cmdlet) **Restart-ServiceFabricNode** sul nodo, denominato Node.4, noteremo che è il tempo di attività del nodo è stato reimpostato.

### Eseguire un'azione su un cluster di Azure

L’esecuzione di un'azione di Testabilità (con PowerShell) su un cluster di Azure è simile all'esecuzione dell'azione su un cluster locale. L’unica differenza è che prima di eseguire l'azione, invece di connettersi al cluster locale, è necessario connettersi innanzitutto al cluster di Azure.

## Esecuzione di un'azione di Testabilità con C# 

Per eseguire un'azione di Testabilità con C#, è necessario connettersi al cluster tramite FabricClient. Ottenere quindi i parametri necessari per eseguire l'azione. Per eseguire la stessa azione possono essere utilizzati parametri diversi. Esaminando l'azione RestartServiceFabricNode, un modo per eseguirla consiste nell’utilizzare le informazioni relative al nodo (nodo nome e ID istanza del nodo) nel cluster.

```csharp
RestartNodeAsync(nodeName, nodeInstanceId, completeMode, operationTimeout, CancellationToken.None)
```

Spiegazione di alcuni parametri:

**CompleteMode**: la modalità di completamento specifica che non deve essere verificato se l’azione di riavvio abbia effettivamente avuto esito positivo. Specificando la modalità di completamento come "Verify" verrà verificato se l’azione di riavvio abbia effettivamente avuto esito positivo. **OperationTimeout**: imposta la quantità di tempo per completare l’operazione prima che venga generata un'eccezione TimeoutException. **CancellationToken**: consente di annullare una chiamata in sospeso.

Anziché specificare direttamente il nodo mediante il relativo nome, è possibile specificarlo tramite una chiave di partizione e il tipo di replica.

Per ulteriori informazioni vedere [Selettore di partizione e selettore di replica](#partition_replica_selector).


```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll.
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Fabric.Testability;
using System.Fabric;
using System.Threading;
using System.Numerics;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");
        string nodeName = "N0040";
        BigInteger nodeInstanceId = 130743013389060139;

        Console.WriteLine("Starting RestartNode test");
        try
        {
            //RestartNode using the replicaSelector
            RestartNodeAsync(clusterConnection, serviceName).Wait();

            //Another way to Restart Node using Nodename and NodeInstanceID.
            RestartNodeAsync(clusterConnection, nodeName, nodeInstanceId).Wait();
        }
        catch (AggregateException exAgg)
        {
            Console.WriteLine("RestartNode did not complete: ");
            foreach (Exception ex in exAgg.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("RestartNode completed.");
        return 0;
    }

    static async Task RestartNodeAsync(string clusterConnection, Uri serviceName)
    {
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);
        ReplicaSelector primaryofReplicaSelector = ReplicaSelector.PrimaryOf(randomPartitionSelector);

        // Create FabricClient with connection & security information here.
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.ClusterManager.RestartNodeAsync(primaryofReplicaSelector, CompletionMode.Verify);
    }

    static async Task RestartNodeAsync(string clusterConnection, string nodeName, BigInteger nodeInstanceId)
    {
        // Create FabricClient with connection & security information here.
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.ClusterManager.RestartNodeAsync(nodeName, nodeInstanceId, CompletionMode.Verify);
    }
}
```

## Selettore di partizione e selettore di Replica

### Selettore di partizione
PartitionSelector è un helper esposto in Testabilità e viene utilizzato per selezionare una partizione specifica in cui eseguire una qualsiasi azione di Testabilità. Può essere utilizzato per selezionare una partizione specifica se l'ID partizione è noto in anticipo. In alternativa, è possibile fornire la chiave di partizione; l'operazione risolverà internamente l'ID partizione. È inoltre possibile selezionare una partizione casuale.

A tal fine, creare l'oggetto PartitionSelector e selezionare la partizione utilizzando uno dei metodi Select *, quindi passare l'oggetto PartitionSelector all'API che lo richiede. Se non è selezionata alcuna opzione, viene definita la partizione casuale.

```csharp
Uri serviceName = new Uri("fabric:/samples/InMemoryToDoListApp/InMemoryToDoListService");
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
string partitionName = "Partition1";
Int64 partitionKeyUniformInt64 = 1;

// Select Random partition
PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

// Select partition based on Id
PartitionSelector partitionSelectorById = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);

// Select partition based on name
PartitionSelector namedPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionName);

// Select partition based on partition key
PartitionSelector uniformIntPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionKeyUniformInt64);
```

### Selettore di replica
PartitionSelector è un helper esposto in Testabilità e viene utilizzato per aiutare a selezionare una replica in cui eseguire una qualsiasi azione di Testabilità. Può essere utilizzato per selezionare una replica specifica se l'ID replica è noto in anticipo. Inoltre, è possibile selezionare anche una replica primaria o secondaria casuale. ReplicaSelector deriva da PartitionSelector, pertanto è necessario selezionare sia la replica che la partizione in cui si desidera eseguire l'operazione Testabilità.

A tal fine, creare un oggetto ReplicaSelector e impostare il modo in cui si desidera selezionare la replica e la partizione. Quindi è possibile passarlo nell'API che lo richiede. Se non è selezionata alcuna opzione, per impostazione predefinita vengono selezionate la replica casuale e la partizione casuale.

Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829"); PartitionSelector partitionSelector = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid); long replicaId = 130559876481875498;


```csharp
// Select Random replica
ReplicaSelector randomReplicaSelector = ReplicaSelector.RandomOf(partitionSelector);

// Select primary replica
ReplicaSelector primaryReplicaSelector = ReplicaSelector.PrimaryOf(partitionSelector);

// Select replica by Id
ReplicaSelector replicaByIdSelector = ReplicaSelector.ReplicaIdOf(partitionSelector, replicaId);

// Select random secondary replica
ReplicaSelector secondaryReplicaSelector = ReplicaSelector.RandomSecondaryOf(partitionSelector);
```

## Passaggi successivi

- [Scenari di testabilità](service-fabric-testability-scenarios.md)
- Come eseguire il test del servizio
   - [Simulare gli errori durante i carichi di lavoro del servizio](service-fabric-testability-workload-tests.md)
   - [Errori di comunicazione da servizio a servizio](service-fabric-testability-scenarios-service-communication.md)
 

<!---HONumber=September15_HO1-->