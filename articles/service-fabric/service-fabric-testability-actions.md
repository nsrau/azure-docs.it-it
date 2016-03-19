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
   ms.date="12/04/2015"
   ms.author="heeldin;motanv"/>

# Azioni di Testabilità
Per simulare un'infrastruttura non affidabile, Azure Service Fabric offre agli sviluppatori la possibilità di simulare errori e transizioni di stato del mondo reale, esposti come azioni di testabilità. Le azioni sono le API di basso livello che causano una specifica fault injection, una transizione di stato o una convalida. La combinazione di queste azioni consente agli sviluppatori di scrivere scenari di test completi per i servizi.

Service Fabric fornisce alcuni scenari di test comuni costituiti da queste azioni. È consigliabile usare questi scenari predefiniti, poiché vengono scelti con attenzione per testare le transizioni di stato e i casi di errore comuni. È comunque possibile creare anche scenari di test personalizzati, costituiti dalle stesse azioni, quando si vuole aggiungere la copertura per scenari specifici di un'applicazione o non ancora presenti negli scenari integrati.

Implementazioni in C# delle azioni sono disponibili nell'assembly System.Fabric.Testability.dll. Il modulo PowerShell Testability si trova nell'assembly Microsoft.ServiceFabric.Testability.Powershell.dll. Nell'ambito dell'installazione del runtime viene installato il modulo ServiceFabricTestability di PowerShell per garantire una maggiore semplicità d'uso.

## Azioni di errore normali e anomale
Le azioni di Testabilità sono classificate in due bucket principali:

* Errori anomali: questi errori simulano errori come il riavvio del computer e gli arresti anomali dei processi. In caso di errori di questo tipo, il contesto di esecuzione del processo si interrompe in modo brusco. Non è quindi possibile eseguire alcuna pulizia dello stato prima che l'applicazione venga avviata nuovamente.

* Errori normali: questi errori simulano azioni normali come gli spostamenti delle repliche e le eliminazioni attivate dal bilanciamento del carico. In questi casi, il servizio riceve una notifica dello stato di chiusura e può eseguire la pulizia dello stato prima di uscire.

Per una convalida migliore in termini di qualità, eseguire il carico di lavoro del servizio e del business, includendo diversi errori normali e anomali. Gli errori anomali danno luogo a scenari in cui il processo di servizio si chiude improvvisamente nel corso di un flusso di lavoro. In tal modo viene verificato il percorso di ripristino dopo che la replica del servizio viene ripristinata in Infrastruttura di servizi. Ciò consentirà di verificare la coerenza dei dati e se lo stato del servizio viene conservato correttamente dopo gli errori. L'altro set di errori, ovvero gli errori normali, verifica che il servizio risponda correttamente alle repliche spostate da Service Fabric. Ciò consente di verificare la gestione dell'annullamento nel metodo RunAsync. Il servizio deve controllare che il token di annullamento sia impostato, salvarne correttamente lo stato e chiudere il metodo RunAsync.

## Elenco delle azioni di Testabilità

| Azione | Descrizione | API gestita | Cmdlet di PowerShell | Errori normali/anomali |
|---------|-------------|-------------|-------------------|------------------------------|
|CleanTestState| Rimuove lo stato di tutti i test dal cluster in caso di arresto anomalo del driver di test. | CleanTestStateAsync | Remove-ServiceFabricTestState | Non applicabile |
| InvokeDataLoss | Provoca la perdita di dati in una partizione del servizio. | InvokeDataLossAsync | Invoke-ServiceFabricPartitionDataLoss | Normale |
| InvokeQuorumLoss | Inserisce una partizione del servizio con stato in una perdita di quorum. | InvokeQuorumLossAsync | Invoke-ServiceFabricQuorumLoss | Normale |
| Move Primary | Sposta la replica primaria specificata di un servizio con stato nel nodo cluster specificato. | MovePrimaryAsync | Move-ServiceFabricPrimaryReplica | Normale |
| Move Secondary | Sposta la replica secondaria corrente di un servizio con stato a un nodo di cluster diverso. | MoveSecondaryAsync | Move-ServiceFabricSecondaryReplica | Normale |
| RemoveReplica | Simula un errore di replica tramite la rimozione di una replica da un cluster. In tal modo la replica verrà chiusa e passata al ruolo 'None', rimuovendo tutto lo stato dal cluster. | RemoveReplicaAsync | Remove-ServiceFabricReplica | Normale |
| RestartDeployedCodePackage | Simula un errore di processo del pacchetto di codice mediante il riavvio di un pacchetto di codice distribuito in un nodo in un cluster. In questo modo, il processo del pacchetto di codice viene interrotto e verranno riavviate tutte le repliche del servizio utente ospitate nel processo. | RestartDeployedCodePackageAsync | Restart-ServiceFabricDeployedCodePackage | Anomalo |
| RestartNode | Simula un errore del nodo cluster Infrastruttura di servizi tramite il riavvio di un nodo. | RestartNodeAsync | Restart-ServiceFabricNode | Anomalo |
| RestartPartition | Simula uno scenario di blackout del data center o del cluster mediante il riavvio di alcune o di tutte le repliche di una partizione. | RestartPartitionAsync | Restart-ServiceFabricPartition | Normale |
| RestartReplica | Simula un errore di replica mediante il riavvio di una replica persistente in un cluster, la chiusura della replica e quindi la riapertura. | RestartReplicaAsync | Restart-ServiceFabricReplica | Normale |
| StartNode | Avvia un nodo in un cluster già arrestato. | StartNodeAsync | Start-ServiceFabricNode | Non applicabile |
| StopNode | Simula l’errore in un nodo mediante l’arresto di un nodo in un cluster. Il nodo resterà inattivo fino a quando non viene chiamato StartNode. | StopNodeAsync | Stop-ServiceFabricNode | Anomalo |
| ValidateApplication | Convalida la disponibilità e l’integrità di tutti i servizi Infrastruttura di servizi all’interno dell’applicazione, in genere dopo aver causato un errore nel sistema. | ValidateApplicationAsync | Test-ServiceFabricApplication | Non applicabile |
| ValidateService | Convalida la disponibilità e l’integrità di un servizio Infrastruttura di servizi, in genere dopo aver causato un errore nel sistema. | ValidateServiceAsync | Test-ServiceFabricService | Non applicabile |

