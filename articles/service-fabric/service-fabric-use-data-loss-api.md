---
title: Come richiamare una perdita di dati sui servizi Service Fabric | Microsoft Docs
description: Descrive come usare l'API relativa alla perdita di dati
services: service-fabric
documentationcenter: .net
author: LMWF
manager: rsinha
editor: ''

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/19/2016
ms.author: lemai

---
# Come richiamare la perdita di dati nei servizi
> [!WARNING]
> Questo documento descrive come causare la perdita di dati nei servizi e la procedura deve essere usata con cautela.
> 
> 

## Introduzione
È possibile richiamare la perdita di dati in una partizione del servizio Service Fabric chiamando StartPartitionDataLossAsync(). L'API usa il servizio di fault injection e analisi per causare condizioni di perdita dei dati.

## Uso del servizio di fault injection e analisi
Il servizio di fault injection e analisi attualmente supporta le API indicate nel grafico seguente. Il lato destro del grafico mostra il corrispondente cmdlet di PowerShell. Per altre informazioni su queste API, vedere la documentazione relativa in MSDN.

| API C# | Cmdlet di PowerShell |
| --- | ---:|
| [StartPartitionDataLossAsync][dl] |[Start-ServiceFabricPartitionDataLoss][psdl] |
| [StartPartitionQuorumLossAsync][ql] |[Start-ServiceFabricPartitionQuorumLoss][psql] |
| [StartPartitionRestartAsync][rp] |[Start-ServiceFabricPartitionRestart][psrp] |

## Panoramica concettuale dell'esecuzione di un comando
Il servizio di fault injection e analisi usa un modello asincrono in cui si avvia il comando con un'API, chiamata API “Start” in questo documento, quindi si controlla l'avanzamento del comando mediante un'API “GetProgress” fino a quando il comando raggiunge uno stato terminale o fino a quando lo si annulla. Per avviare un comando, chiamare l'API "Start" per l'API corrispondente. Questa API ritorna quando il servizio di fault injection e analisi ha accettato la richiesta. Tuttavia non indica a che punto si trova l'esecuzione del comando e neppure se è già stato avviato. Per controllare l'avanzamento di un comando chiamare l'API "GetProgress" corrispondente all'API "Start" chiamata in precedenza. L'API "GetProgress" restituirà un oggetto che indica lo stato corrente del comando all'interno della sua proprietà State. Un comando viene eseguito all'infinito finché:

1. Viene completato correttamente. In questo caso, se si chiama "GetProgress" per esso, lo stato dell'oggetto di avanzamento sarà Completed.
2. Si verifica un errore irreversibile. In questo caso, se si chiama "GetProgress" per esso, lo stato dell'oggetto di avanzamento sarà Faulted
3. Lo si annulla mediante l'API [CancelTestCommandAsync][cancel] o il cmdlet di PowerShell [Stop-ServiceFabricTestCommand][cancelps]. In questo caso, se si chiama “GetProgress” per esso, lo stato dell'oggetto di avanzamento sarà Cancelled o ForceCancelled, a seconda dell'argomento dell'API. Per altri dettagli, vedere la documentazione relativa a [CancelTestCommandAsync][cancel].

## Dettagli dell'esecuzione di un comando
Per avviare un comando, chiamare l'API Start con gli argomenti previsti. Tutte le API Start dispongono di un argomento Guid denominato operationId. È necessario tenere traccia dell'argomento operationId, poiché viene usato per monitorare l'avanzamento di questo comando. Deve essere passato nell'API "GetProgress" per tenere traccia dell'avanzamento del comando. L'operationId deve essere univoco.

