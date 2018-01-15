---
title: Orchestrazioni perenni in Funzioni permanenti - Azure
description: Informazioni su come implementare le orchestrazioni perenni usando l'estensione Funzioni permanenti per Funzioni di Azure.
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 1256e7f0286d9eb6ea6498b024fba41eb9f6a641
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2018
---
# <a name="eternal-orchestrations-in-durable-functions-azure-functions"></a>Orchestrazioni perenni in Funzioni permanenti (Funzioni di Azure)

Le *orchestrazioni perenni* sono funzioni di orchestrazione che non terminano mai. Sono utili quando si vuole usare [Funzioni permanenti](durable-functions-overview.md) per gli aggregatori e per qualsiasi scenario che richiede un ciclo infinito.

## <a name="orchestration-history"></a>Cronologia di orchestrazione

Come spiegato nelle informazioni su [checkpoint e riesecuzione](durable-functions-checkpointing-and-replay.md), Durable Task Framework tiene traccia della cronologia di ogni orchestrazione di funzioni. Questa cronologia aumenta in modo costante finché la funzione dell'agente di orchestrazione continua a pianificare nuovo lavoro. Se la funzione dell'agente di orchestrazione entra in un ciclo infinito e pianifica lavoro in modo continuo, le dimensioni della cronologia potrebbero diventare eccessive con notevoli problemi a livello di prestazioni. Il concetto di *orchestrazione perenne* è stato ideato per ridurre questa tipologia di problemi per le applicazioni che necessitano di un ciclo infinito.

## <a name="resetting-and-restarting"></a>Reimpostazione e riavvio

Anziché usare cicli infiniti, le funzioni di orchestrazione reimpostano lo stato chiamando il metodo [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_). Questo metodo accetta un singolo parametro serializzabile in JSON, che diventa il nuovo input per la generazione delle funzioni di orchestrazione successive.

Quando viene chiamato il metodo `ContinueAsNew`, l'istanza accoda un messaggio a se stessa prima della chiusura. Il messaggio riavvia l'istanza con il nuovo valore di input. Viene mantenuto lo stesso ID istanza, ma la cronologia della funzione dell'agente di orchestrazione viene di fatto troncata.

> [!NOTE]
> Durable Task Framework mantiene lo stesso ID istanza, ma crea internamente un nuovo *ID esecuzione* per la funzione dell'agente di orchestrazione reimpostata da `ContinueAsNew`. Questo ID di esecuzione in genere non viene esposto esternamente, ma può essere utile da conoscere durante il debug dell'esecuzione dell'orchestrazione.

## <a name="periodic-work-example"></a>Esempio di operazioni periodiche

Un caso d'uso di orchestrazioni perenni è il codice che deve eseguire operazioni periodiche per un periodo illimitato.

```csharp
[FunctionName("Periodic_Cleanup_Loop")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    await context.CallActivityAsync("DoCleanup");

    // sleep for one hour between cleanups
    DateTime nextCleanup = context.CurrentUtcDateTime.AddHours(1);
    await context.CreateTimer<string>(nextCleanup);

    context.ContinueAsNew(null);
}
```

La differenza tra questo esempio e una funzione attivata da timer è che i tempi del trigger di pulizia non sono basati su una pianificazione. Ad esempio, una pianificazione CRON che esegue una funzione ogni ora verrà eseguita all'1:00, alle 2:00, alle 3:00 e così via e potrebbe potenzialmente generare problemi di sovrapposizione. In questo esempio, tuttavia, se la pulizia richiede 30 minuti, verrà pianificata all'1:00, alle 2:30, alle 4:00 e così via senza possibilità di sovrapposizione.

## <a name="counter-example"></a>Esempio di contatore

Di seguito è riportato un esempio semplificato di una funzione *counter* in ascolto di eventi di *incremento* e *decremento* in modo perenne.

```csharp
[FunctionName("SimpleCounter")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    int counterState = context.GetInput<int>();

    string operation = await context.WaitForExternalEvent<string>("operation");

    if (operation == "incr")
    {
        counterState++;
    }
    else if (operation == "decr")
    {
        counterState--;
    }
    
    context.ContinueAsNew(counterState);
}
```

## <a name="exit-from-an-eternal-orchestration"></a>Uscire da un'orchestrazione perenne

Se una funzione dell'agente di orchestrazione deve essere completata, è sufficiente *non* chiamare `ContinueAsNew` e consentire l'uscita della funzione.

Se una funzione dell'agente di orchestrazione è in un ciclo infinito e deve essere arrestata, usare il metodo [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) a tale scopo. Per altre informazioni, vedere [Gestione delle istanze](durable-functions-instance-management.md).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su come implementare le orchestrazioni singleton](durable-functions-singletons.md)

> [!div class="nextstepaction"]
> [Eseguire un'orchestrazione perenne di esempio](durable-functions-counter.md)
