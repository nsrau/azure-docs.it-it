---
title: Scenari di test personalizzati | Microsoft Docs
description: Come proteggere i servizi in caso di errori normali e anomali
services: service-fabric
documentationcenter: .net
author: anmolah
manager: timlt
editor: ''

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/17/2016
ms.author: anmola

---
# Simulare gli errori durante i carichi di lavoro del servizio
Gli scenari di testabilità in Service Fabric di Azure consentono agli sviluppatori di non preoccuparsi della gestione dei singoli errori. Tuttavia, sono disponibili scenari in cui potrebbe essere necessaria un'interfoliazione esplicita del carico di lavoro client e degli errori. L'interfoliazione del carico di lavoro client e degli errori garantisce che il servizio stia effettivamente eseguendo un’azione quando si verifica un errore. Dato il livello di controllo fornito dalla testabilità, questi errori potrebbero verificarsi in momenti precisi dell'esecuzione del carico di lavoro. L’induzione degli errori in stati diversi nell'applicazione può consentire di individuare bug e migliorare la qualità.

## Esempio di scenario personalizzato
In questo test viene illustrato uno scenario in cui si verifica l'interfoliazione del carico di lavoro di business con [errori normali e anomali](service-fabric-testability-actions.md#graceful-vs-ungraceful-fault-actions). Per ottenere risultati ottimali, gli errori devono essere indotti nel corso delle operazioni del servizio o del calcolo.

Esaminiamo in dettaglio un esempio di servizio che espone quattro carichi di lavoro: A, B, C e D. Ognuno corrisponde a un insieme di flussi di lavoro e potrebbe essere calcolo, archiviazione o una combinazione. Per ragioni di semplicità, nel nostro esempio verranno estrapolati i carichi di lavoro. I diversi errori eseguiti in questo esempio sono:

* RestartNode: errore anomalo per simulare un riavvio del computer.
* RestartDeployedCodePackage: errore anomalo per simulare arresti anomali del processo host del servizio.
* RemoveReplica: errore normale per simulare la rimozione della replica.
* MovePrimary: errore normale per simulare gli spostamenti della replica attivati dal servizio di bilanciamento del carico di Service Fabric.

```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll.

using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        // Replace these strings with the actual version for your cluster and application.
        string clusterConnection = "localhost:19000";
        Uri applicationName = new Uri("fabric:/samples/PersistentToDoListApp");
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");

        Console.WriteLine("Starting Workload Test...");
        try
        {
            RunTestAsync(clusterConnection, applicationName, serviceName).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Workload Test failed: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Workload Test completed successfully.");
        return 0;
    }

    public enum ServiceWorkloads
    {
        A,
        B,
        C,
        D
    }

    public enum ServiceFabricFaults
    {
        RestartNode,
        RestartCodePackage,
        RemoveReplica,
        MovePrimary,
    }

    public static async Task RunTestAsync(string clusterConnection, Uri applicationName, Uri serviceName)
    {
        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);
        // Maximum time to wait for a service to stabilize.
        TimeSpan maxServiceStabilizationTime = TimeSpan.FromSeconds(120);

        // How many loops of faults you want to execute.
        uint testLoopCount = 20;
        Random random = new Random();

        for (var i = 0; i < testLoopCount; ++i)
        {
            var workload = SelectRandomValue<ServiceWorkloads>(random);
            // Start the workload.
            var workloadTask = RunWorkloadAsync(workload);

            // While the task is running, induce faults into the service. They can be ungraceful faults like
            // RestartNode and RestartDeployedCodePackage or graceful faults like RemoveReplica or MovePrimary.
            var fault = SelectRandomValue<ServiceFabricFaults>(random);

            // Create a replica selector, which will select a primary replica from the given service to test.
            var replicaSelector = ReplicaSelector.PrimaryOf(PartitionSelector.RandomOf(serviceName));
            // Run the selected random fault.
            await RunFaultAsync(applicationName, fault, replicaSelector, fabricClient);
            // Validate the health and stability of the service.
            await fabricClient.ServiceManager.ValidateServiceAsync(serviceName, maxServiceStabilizationTime);

            // Wait for the workload to finish successfully.
            await workloadTask;
        }
    }

    private static async Task RunFaultAsync(Uri applicationName, ServiceFabricFaults fault, ReplicaSelector selector, FabricClient client)
    {
        switch (fault)
        {
            case ServiceFabricFaults.RestartNode:
                await client.ClusterManager.RestartNodeAsync(selector, CompletionMode.Verify);
                break;
            case ServiceFabricFaults.RestartCodePackage:
                await client.ApplicationManager.RestartDeployedCodePackageAsync(applicationName, selector, CompletionMode.Verify);
                break;
            case ServiceFabricFaults.RemoveReplica:
                await client.ServiceManager.RemoveReplicaAsync(selector, CompletionMode.Verify, false);
                break;
            case ServiceFabricFaults.MovePrimary:
                await client.ServiceManager.MovePrimaryAsync(selector.PartitionSelector);
                break;
        }
    }

    private static Task RunWorkloadAsync(ServiceWorkloads workload)
    {
        throw new NotImplementedException();
        // This is where you trigger and complete your service workload.
        // Note that the faults induced while your service workload is running will
        // fault the primary service. Hence, you will need to reconnect to complete or check
        // the status of the workload.
    }

    private static T SelectRandomValue<T>(Random random)
    {
        Array values = Enum.GetValues(typeof(T));
        T workload = (T)values.GetValue(random.Next(values.Length));
        return workload;
    }
}
```

<!---HONumber=AcomDC_0518_2016-->