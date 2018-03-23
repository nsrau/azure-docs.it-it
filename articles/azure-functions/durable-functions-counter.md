---
title: Singleton con stato in Funzioni permanenti - Azure
description: Informazioni su come implementare un singleton con stato nell'estensione Funzioni permanenti per le Funzioni di Azure.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 46cdd8523117e1100e7ce2a29ade9eb2dc0afe75
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="stateful-singletons-in-durable-functions---counter-sample"></a>Singleton con stato in Funzioni permanenti - Contatore di esempio

> [!NOTE]
> È in corso la riscrittura di questo esempio. I frammenti di codice sono stati rimossi e verrà creato in sostituzione un nuovo articolo con un nuovo esempio.

I singleton con stato sono funzioni di orchestrazione a esecuzione prolungata (potenzialmente perenni) che possono archiviare lo stato ed essere richiamate e sottoposte a query da altre funzioni. I singleton con stato sono simili al [modello Actor](https://en.wikipedia.org/wiki/Actor_model) in elaborazione distribuita.

Sebbene non siano un'implementazione "attore" vera e propria, le funzioni di orchestrazione hanno molte caratteristiche di runtime analoghe. Sono ad esempio con stato, affidabili, a thread singolo, con trasparenza di locazione e indirizzabili a livello globale. Queste sono le caratteristiche che rendono le implementazioni attore reali particolarmente utili, ma senza la necessità di un framework separato.

Questo articolo mostra come eseguire il *contatore* di esempio. L'esempio illustra un oggetto singleton che supporta le operazioni di *incremento* e *decremento* e aggiorna di conseguenza il proprio stato interno.

## <a name="prerequisites"></a>prerequisiti

* Seguire le istruzioni in [Install Durable Functions](durable-functions-install.md) (Installare Funzioni permanenti) per impostare l'esempio.
* Questo articolo presuppone che l'utente abbia già eseguito la procedura dettagliata di esempio descritta in [Hello Sequence](durable-functions-sequence.md) (Sequenza di Hello).

## <a name="scenario-overview"></a>Panoramica dello scenario

Lo scenario del contatore è incredibilmente difficile da implementare con le normali funzioni senza stato. Una delle sfide principali per l'utente è quella di gestire la **concorrenza**. Operazioni come quelle di *incremento* e *decremento* devono essere atomiche o in caso contrario potrebbe verificarsi una race condition che comporterebbe la sovrascrittura reciproca delle operazioni.

Un'opzione può essere quella di usare una singola VM per ospitare i dati del contatore, ma è dispendiosa e gestire l'**affidabilità** può risultare difficile, poiché una singola VM potrebbe dover essere riavviata periodicamente. In alternativa, per agevolare la gestione della concorrenza si può usare una piattaforma distribuita con strumenti per la sincronizzazione come i lease BLOB, ma questa operazione presenta una notevole **complessità**.

Le Funzioni permanenti facilitano l'implementazione di questo tipo di scenario, perché le istanze di orchestrazione sono rese affini a una singola VM e l'esecuzione della funzione di orchestrazione è sempre a thread singolo. Inoltre sono a esecuzione prolungata, con stato e possono rispondere agli eventi esterni. L'esempio di codice seguente illustra come implementare questo tipo di contatore come funzione di orchestrazione con esecuzione prolungata.

## <a name="the-sample-function"></a>Funzione di esempio

Questo articolo analizza la funzione **E3_Counter** nell'app di esempio.



## <a name="the-counter-orchestration"></a>Orchestrazione del contatore

Le sezioni seguenti illustrano il codice usato per lo sviluppo in Visual Studio Code e nel portale di Azure.

### <a name="c-script"></a>Script C#

Il file function.json:

<!-- [!code-json[Main](~/samples-durable-functions/samples/csx/E3_Counter/function.json)] -->

Il file run.csx:

<!-- [!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_Counter/run.csx)] -->

### <a name="precompiled-c"></a>C# precompilato 

Le sezioni seguenti illustrano il codice usato per lo sviluppo di Visual Studio.

Il codice che implementa la funzione dell'agente di orchestrazione è il seguente:

<!-- [!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Counter.cs)] -->

### <a name="explanation-of-the-code"></a>Descrizione del codice

Le operazioni di questa funzione dell'agente di orchestrazione sono le seguenti:

1. Ascolta un evento esterno denominato *operation* usando [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_).
2. Incrementa o decrementa la variabile locale `counterState` a seconda dell'operazione richiesta.
3. Riavvia l'agente di orchestrazione usando il metodo [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_), impostando il valore più recente di `counterState` come nuovo input.
4. Continua l'esecuzione all'infinito o fino alla ricezione di un messaggio di *fine*.

