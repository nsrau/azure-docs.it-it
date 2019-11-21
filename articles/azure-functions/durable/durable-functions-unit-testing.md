---
title: Testing unità di Funzioni durevoli di Azure
description: Informazioni su come eseguire lo unit test di Funzioni durevoli.
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: 86733f8b5b80799bad3e52c643ed27465dfc7641
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231232"
---
# <a name="durable-functions-unit-testing"></a>Testing unità di Funzioni durevoli

Il testing unità è una parte importante delle moderne procedure di sviluppo software. Gli unit test verificano il comportamento della logica di business e proteggono dall'introduzione futura di modifiche inosservate che causano un'interruzione. La complessità di Funzioni durevoli può facilmente aumentare e l'introduzione di unit test consentirà quindi di evitare le modifiche che causano un'interruzione. The following sections explain how to unit test the three function types - Orchestration client, orchestrator, and activity functions.

> [!NOTE]
> This article provides guidance for unit testing for Durable Functions apps targeting Durable Functions 1.x. It has not yet been updated to account for changes introduced in Durable Functions 2.x. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

## <a name="prerequisites"></a>Prerequisiti

Gli esempi di questo articolo richiedono la conoscenza dei concetti e dei framework seguenti:

* Testing unità

* Funzioni permanenti

* [xUnit](https://xunit.github.io/): framework di test

* [moq](https://github.com/moq/moq4): framework di comportamento fittizio

## <a name="base-classes-for-mocking"></a>Classi base per il comportamento fittizio

Mocking is supported via three abstract classes in Durable Functions 1.x:

* `DurableOrchestrationClientBase`

* `DurableOrchestrationContextBase`

* `DurableActivityContextBase`

These classes are base classes for `DurableOrchestrationClient`, `DurableOrchestrationContext`, and `DurableActivityContext` that define Orchestration Client, Orchestrator, and Activity methods. I comportamenti fittizi imposteranno il comportamento previsto per i metodi delle classi base in modo che lo unit test possa verificare la logica di business. Esiste un flusso di lavoro in due passaggi per il testing unità della logica di business nel client di orchestrazione e nell'agente di orchestrazione:

1. Use the base classes instead of the concrete implementation when defining orchestration client and orchestrator function signatures.
2. Negli unit test simulare il comportamento delle classi base e verificare la logica di business.

Nei paragrafi seguenti sono disponibili altre informazioni dettagliate sulle funzioni di test che usano l'associazione del client di orchestrazione e l'associazione del trigger dell'agente di orchestrazione.

## <a name="unit-testing-trigger-functions"></a>Testing unità delle funzioni di trigger

In questa sezione lo unit test convaliderà la logica della funzione di trigger HTTP seguente per avviare nuove orchestrazioni.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

L'attività di unit test servirà a verificare il valore dell'intestazione `Retry-After` fornita nel payload della risposta. So the unit test will mock some of `DurableOrchestrationClientBase` methods to ensure predictable behavior.

First, a mock of the base class is required, `DurableOrchestrationClientBase`. The mock can be a new class that implements `DurableOrchestrationClientBase`. L'uso di un framework di comportamento fittizio, ad esempio [moq](https://github.com/moq/moq4), semplifica tuttavia il processo:

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
            Headers =
            {
                RetryAfter = new RetryConditionHeaderValue(TimeSpan.FromSeconds(10))
            }
        });
```

Viene simulato anche `ILogger`:

```csharp
    // Mock ILogger
    var loggerMock = new Mock<ILogger>();
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
        loggerMock.Object);
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

Le funzioni dell'agente di orchestrazione sono ancora più interessanti per il testing unità perché in genere contengono molta più logica di business.

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

È possibile eseguire lo unit test delle funzioni dell'attività nello stesso modo in cui si esegue quello delle funzioni non durevoli.

In questa sezione lo unit test convaliderà il comportamento della funzione dell'attività `E1_SayHello`:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

Lo unit test verificherà il formato dell'output. The unit tests can use the parameter types directly or mock `DurableActivityContextBase` class:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni su xUnit](https://xunit.github.io/docs/getting-started-dotnet-core)
> 
> [Altre informazioni su moq](https://github.com/Moq/moq4/wiki/Quickstart)