Dopo l'esito positivo della chiamata dell'API Start, deve essere chiamata l'API GetProgress in un ciclo fino a quando la proprietà State dell'oggetto di avanzamento è Completed. È necessario tentare di nuovo tutti i [FabricTransientException][fte] e OperationCanceledException. Quando il comando ha raggiunto uno stato finale (Completed, Faulted o Cancelled), la proprietà Result dell'oggetto di avanzamento restituito conterrà informazioni aggiuntive. Se lo stato è Completed, Result.SelectedPartition.PartitionId conterrà l'ID partizione selezionato. Result.Exception sarà null. Se lo stato è Faulted, Result.Exception conterrà la ragione per cui il servizio di fault injection e analisi ha generato un errore nel comando. Result.SelectedPartition.PartitionId conterrà l'ID partizione selezionato. In alcuni casi il comando potrebbe non essere stato eseguito per un tempo sufficiente per la scelta di una partizione. In tal caso PartitionId sarà 0. Se lo stato è Cancelled, Result.Exception sarà null. Come nel caso di Faulted, Result.SelectedPartition.PartitionId conterrà l'ID partizione selezionato, ma se il comando non è stato eseguito per un tempo sufficiente per la scelta di una partizione, sarà 0. Vedere anche l'esempio seguente.

Il codice di esempio riportato di seguito mostra come avviare e poi controllare l'avanzamento di un comando per causare perdite di dati in una determinata partizione.

```csharp
    static async Task PerformDataLossSample()
    {
        // Create a unique operation id for the command below
        Guid operationId = Guid.NewGuid();

        // Note: Use the appropriate overload for your configuration
        FabricClient fabricClient = new FabricClient();

        // The name of the target service
        Uri targetServiceName = new Uri("fabric:/MyService");

        // The id of the target partition inside the target service
        Guid targetPartitionId = new Guid("00000000-0000-0000-0000-000002233445");

        PartitionSelector partitionSelector = PartitionSelector.PartitionIdOf(targetServiceName, targetPartitionId);

        // Start the command.  Retry OperationCanceledException and all FabricTransientException's.  Note when StartPartitionDataLossAsync completes
        // successfully it only means the Fault Injection and Analysis Service has saved the intent to perform this work.  It does not say anything about the progress
        // of the command.
        while (true)
        {
            try
            {
                await fabricClient.TestManager.StartPartitionDataLossAsync(operationId, partitionSelector, DataLossMode.FullDataLoss).ConfigureAwait(false);
                break;
            }
            catch (OperationCanceledException)
            {
            }
            catch (FabricTransientException)
            {
            }

            await Task.Delay(TimeSpan.FromSeconds(1.0d)).ConfigureAwait(false);
        }

        PartitionDataLossProgress progress = null;

        // Poll the progress using GetPartitionDataLossProgressAsync until it is either Completed or Faulted.  In this example, we're assuming
        // the command won't be cancelled.        

        while (true)
        {
            try
            {
                progress = await fabricClient.TestManager.GetPartitionDataLossProgressAsync(operationId).ConfigureAwait(false);
            }
            catch (OperationCanceledException)
            {
                continue;
            }
            catch (FabricTransientException)
            {
                continue;
            }

            if (progress.State == TestCommandProgressState.Completed)
            {
                Console.WriteLine("Command '{0}' completed successfully", operationId);

                // In a terminal state .Result.SelectedPartition.PartitionId will have the chosen partition
                Console.WriteLine("  Printing selected partition='{0}'", progress.Result.SelectedPartition.PartitionId);
                break;
            }
            else if (progress.State == TestCommandProgressState.Faulted)
            {
                // If State is Faulted, the progress object's Result property's Exception property will have the reason why.
                Console.WriteLine("Command '{0}' failed with '{1}'", operationId, progress.Result.Exception);
                break;
            }
            else
            {
                Console.WriteLine("Command '{0}' is currently Running", operationId);
            }

            await Task.Delay(TimeSpan.FromSeconds(5.0d)).ConfigureAwait(false);
        }
    }
```

L'esempio seguente illustra come usare PartitionSelector per scegliere una partizione casuale di un servizio specificato:

