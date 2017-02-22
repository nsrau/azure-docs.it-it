---
title: Avviare e arrestare nodi di cluster per testare microservizi di Azure | Documentazione Microsoft
description: Informazioni su come usare la tecnologia fault injection per testare un&quot;applicazione di Service Fabric avviando e arrestando nodi di cluster.
services: service-fabric
documentationcenter: .net
author: LMWF
manager: rsinha
editor: 
ms.assetid: f4e70f6f-cad9-4a3e-9655-009b4db09c6d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/19/2016
ms.author: lemai
translationtype: Human Translation
ms.sourcegitcommit: f7edee399717ecb96fb920d0a938da551101c9e1
ms.openlocfilehash: 5f76100007466f2baf4e067de963486f47fbf857


---

# <a name="replacing-the-start-node-and-stop-node-apis-with-the-node-transition-api"></a>Sostituzione dell'API di avvio del nodo e dell'API di arresto del nodo con l'API di transizione del nodo

## <a name="what-do-the-stop-node-and-start-node-apis-do"></a>Come funzionano le API di avvio del nodo e di arresto del nodo?

L'API di avvio del nodo (gestita: [StopNodeAsync()][stopnode], PowerShell: [Stop-ServiceFabricNode][stopnodeps]) arresta un nodo di Service Fabric.  Un nodo di Service Fabric è un processo, non una VM o macchina; la VM o la macchina sarà comunque in esecuzione.  Nel resto del documento, "nodo" indica il nodo di Service Fabric.  L'arresto di un nodo inserisce il nodo in uno stato *arrestato*, in cui non è un membro del cluster e non può ospitare servizi, simulando in questo modo un nodo *inattivo*.  Questa condizione è utile per l'inserimento di errori nel sistema per testare l'applicazione.  L'API di avvio del nodo (gestita: [StartNodeAsync()][startnode], PowerShell: [Start-ServiceFabricNode][startnodeps]]) inverte l'API di arresto del nodo riportando il nodo a uno stato normale.

## <a name="why-are-we-replacing-these"></a>Perché si sta procedendo alla relativa sostituzione?

Come descritto in precedenza, un nodo *arrestato* di Service Fabric è un nodo di destinazione scelto intenzionalmente usando l'API di arresto del nodo.  Un nodo *inattivo* è un nodo che non è attivo per qualsiasi altro motivo, ad esempio, la VM o la macchina è disattivata.  Con l'API di arresto del nodo, il sistema non espone le informazioni per distinguere i nodi *arrestati* e i nodi *inattivi*.

Inoltre, alcuni errori restituiti da queste API non offrono il massimo livello di dettaglio possibile.  Ad esempio, se si chiama l'API di arresto del nodo su un nodo già *arrestato* verrà restituito l'errore *InvalidAddress*.  Questa esperienza poteva essere migliorata.

Inoltre, la durata per cui un nodo viene arrestato è "infinita" finché non viene richiamata l'API di avvio del nodo.  Questa condizione può causare problemi ed essere soggetta a errori.  Ad esempio, si sono verificati problemi in cui un utente ha richiama l'API di avvio del nodo su un nodo e successivamente ha dimenticato di avere eseguito questa operazione.  In seguito non è stato chiaro se il nodo era *inattivo* o *arrestato*.


## <a name="introducing-the-node-transition-apis"></a>Introduzione alle API di transizione del nodo

Questi problemi sono stati risolti in un nuovo set di API.  La nuova API di transizione del nodo (gestito: [StartNodeTransitionAsync()][snt]) può essere usata per la transizione di un nodo di Service Fabric a uno stato *arrestato* o per eseguire la transizione da uno stato *arrestato* a uno stato attivo normale.  Si noti che "Start" nel nome dell'API non fa riferimento all'avvio di un nodo.  Fa riferimento all'avvio di un'operazione asincrona che verrà eseguita dal sistema per la transizione del nodo nello stato *arrestato* o avviato.

**Utilizzo**

Se l'API di transizione del nodo non genera un'eccezione quando viene richiamata, il sistema ha accettato l'operazione asincrona e la eseguirà.  Una chiamata eseguita correttamente non implica che l'operazione sia stata completata.  Per ottenere informazioni sullo stato corrente dell'operazione, chiamare l'API di avanzamento della transizione del nodo (gestito: [GetNodeTransitionProgressAsync()][gntp]) con il GUID usato per chiamare l'API di transizione di nodo per questa operazione.  L'API di avanzamento della transizione del nodo restituisce un oggetto NodeTransitionProgress.  La proprietà State dell'oggetto specifica lo stato corrente dell'operazione.  Se lo stato è "in esecuzione", l'operazione è in corso.  Se lo stato è completato, l'operazione è stata completata senza errori.  Se lo stato indica un errore, si è verificato un problema in fase di esecuzione.  La proprietà Exception della proprietà Result indicherà il problema riscontrato.  Per altre informazioni sulla proprietà State e la sezione "Esempio di utilizzo" per esempi di codice, vedere https://docs.microsoft.com/dotnet/api/system.fabric.testcommandprogressstate.