## Esecuzione di un'azione di testabilità con PowerShell

Questa esercitazione illustra come eseguire un'azione di testabilità con PowerShell. Si apprenderà come eseguire un'azione di testabilità in un cluster locale (di una casella) o in un cluster di Azure. Microsoft.Fabric.Testability.Powershell.dll, il modulo di testabilità di PowerShell, viene installato automaticamente quando si installa l'MSI di Microsoft Service Fabric e caricato automaticamente quando si apre un prompt di PowerShell.

Sezioni dell'esercitazione:

- [Eseguire un'azione su un cluster di una casella](#run-an-action-against-a-one-box-cluster)
- [Eseguire un'azione su un cluster di Azure](#run-an-action-against-an-azure-cluster)

### Eseguire un'azione su un cluster di una casella

Per eseguire un'azione di testabilità su un cluster locale, è necessario in primo luogo connettersi al cluster e aprire il prompt di PowerShell in modalità amministratore. Esaminiamo l’azione **Restart-ServiceFabricNode**.

```powershell
Restart-ServiceFabricNode -NodeName Node1 -CompletionMode DoNotVerify
```

In questo caso, l'azione **Restart-ServiceFabricNode** è in esecuzione su un nodo denominato "Node1" e la modalità di completamento specifica che l'esito positivo dell'azione di riavvio non verrà verificato. Per verificare l'esito positivo dell'azione di riavvio, è necessario specificare la modalità di completamento come "Verify". Anziché specificare direttamente il nodo mediante il nome, è possibile specificarlo tramite una chiave di partizione e il tipo di replica, come indicato di seguito:

```powershell
Restart-ServiceFabricNode -ReplicaKindPrimary  -PartitionKindNamed -PartitionKey Partition3 -CompletionMode Verify
```


```powershell
$connection = "localhost:19000"
$nodeName = "Node1"

Connect-ServiceFabricCluster $connection
Restart-ServiceFabricNode -NodeName $nodeName -CompletionMode DoNotVerify
```

**Restart-ServiceFabricNode** deve essere utilizzata per riavviare un nodo Infrastruttura di servizi in un cluster. In questo modo, il processo Fabric.exe verrà interrotto e tutte le repliche del servizio di sistema e del servizio utente ospitate in quel nodo verranno riavviate. L’uso di questa API per il test del servizio consente di rilevare bug lungo i percorsi di ripristino del failover. Consente di simulare gli errori dei nodi nel cluster.

La schermata seguente mostra il comando di testabilità **Restart-ServiceFabricNode** in azione.

![](media/service-fabric-testability-actions/Restart-ServiceFabricNode.png)

L'output del primo **Get ServiceFabricNode** (un cmdlet dal modulo PowerShell di ServiceFabric) mostra che il cluster locale ha cinque nodi: da Node.1 a Node.5. Dopo l'esecuzione dell'azione di testabilità (cmdlet) **Restart-ServiceFabricNode** sul nodo, denominato Node.4, noteremo che il tempo di attività del nodo è stato reimpostato.

### Eseguire un'azione su un cluster di Azure

L'esecuzione di un'azione di testabilità (con PowerShell) su un cluster di Azure è simile all'esecuzione della stessa azione su un cluster locale. L'unica differenza è che, prima di poter eseguire l'azione, invece di connettersi al cluster locale, è necessario connettersi al cluster di Azure.

## Esecuzione di un'azione di testabilità con C#

Per eseguire un'azione di testabilità con C#, è necessario prima connettersi al cluster tramite FabricClient. Ottenere quindi i parametri necessari per eseguire l'azione. Per eseguire la stessa azione possono essere utilizzati parametri diversi. In merito all'azione RestartServiceFabricNode, per eseguirla è possibile usare le informazioni sul nodo (nodo del nome e ID dell'istanza del nodo) disponibili nel cluster.

```csharp
RestartNodeAsync(nodeName, nodeInstanceId, completeMode, operationTimeout, CancellationToken.None)
```

Spiegazione di alcuni parametri:

- **CompleteMode** specifica che l'esito positivo dell'azione di riavvio non verrà verificato. Per verificare l'esito positivo dell'azione di riavvio, è necessario specificare la modalità di completamento come "Verify".  
- **OperationTimeout**: imposta la quantità di tempo disponibile per completare l'operazione prima che venga generata un'eccezione TimeoutException.
- **CancellationToken**: consente di annullare una chiamata in sospeso.

Anziché specificare direttamente il nodo mediante il nome, è possibile specificarlo tramite una chiave di partizione e il tipo di replica:

Per altre informazioni vedere [PartitionSelector e ReplicaSelector](#partition_replica_selector).


```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll
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
            //Restart the node by using ReplicaSelector
            RestartNodeAsync(clusterConnection, serviceName).Wait();

            //Another way to restart node is by using nodeName and nodeInstanceId
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

        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.ClusterManager.RestartNodeAsync(primaryofReplicaSelector, CompletionMode.Verify);
    }

    static async Task RestartNodeAsync(string clusterConnection, string nodeName, BigInteger nodeInstanceId)
    {
        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.ClusterManager.RestartNodeAsync(nodeName, nodeInstanceId, CompletionMode.Verify);
    }
}
```

## PartitionSelector e ReplicaSelector

### PartitionSelector
PartitionSelector è un helper esposto in fase di testabilità che consente di selezionare una partizione specifica in cui eseguire le azioni di testabilità. Può essere utilizzato per selezionare una partizione specifica se l'ID partizione è noto in anticipo. In alternativa, è possibile fornire la chiave di partizione; l'operazione risolverà internamente l'ID partizione. È inoltre possibile selezionare una partizione casuale.

Per usare questo helper, creare l'oggetto PartitionSelector e selezionare la partizione ricorrendo a uno dei metodi Select*. Passare quindi l'oggetto PartitionSelector all'API che lo richiede. Se non è selezionata alcuna opzione, per impostazione predefinita viene selezionata una partizione casuale.

```csharp
Uri serviceName = new Uri("fabric:/samples/InMemoryToDoListApp/InMemoryToDoListService");
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
string partitionName = "Partition1";
Int64 partitionKeyUniformInt64 = 1;

// Select a random partition
PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

// Select a partition based on ID
PartitionSelector partitionSelectorById = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);

// Select a partition based on name
PartitionSelector namedPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionName);

// Select a partition based on partition key
PartitionSelector uniformIntPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionKeyUniformInt64);
```

### ReplicaSelector
ReplicaSelector è un helper esposto in fase di testabilità che consente di selezionare una replica in cui eseguire le azioni di testabilità. Può essere usato per selezionare una replica specifica se l'ID di replica è noto in anticipo. È possibile selezionare anche una replica primaria o una replica secondaria casuale. ReplicaSelector deriva da PartitionSelector ed è quindi necessario selezionare sia la replica sia la partizione in cui si vuole eseguire l'operazione di testabilità.

Per usare l'helper, creare un oggetto ReplicaSelector e impostare il modo in cui si vuole selezionare la replica e la partizione. Quindi è possibile passarlo nell'API che lo richiede. Se non è selezionata alcuna opzione, per impostazione predefinita vengono selezionate una replica casuale e una partizione casuale.

Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829"); PartitionSelector partitionSelector = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid); long replicaId = 130559876481875498;


```csharp
// Select a random replica
ReplicaSelector randomReplicaSelector = ReplicaSelector.RandomOf(partitionSelector);

// Select the primary replica
ReplicaSelector primaryReplicaSelector = ReplicaSelector.PrimaryOf(partitionSelector);

// Select the replica by ID
ReplicaSelector replicaByIdSelector = ReplicaSelector.ReplicaIdOf(partitionSelector, replicaId);

// Select a random secondary replica
ReplicaSelector secondaryReplicaSelector = ReplicaSelector.RandomSecondaryOf(partitionSelector);
```

## Passaggi successivi

- [Scenari di testabilità](service-fabric-testability-scenarios.md)
- Come eseguire il test del servizio
   - [Simulare gli errori durante i carichi di lavoro del servizio](service-fabric-testability-workload-tests.md)
   - [Errori di comunicazione da servizio a servizio](service-fabric-testability-scenarios-service-communication.md)

<!---HONumber=AcomDC_1223_2015-->