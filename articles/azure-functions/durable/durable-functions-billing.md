---
title: Fatturazione di Durable Functions - Funzioni di Azure
description: Informazioni sui comportamenti interni di Durable Functions e su come influiscono sulla fatturazione di Funzioni di Azure.
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: overview
ms.date: 08/31/2019
ms.author: azfuncdf
ms.openlocfilehash: b79d50adf932bd5c316fbda9d0964eea7c0484ca
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935459"
---
# <a name="durable-functions-billing"></a>Fatturazione di Durable Functions

La fatturazione di [Durable Functions](durable-functions-overview.md) è analoga a quella di Funzioni di Azure. Per altre informazioni, vedere [Prezzi di Funzioni](https://azure.microsoft.com/pricing/details/functions/). Quando si eseguono le funzioni dell'agente di orchestrazione nel [piano a consumo](../functions-scale.md#consumption-plan) di Funzioni di Azure, è necessario tenere presenti alcuni aspetti della fatturazione. Le sezioni seguenti descrivono questi comportamenti in modo più dettagliato.

## <a name="orchestrator-function-replay-billing"></a>Fatturazione della riesecuzione di funzioni dell'agente di orchestrazione

Le [funzioni dell'agente di orchestrazione](durable-functions-orchestrations.md) possono essere rieseguite diverse volte per l'intera durata dell'orchestrazione. Ogni riesecuzione viene considerata dal runtime di Funzioni di Azure come una chiamata di funzione distinta. Per questo motivo, nel piano a consumo di Funzioni di Azure, verrà fatturata ogni riesecuzione della funzione dell'agente di orchestrazione. In altri tipi di piani la riesecuzione delle funzioni dell'agente di orchestrazione non viene addebitata.

## <a name="awaiting-and-yielding-in-orchestrator-functions"></a>Attesa e sospensione delle funzioni dell'agente di orchestrazione

Quando una funzione dell'agente di orchestrazione attende il completamento di un'azione asincrona tramite `await` (C#) o `yield` (JavaScript), il runtime considera questa particolare esecuzione come completata. La fatturazione per la funzione dell'agente di orchestrazione si interrompe a questo punto e riprende solo alla successiva riesecuzione. Il tempo di attesa o di sospensione di una funzione dell'agente di orchestrazione non viene fatturato.

> [!NOTE]
> Le funzioni che chiamano altre funzioni vengono a volte considerate un antipattern. Il motivo è dovuto a un problema noto come _doppia fatturazione_. Quando una funzione chiama direttamente un'altra funzione, vengono eseguite entrambe contemporaneamente. La funzione chiamata esegue attivamente codice, mentre la chiamante è in attesa di una risposta. In questo caso, è necessario pagare per il tempo trascorso dalla funzione chiamante in attesa dell'esecuzione di quella chiamata. Le funzioni dell'agente di orchestrazione non sono soggette a doppia fatturazione, perché la fatturazione di una funzione si interrompe durante l'attesa del risultato di una funzione di attività (o orchestrazione secondaria).

## <a name="durable-http-polling"></a>Polling HTTP durevole

Le funzioni dell'agente di orchestrazione possono effettuare chiamate HTTP con esecuzione prolungata a endpoint esterni, come descritto nell'articolo sulle [funzionalità HTTP](durable-functions-http-features.md). Il metodo `CallHttpAsync` (C#) e il metodo `callHttp` (JavaScript) possono eseguire internamente il polling di un endpoint HTTP quando seguono il [modello 202 asincrono](durable-functions-http-features.md#http-202-handling). Non è attualmente prevista una fatturazione diretta per le operazioni di polling HTTP interno. Tuttavia, il polling interno può causare la riesecuzione periodica della funzione dell'agente di orchestrazione e queste riesecuzioni di funzioni interne verranno fatturate con addebiti standard.

## <a name="azure-storage-transactions"></a>Transazioni di archiviazione di Azure

Durable Functions usa Archiviazione di Azure per impostazione predefinita per la persistenza dello stato, l'elaborazione dei messaggi e la gestione di partizione tramite lease di BLOB. Questo account di archiviazione è di proprietà dell'utente, quindi nella sottoscrizione di Azure vengono fatturati solo gli eventuali costi delle transazioni. Per altre informazioni sugli artefatti di Archiviazione di Azure usati da Durable Functions, vedere l'articolo sugli [hub attività](durable-functions-task-hubs.md).

Diversi fattori contribuiscono ai costi effettivi di Archiviazione di Azure generati dall'applicazione di Durable Functions.

* Una singola app per le funzioni viene associata a un singolo hub attività, che condivide un set di risorse di Archiviazione di Azure. Queste risorse vengono usate da tutte le istanze di Durable Functions in un'app per le funzioni. Il numero effettivo di funzioni dell'app per le funzioni non influisce sui costi delle transazioni di Archiviazione di Azure.
* Ogni istanza di app per le funzioni esegue internamente il polling di più code nell'account di archiviazione usando un algoritmo di polling basato su backoff esponenziale. Un'istanza di applicazione inattiva eseguirà il polling delle code meno frequentemente rispetto a un'applicazione attiva, quindi con meno costi per le transazioni. Per altre informazioni sul comportamento del polling delle code di Durable Functions, vedere la sezione sul [polling di code nell'articolo su prestazioni e scalabilità](durable-functions-perf-and-scale.md#queue-polling).
* Quando viene eseguito nei piani a consumo o Premium di Funzioni di Azure, il [controller di scalabilità di Funzioni di Azure](../functions-scale.md#how-the-consumption-and-premium-plans-work) esegue regolarmente il polling di tutte le code dell'hub attività in background. Su scala leggera o moderata, il polling di queste code viene eseguito solo da una singola istanza del controller di scalabilità. Se l'app per le funzioni viene ampliata per includere un numero elevato di istanze, potrebbero essere aggiunte altre istanze del controller di scalabilità. Queste istanze aggiuntive possono aumentare i costi totali delle transazioni delle code.
* Ogni istanza dell'app per le funzioni compete per un set di lease di BLOB. Queste istanze effettuano periodicamente chiamate al servizio BLOB di Azure per rinnovare i lease mantenuti o per provare ad acquisirne di nuovi. Il numero di lease di BLOB è determinato dal conteggio di partizioni configurato dell'hub attività. L'ampliamento con l'aggiunta di un numero più elevato di istanze dell'app per le funzioni comporta un aumento dei costi delle transazioni di Archiviazione di Azure con queste operazioni di lease.

Per altre informazioni, vedere la [documentazione sui prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni sui prezzi di Funzioni di Azure](https://azure.microsoft.com/pricing/details/functions/)