**Differenze tra un nodo arrestato e un nodo inattivo** Se un nodo è stato *arrestato* usando l'API di transizione del nodo, l'output di una query del nodo (gestito: [GetNodeListAsync()][nodequery], PowerShell: [Get-ServiceFabricNode][nodequeryps]) indicherà il valore true della proprietà *IsStopped* per questo nodo.  Si noti che questo valore è diverso dal valore della proprietà *NodeStatus* che indicherà lo stato *inattivo*.  Se la proprietà *NodeStatus* ha il valore *inattivo*, ma *IsStopped* restituisce false, il nodo non è stato arrestato usando l'API di transizione del nodo ed è *inattivo* per un altro motivo.  Se il valore della proprietà *IsStopped* è true e la proprietà *NodeStatus* indica lo stato *inattivo*, il nodo è stato arrestato usando l'API di transizione del nodo.

L'avvio di un nodo *arrestato* usando l'API di transizione del nodo ne ripristina il funzionamento come membro normale del cluster.  L'output dell'API di query del nodo indicherà *IsStopped* come false e *NodeStatus* indicherà un valore che non è inattivo, ad esempio, attivo.


**Durata limitata** quando si usa l'API di transizione di nodo per arrestare un nodo, uno dei parametri obbligatori, *stopNodeDurationInSeconds*, rappresenta il tempo in secondi per cui il nodo viene mantenuto *arrestato*.  Questo valore deve essere compreso nell'intervallo consentito, che include un minimo di 600 secondi e un massimo di 14.400 secondi.  Al termine di questo periodo, il nodo verrà riavviato automaticamente in uno stato attivo.  Fare riferimento all'esempio 1 di seguito per un esempio di utilizzo.

> [!WARNING]
> Non combinare le API di transizione del nodo e le API di avvio del nodo e di arresto del nodo.  È consigliabile usare solo l'API di transizione del nodo.  Se un nodo è già stato arrestato usando l'API di arresto del nodo, è necessario avviarlo usando l'API di avvio del nodo prima di usare le API di transizioni del nodo.

> [!WARNING]
> Non è possibile eseguire più chiamate API di transizione del nodo in parallelo nello stesso nodo.  In questo caso, l'API di transizione del nodo  genererà un FabricException con un valore NodeTransitionInProgress della proprietà ErrorCode.  Dopo avere avviato una transizione del nodo in un nodo specifico, è necessario attendere fino a quando l'operazione raggiunge uno stato finale (completato, con errore e annullamento forzato) prima di avviare una nuova transizione nello stesso nodo.  Sono consentite chiamate a transizioni del nodo in parallelo su nodi diversi.


#### <a name="sample-usage"></a>Esempio di utilizzo


**Esempio 1**: l'esempio seguente usa l'API di transizione del nodo per arrestare un nodo.

```csharp
        // Helper function to get information about a node
        static Node GetNodeInfo(FabricClient fc, string node)
        {
            NodeList n = null;
            while (n == null)
            {
                n = fc.QueryManager.GetNodeListAsync(node).GetAwaiter().GetResult();
                Task.Delay(TimeSpan.FromSeconds(1)).GetAwaiter();
            };

            return n.FirstOrDefault();
        }

        static async Task WaitForStateAsync(FabricClient fc, Guid operationId, TestCommandProgressState targetState)
        {
            NodeTransitionProgress progress = null;

            do
            {
                bool exceptionObserved = false;
                try
                {
                    progress = await fc.TestManager.GetNodeTransitionProgressAsync(operationId, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                    exceptionObserved = true;
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                    exceptionObserved = true;
                }

                if (!exceptionObserved)
                {
                    Console.WriteLine("Current state of operation '{0}': {1}", operationId, progress.State);

                    if (progress.State == TestCommandProgressState.Faulted)
                    {
                        // Inspect the progress object's Result.Exception.HResult to get the error code.
                        Console.WriteLine("'{0}' failed with: {1}, HResult: {2}", operationId, progress.Result.Exception, progress.Result.Exception.HResult);

                        // ...additional logic as required
                    }

                    if (progress.State == targetState)
                    {
                        Console.WriteLine("Target state '{0}' has been reached", targetState);
                        break;
                    }
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }
            while (true);
        }

        static async Task StopNodeAsync(FabricClient fc, string nodeName, int durationInSeconds)
        {
            // Uses the GetNodeListAsync() API to get information about the target node
            Node n = GetNodeInfo(fc, nodeName);

            // Create a Guid
            Guid guid = Guid.NewGuid();

            // Create a NodeStopDescription object, which will be used as a parameter into StartNodeTransition
            NodeStopDescription description = new NodeStopDescription(guid, n.NodeName, n.NodeInstanceId, durationInSeconds);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStopDescription from above, which will stop the target node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    // This is retryable
                }
                catch (FabricTransientException fte)
                {
                    // This is retryable
                }

                // Backoff
                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until hte desired state is reached.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Completed).ConfigureAwait(false);
        }
```