```csharp
    static async Task PerformDataLossUseSelectorSample()
    {
        // Create a unique operation id for the command below
        Guid operationId = Guid.NewGuid();

        // Note: Use the appropriate overload for your configuration
        FabricClient fabricClient = new FabricClient();

        // The name of the target service
        Uri targetServiceName = new Uri("fabric:/SampleService ");

        // Use a PartitionSelector that will have the Fault Injection and Analysis Service choose a random partition of “targetServiceName”
        PartitionSelector partitionSelector = PartitionSelector.RandomOf(targetServiceName);

        // Start the command.  Retry OperationCanceledException and all FabricTransientException's.  Note when StartPartitionDataLossAsync completes
        // successfully it only means the Fault Injection and Analysis Service has saved the intent to perform this work.  It does not say anything about the progress
        // of the command.
        while (true)
        {
            try
            {
                await fabricClient.TestManager.StartPartitionDataLossAsync(operationId, partitionSelector, DataLossMode.FullDataLoss).ConfigureAwait(false);
                break;
            }
            catch (OperationCanceledException)
            {
            }
            catch (FabricTransientException)
            {
            }
            catch (Exception e)
            {
                Console.WriteLine("Unexpected exception '{0}'", e);
                throw;
            }

            await Task.Delay(TimeSpan.FromSeconds(1.0d)).ConfigureAwait(false);
        }

        PartitionDataLossProgress progress = null;

        // Poll the progress using GetPartitionDataLossProgressAsync until it is either Completed or Faulted.  In this example, we're assuming
        // the command won't be cancelled.

        while (true)
        {
            try
            {
                progress = await fabricClient.TestManager.GetPartitionDataLossProgressAsync(operationId).ConfigureAwait(false);
            }
            catch (OperationCanceledException)
            {
                continue;
            }
            catch (FabricTransientException)
            {
                continue;
            }

            if (progress.State == TestCommandProgressState.Completed)
            {
                Console.WriteLine("Command '{0}' completed successfully", operationId);

                Console.WriteLine("Printing progress.Result:");
                Console.WriteLine("  Printing selected partition='{0}'", progress.Result.SelectedPartition.PartitionId);

                break;
            }
            else if (progress.State == TestCommandProgressState.Faulted)
            {
                // If State is Faulted, the progress object's Result property's Exception property will have the reason why.
                Console.WriteLine("Command '{0}' failed with '{1}', SelectedPartition {2}", operationId, progress.Result.Exception, progress.Result.SelectedPartition);
                break;
            }
            else
            {
                Console.WriteLine("Command '{0}' is currently Running", operationId);
            }

            await Task.Delay(TimeSpan.FromSeconds(1.0d)).ConfigureAwait(false);
        }
    }
```

## Cronologia e troncamento
Dopo che un comando ha raggiunto uno stato finale, i suoi metadati rimarranno nel servizio di fault injection e analisi per un certo periodo di tempo prima di essere rimossi per liberare spazio. Se si chiama “GetProgress” usando l'operationId di un comando dopo che è stato rimosso, restituirà una FabricException con l'ErrorCode KeyNotFound.

[dl]: https://msdn.microsoft.com/library/azure/mt693569.aspx
[ql]: https://msdn.microsoft.com/library/azure/mt693558.aspx
[rp]: https://msdn.microsoft.com/library/azure/mt645056.aspx
[psdl]: https://msdn.microsoft.com/library/mt697573.aspx
[psql]: https://msdn.microsoft.com/library/mt697557.aspx
[psrp]: https://msdn.microsoft.com/library/mt697560.aspx
[cancel]: https://msdn.microsoft.com/library/azure/mt668910.aspx
[cancelps]: https://msdn.microsoft.com/library/mt697566.aspx
[fte]: https://msdn.microsoft.com/library/azure/system.fabric.fabrictransientexception.aspx

<!---HONumber=AcomDC_0921_2016-->