Ecco un esempio di *orchestrazione perenne* &mdash;, ovvero che potrebbe non terminare mai. Risponde ai messaggi inviati dal metodo [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_), che può essere chiamato da qualsiasi funzione che non sia di orchestrazione.

Una caratteristica univoca di questa funzione di orchestrazione è che non ha effettivamente alcuna cronologia: il metodo `ContinueAsNew` reimposta la cronologia dopo ogni evento elaborato. Questo è il modo migliore di implementare un agente di orchestrazione che ha una durata arbitraria. L'uso di un ciclo `while` potrebbe causare un aumento illimitato della cronologia della funzione di orchestrazione, con un conseguente uso troppo elevato della memoria.

> [!NOTE]
> Oltre alle orchestrazioni perenni, il metodo `ContinueAsNew` presenta altri casi d'uso. Per altre informazioni, vedere [Orchestrazioni perenni](durable-functions-eternal-orchestrations.md).

## <a name="run-the-sample"></a>Eseguire l'esempio

È possibile avviare l'orchestrazione inviando la richiesta HTTP POST seguente. Per consentire a `counterState` di iniziare da zero (valore predefinito per `int`), questa richiesta non ha contenuto.

```
POST http://{host}/orchestrators/E3_Counter
Content-Length: 0
```

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{
  "id":"bcf6fb5067b046fbb021b52ba7deae5a",
  "statusQueryGetUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}",
  "sendEventPostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}",
  "terminatePostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}"}
```

L'istanza **E3_Counter** si avvia e attende immediatamente che le venga inviato un evento tramite `RaiseEventAsync` o il webhook HTTP POST **sendEventUrl** a cui fa riferimento la risposta 202. I valori `eventName` validi comprendono *incr*, *decr* e *end*.

```
POST http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Type: application/json
Content-Length: 6

"incr"
```

È possibile visualizzare i risultati dell'operazione "incr" esaminando i log della funzione nel portale Funzioni di Azure.

```
2017-06-29T18:54:53.998 Function started (Id=34e34a61-38b3-4eac-b6e2-98b85e32eec8)
2017-06-29T18:54:53.998 Current counter state is 0. Waiting for next operation.
2017-06-29T18:58:01.458 Function started (Id=b45d6c2f-39f3-42a2-b904-7761b2614232)
2017-06-29T18:58:01.458 Current counter state is 0. Waiting for next operation.
2017-06-29T18:58:01.458 Received 'incr' operation.
2017-06-29T18:58:01.458 Function completed (Success, Id=b45d6c2f-39f3-42a2-b904-7761b2614232, Duration=8ms)
2017-06-29T18:58:11.518 Function started (Id=e1f38cb2-546a-404d-ab22-1ac8f81a93d9)
2017-06-29T18:58:11.518 Current counter state is 1. Waiting for next operation.
```

Analogamente, controllando lo stato dell'agente di orchestrazione, si visualizza il campo `input` impostato sul valore aggiornato (1).

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey} HTTP/1.1
```

```
HTTP/1.1 202 Accepted
Content-Length: 129
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"runtimeStatus":"Running","input":1,"output":null,"createdTime":"2017-06-29T18:58:01Z","lastUpdatedTime":"2017-06-29T18:58:11Z"}
```

È possibile continuare a inviare nuove operazioni a questa istanza e osservare che il suo stato viene aggiornato di conseguenza. Per terminare l'istanza, è possibile inviare un'operazione di *fine*.

> [!WARNING]
> Al momento della scrittura, si verificano race condition note se la chiamata di `ContinueAsNew` avviene durante l'elaborazione contemporanea di messaggi, come ad esempio eventi esterni o richieste di risoluzione. Per informazioni aggiornate sulle race condition, vedere questo [Problema di GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/67).

## <a name="next-steps"></a>Passaggi successivi

In questo esempio è stato illustrato come gestire [eventi esterni](durable-functions-external-events.md) e implementare [orchestrazioni perenni](durable-functions-eternal-orchestrations.md) in [singleton con stato](durable-functions-singletons.md). Nell'esempio successivo viene illustrato come usare eventi esterni e [timer durevoli](durable-functions-timers.md) per gestire l'interazione umana.

> [!div class="nextstepaction"]
> [Eseguire l'esempio di interazione umana](durable-functions-phone-verification.md)