**Esempio 2**: l'esempio seguente avvia un nodo *arrestato*.  Vengono usati alcuni metodi di supporto dal primo esempio.

```csharp
        static async Task StartNodeAsync(FabricClient fc, string nodeName)
        {
            // Uses the GetNodeListAsync() API to get information about the target node
            Node n = GetNodeInfo(fc, nodeName);

            Guid guid = Guid.NewGuid();
            BigInteger nodeInstanceId = n.NodeInstanceId;

            // Create a NodeStartDescription object, which will be used as a parameter into StartNodeTransition
            NodeStartDescription description = new NodeStartDescription(guid, n.NodeName, nodeInstanceId);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStartDescription from above, which will start the target stopped node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);

            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until hte desired state is reached.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Completed).ConfigureAwait(false);
        }
```

**Esempio 3**: l'esempio seguente illustra un uso non corretto.  Questo uso non è corretto perché il valore *stopDurationInSeconds* fornito è maggiore dell'intervallo consentito.  Poiché StartNodeTransitionAsync() genererà un errore irreversibile, l'operazione non è stata accettata e l'API di avanzamento non deve essere chiamata.  Questo esempio usa alcuni metodi di supporto dal primo esempio.

```csharp
        static async Task StopNodeWithOutOfRangeDurationAsync(FabricClient fc, string nodeName)
        {
            Node n = GetNodeInfo(fc, nodeName);

            Guid guid = Guid.NewGuid();

            // Use an out of range value for stopDurationInSeconds to demonstrate error
            NodeStopDescription description = new NodeStopDescription(guid, n.NodeName, n.NodeInstanceId, 99999);

            try
            {
                await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
            }

            catch (FabricException e)
            {
                Console.WriteLine("Caught {0}", e);
                Console.WriteLine("ErrorCode {0}", e.ErrorCode);
                // Output:
                // Caught System.Fabric.FabricException: System.Runtime.InteropServices.COMException (-2147017629)
                // StopDurationInSeconds is out of range ---> System.Runtime.InteropServices.COMException: Exception from HRESULT: 0x80071C63
                // << Parts of exception omitted>>
                //
                // ErrorCode InvalidDuration
            }
        }
```

**Esempio 4**: l'esempio seguente mostra le informazioni sull'errore che verranno restituite dall'API di avanzamento della transizione del nodo quando l'operazione avviata dall'API di transizione del nodo viene accettata, ma si verifica un errore successivamente in fase di esecuzione.  In questo caso l'errore si verifica perché l'API di transizione del nodo tenta di avviare un nodo che non esiste.  Questo esempio usa alcuni metodi di supporto dal primo esempio.

```csharp
        static async Task StartNodeWithNonexistentNodeAsync(FabricClient fc)
        {
            Guid guid = Guid.NewGuid();
            BigInteger nodeInstanceId = 12345;

            // Intentionally use a nonexistent node
            NodeStartDescription description = new NodeStartDescription(guid, "NonexistentNode", nodeInstanceId);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStartDescription from above, which will start the target stopped node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);

            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.  In this case, it will end up in the Faulted state since the node does not exist.
            // When StartNodeTransitionProgressAsync()'s returned progress object has a State if Faulted, inspect the progress object's Result.Exception.HResult to get the error code.
            // In this case, it will be NodeNotFound.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Faulted).ConfigureAwait(false);
        }
```

[stopnode]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.faultmanagementclient?redirectedfrom=MSDN#System_Fabric_FabricClient_FaultManagementClient_StopNodeAsync_System_String_System_Numerics_BigInteger_System_Fabric_CompletionMode_
[stopnodeps]: https://msdn.microsoft.com/library/mt125982.aspx
[startnode]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.faultmanagementclient?redirectedfrom=MSDN#System_Fabric_FabricClient_FaultManagementClient_StartNodeAsync_System_String_System_Numerics_BigInteger_System_String_System_Int32_System_Fabric_CompletionMode_System_Threading_CancellationToken_
[startnodeps]: https://msdn.microsoft.com/library/mt163520.aspx
[nodequery]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient#System_Fabric_FabricClient_QueryClient_GetNodeListAsync_System_String_
[nodequeryps]: https://docs.microsoft.com/powershell/servicefabric/vlatest/Get-ServiceFabricNode?redirectedfrom=msdn
[snt]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.testmanagementclient#System_Fabric_FabricClient_TestManagementClient_StartNodeTransitionAsync_System_Fabric_Description_NodeTransitionDescription_System_TimeSpan_System_Threading_CancellationToken_
[gntp]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.testmanagementclient#System_Fabric_FabricClient_TestManagementClient_GetNodeTransitionProgressAsync_System_Guid_System_TimeSpan_System_Threading_CancellationToken_



<!--HONumber=Jan17_HO4-->


