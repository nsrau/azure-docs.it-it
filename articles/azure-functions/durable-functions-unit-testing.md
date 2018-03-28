---
title: Testing unità di Funzioni durevoli di Azure
description: Informazioni su come eseguire lo unit test di Funzioni durevoli.
services: functions
author: kadimitr
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/28/2018
ms.author: kadimitr
ms.openlocfilehash: a8d2a2281dcaf6e5e308ad4a2aafb167f9ba9121
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="durable-functions-unit-testing"></a>Testing unità di Funzioni durevoli

Il testing unità è una parte importante delle moderne procedure di sviluppo software. Gli unit test verificano il comportamento della logica di business e proteggono dall'introduzione futura di modifiche inosservate che causano un'interruzione. La complessità di Funzioni durevoli può facilmente aumentare e l'introduzione di unit test consentirà quindi di evitare le modifiche che causano un'interruzione. Le sezioni seguenti illustrano come eseguire lo unit test dei tre tipi di funzioni: funzioni del client di orchestrazione, dell'agente di orchestrazione e dell'attività. 

## <a name="prerequisites"></a>prerequisiti

Gli esempi di questo articolo richiedono la conoscenza dei concetti e dei framework seguenti: 

* Testing unità

* Funzioni permanenti 

* [xUnit](https://xunit.github.io/): framework di test

* [moq](https://github.com/moq/moq4): framework di comportamento fittizio


## <a name="base-classes-for-mocking"></a>Classi base per il comportamento fittizio 

Il comportamento fittizio è supportato tramite due classi astratte in Funzioni durevoli:

* [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html) 

* [DurableOrchestrationContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContextBase.html)

Queste sono classi base per [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) e [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) che definiscono i metodi Orchestration Client e Orchestrator. I comportamenti fittizi imposteranno il comportamento previsto per i metodi delle classi base in modo che lo unit test possa verificare la logica di business. Esiste un flusso di lavoro in due passaggi per il testing unità della logica di business nel client di orchestrazione e nell'agente di orchestrazione:

1. Usare le classi base invece dell'implementazione concreta quando si definiscono le firme del client di orchestrazione e dell'agente di orchestrazione.
2. Negli unit test simulare il comportamento delle classi base e verificare la logica di business. 

Nei paragrafi seguenti sono disponibili altre informazioni dettagliate sulle funzioni di test che usano l'associazione del client di orchestrazione e l'associazione del trigger dell'agente di orchestrazione.

## <a name="unit-testing-trigger-functions"></a>Testing unità delle funzioni di trigger

In questa sezione lo unit test convaliderà la logica della funzione di trigger HTTP seguente per avviare nuove orchestrazioni.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

L'attività di unit test servirà a verificare il valore dell'intestazione `Retry-After` fornita nel payload della risposta. Lo unit test simulerà quindi alcuni dei metodi [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html) per assicurare un comportamento prevedibile. 

Prima di tutto è necessaria una simulazione della classe base, [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html). La simulazione può essere una nuova classe che implementa [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html). L'uso di un framework di comportamento fittizio, ad esempio [moq](https://github.com/moq/moq4), semplifica tuttavia il processo:    

```csharp
    // Mock DurableOrchestrationClientBase
    var durableOrchestrationClientBaseMock = new Mock<DurableOrchestrationClientBase>();
```

Viene quindi simulato il metodo `StartNewAsync` per restituire un ID istanza noto.

```csharp
    // Mock StartNewAsync method
    durableOrchestrationClientBaseMock.
        Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
        ReturnsAsync(instanceId);
```

Viene successivamente simulato `CreateCheckStatusResponse` per restituire sempre una risposta HTTP 200 vuota.

```csharp
    // Mock CreateCheckStatusResponse method
    durableOrchestrationClientBaseMock
        .Setup(x => x.CreateCheckStatusResponse(It.IsAny<HttpRequestMessage>(), instanceId))
        .Returns(new HttpResponseMessage
        {
            StatusCode = HttpStatusCode.OK,
            Content = new StringContent(string.Empty),
        });
```

Viene simulato anche `TraceWriter`:

```csharp
    // Mock TraceWriter
    var traceWriterMock = new Mock<TraceWriter>(TraceLevel.Info);

```  

Il metodo `Run` viene ora chiamato dallo unit test:

```csharp
    // Call Orchestration trigger function
    var result = await HttpStart.Run(
        new HttpRequestMessage()
        {
            Content = new StringContent("{}", Encoding.UTF8, "application/json"),
            RequestUri = new Uri("http://localhost:7071/orchestrators/E1_HelloSequence"),
        },
        durableOrchestrationClientBaseMock.Object, 
        functionName,
        traceWriterMock.Object);
 ``` 

 L'ultimo passaggio è il confronto dell'output con il valore previsto:

```csharp
    // Validate that output is not null
    Assert.NotNull(result.Headers.RetryAfter);

    // Validate output's Retry-After header value
    Assert.Equal(TimeSpan.FromSeconds(10), result.Headers.RetryAfter.Delta);
```

Dopo avere combinato tutti i passaggi, lo unit test avrà il codice seguente: 

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HttpStartTests.cs)]

## <a name="unit-testing-orchestrator-functions"></a>Testing unità delle funzioni dell'agente di orchestrazione

Le funzioni dell'agente di orchestrazione sono ancora più interessanti per il testing unità perché in genere contengono molta più logica di business. Le funzioni dell'agente di orchestrazione attualmente possono essere implementate solo in C#.

In questa sezione gli unit test convalideranno l'output della funzione dell'agente di orchestrazione `E1_HelloSequence`:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

Il codice degli unit test inizierà con la creazione di una simulazione:

```csharp
    var durableOrchestrationContextMock = new Mock<DurableOrchestrationContextBase>();
```

Verranno quindi simulate le chiamate al metodo dell'attività:

```csharp
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Tokyo")).ReturnsAsync("Hello Tokyo!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Seattle")).ReturnsAsync("Hello Seattle!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "London")).ReturnsAsync("Hello London!");
```

Lo unit test chiamerà successivamente il metodo `HelloSequence.Run`:

```csharp
    var result = await HelloSequence.Run(durableOrchestrationContextMock.Object);
```

Infine verrà convalidato l'output:

```csharp
    Assert.Equal(3, result.Count);
    Assert.Equal("Hello Tokyo!", result[0]);
    Assert.Equal("Hello Seattle!", result[1]);
    Assert.Equal("Hello London!", result[2]);
```

Dopo avere combinato tutti i passaggi, lo unit test avrà il codice seguente:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceOrchestratorTests.cs)]

## <a name="unit-testing-activity-functions"></a>Testing unità delle funzioni dell'attività

È possibile eseguire lo unit test delle funzioni dell'attività nello stesso modo in cui si esegue quello delle funzioni non durevoli. Le funzioni dell'attività non hanno una classe base per il comportamento fittizio. Gli unit test quindi usano direttamente i tipi di parametro.

In questa sezione lo unit test convaliderà il comportamento della funzione dell'attività `E1_SayHello`:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

Lo unit test verificherà il formato dell'output:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni su xUnit](http://xunit.github.io/docs/getting-started-dotnet-core)

> [Altre informazioni su moq](https://github.com/Moq/moq4/wiki/Quickstart)