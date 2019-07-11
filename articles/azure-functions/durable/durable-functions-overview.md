---
title: Panoramica di Funzioni permanenti - Azure
description: Introduzione all'estensione Funzioni permanenti per Funzioni di Azure.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: overview
ms.date: 12/22/2018
ms.author: glenga
ms.reviewer: azfuncdf
ms.openlocfilehash: 6e1acf5f605d7f6fb42d24e6e7ec624a317b0e31
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612870"
---
# <a name="what-are-durable-functions"></a>Informazioni su Durable Functions

*Durable Functions* è un'estensione di [Funzioni di Azure](../functions-overview.md) che consente di scrivere funzioni con stato in un ambiente serverless. L'estensione gestisce automaticamente lo stato, i checkpoint e i riavvii.

## <a name="benefits"></a>Vantaggi

L'estensione consente di definire flussi di lavoro con stato che usano una [*funzione di orchestrazione*](durable-functions-types-features-overview.md#orchestrator-functions), che può offrire i vantaggi seguenti:

* È possibile definire i flussi di lavoro nel codice. Non sono necessari schemi JSON o finestre di progettazione.
* È possibile chiamare altre funzioni in modo sincrono e asincrono. L'output delle funzioni chiamate può essere salvato in variabili locali.
* Il checkpoint dello stato di avanzamento viene eseguito automaticamente ogni volta che la funzione è in attesa. Lo stato locale non va mai perso quando il processo viene riciclato o la macchina virtuale viene riavviata.

## <a name="application-patterns"></a>Modelli di applicazione

Il caso d'uso principale per Durable Functions è la semplificazione dei requisiti complessi di coordinamento con stato nelle applicazioni senza server. Di seguito sono riportati alcuni modelli di applicazione tipici che possono trarre vantaggio da Durable Functions:

* [Concatenamento](durable-functions-concepts.md#chaining)
* [Fan-out/fan-in](durable-functions-concepts.md#fan-in-out)
* [API HTTP asincrone](durable-functions-concepts.md#async-http)
* [Monitoraggio](durable-functions-concepts.md#monitoring)
* [Interazione umana](durable-functions-concepts.md#human)

## <a name="language-support"></a>Lingue supportate

Durable Functions supporta attualmente i linguaggi seguenti:

* **C#** : sia le [librerie di classe precompilate](../functions-dotnet-class-library.md) che lo [script C#](../functions-reference-csharp.md).
* **F#** : librerie di classe precompilate e script F#. Lo script F# è supportato solo per la versione 1.x del runtime di Funzioni di Azure.
* **JavaScript**: supportato per la versione 2.x del runtime di Funzioni di Azure. Richiede la versione 1.7.0 dell'estensione Durable Functions, o versione successiva. 

Durable Functions ha l'obiettivo di supportare tutti i [linguaggi di Funzioni di Azure](../supported-languages.md). Vedere l'[elenco dei problemi di Durable Functions](https://github.com/Azure/azure-functions-durable-extension/issues) per ottenere lo stato del lavoro più recente a supporto di linguaggi aggiuntivi.

Come per Funzioni di Azure, sono disponibili modelli che contribuiscono allo sviluppo di Durable Functions tramite [Visual Studio 2019](durable-functions-create-first-csharp.md), [Visual Studio Code](quickstart-js-vscode.md) e il [portale di Azure](durable-functions-create-portal.md).

## <a name="billing"></a>Fatturazione

La fatturazione di Durable Functions è analoga a quella delle Funzioni di Azure. Per altre informazioni, vedere [Prezzi di Funzioni](https://azure.microsoft.com/pricing/details/functions/).

## <a name="jump-right-in"></a>Per iniziare immediatamente

È possibile iniziare a usare Durable Functions in meno di 10 minuti completando una di queste esercitazioni introduttive specifiche del linguaggio:

* [C# con Visual Studio 2019](durable-functions-create-first-csharp.md)
* [JavaScript con Visual Studio Code](quickstart-js-vscode.md)

In entrambe le guide introduttive, si crea e testa in locale una funzione durevole "hello world". Il codice della funzione verrà quindi pubblicato in Azure. La funzione creata orchestra e concatena le chiamate ad altre funzioni.

## <a name="learn-more"></a>Altre informazioni

Il video seguente illustra i vantaggi di Durable Functions:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Durable-Functions-in-Azure-Functions/player] 

Dal momento che Durable Functions è un'estensione avanzata per [Funzioni di Azure](../functions-overview.md) non è adatta a tutte le applicazioni. Per altre informazioni su Durable Functions, vedere [Modelli e concetti tecnici per Durable Functions](durable-functions-concepts.md). Per un confronto con altre tecnologie di orchestrazione di Azure, vedere [Confrontare Funzioni di Azure e App per la logica di Azure](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-azure-functions-and-azure-logic-apps).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Modelli e concetti tecnici per Durable Functions](durable-functions-concepts.md)
