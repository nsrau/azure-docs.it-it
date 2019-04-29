---
title: Come usare il feed di modifiche di Azure Cosmos DB con Funzioni di Azure
description: Usare il feed di modifiche di Azure Cosmos DB con Funzioni di Azure
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 18780deba1910b3ea77f7313bfb6d204dacabd82
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62112022"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>Architetture senza server basata su eventi con Azure Cosmos DB e funzioni di Azure

Funzioni di Azure fornisce il modo più semplice per la connessione per il [feed di modifiche](change-feed.md). È possibile creare piccole reattivo funzioni di Azure che verrà attivato automaticamente per ogni nuovo evento nel feed di modifiche del contenitore di Azure Cosmos.

![Funzioni senza server basata su eventi funziona con il Trigger di Azure Cosmos DB](./media/change-feed-functions/functions.png)

Con il [Trigger di Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger), è possibile sfruttare le [processore dei Feed delle modifiche](./change-feed-processor.md)scalabilità dell'e funzionalità di rilevamento di eventi affidabile senza la necessità di mantenere qualsiasi [ruolo di lavoro infrastruttura](./change-feed-processor.md#implementing-the-change-feed-processor-library). Concentrati solo sulla logica della funzione di Azure senza doversi preoccupare il resto della pipeline-sourcing di eventi. È anche possibile combinare il Trigger con qualsiasi altro [associazioni di funzioni di Azure](../azure-functions/functions-triggers-bindings.md#supported-bindings).

> [!NOTE]
> Attualmente, il trigger di Azure Cosmos DB è supportato per l'uso con il Core (API SQL) solo.

## <a name="requirements"></a>Requisiti

Per implementare un flusso basato su eventi senza server, è necessario:

* **Contenitore monitorato**: Contenitore monitorato è il contenitore di Azure Cosmos monitorato, e archivia i dati da cui viene generato il feed delle modifiche. Eventuali inserimenti e modifiche (ad esempio, CRUD) al contenitore monitorato vengono riflesse nel feed di modifiche del contenitore.
* **Il contenitore di lease**: Il contenitore di lease gestisce lo stato tra più e istanze di funzione di Azure senza server dinamica e consente la scalabilità dinamica. Questo contenitore del lease può essere manualmente o automaticamente creato dal Trigger.To DB Cosmos Azure automaticamente creare il contenitore di lease, impostare il *CreateLeaseCollectionIfNotExists* flag nel [configuration](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration). I contenitori partizionati lease sono necessari per avere una `/id` definizione della chiave di partizione.

## <a name="create-your-azure-cosmos-db-trigger"></a>Creare il Trigger di Azure Cosmos DB

Creare una funzione di Azure con un Trigger di Azure Cosmos DB è ora supportato in tutti i IDE funzioni di Azure e le integrazioni della riga di comando:

* [Estensione di Visual Studio](../azure-functions/functions-develop-vs.md) per gli utenti di Visual Studio.
* [Estensione di Visual Studio Core](https://code.visualstudio.com/tutorials/functions-extension/create-function) per gli utenti di Visual Studio Code.
* E infine [gli strumenti CLI Core](../azure-functions/functions-run-local.md#create-func) per un'esperienza agnostica IDE lo sviluppo multipiattaforma.

## <a name="run-your-azure-cosmos-db-trigger-locally"></a>Eseguire il Trigger di Azure Cosmos DB in locale

È possibile eseguire la [funzione di Azure in locale](../azure-functions/functions-develop-local.md) con il [emulatore Azure Cosmos DB](./local-emulator.md) per creare e sviluppare i flussi senza server basata su eventi senza una sottoscrizione di Azure né sostenere costi.

Se si desidera testare gli scenari in tempo reale nel cloud, puoi [prova gratuitamente per Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) senza alcuna carta di credito o una sottoscrizione di Azure necessari.

## <a name="next-steps"></a>Passaggi successivi

È ora possibile continuare a ulteriori informazioni sui feed di modifiche negli articoli seguenti:

* [Panoramica del feed di modifiche](change-feed.md)
* [Metodi per leggere il feed di modifiche](read-change-feed.md)
* [Uso della libreria del processore dei feed di modifiche](change-feed-processor.md)
* [Uso della libreria del processore dei feed di modifiche di Azure Cosmos DB](change-feed-processor.md)
* [Elaborazione di database serverless con Azure Cosmos DB e Funzioni di Azure](serverless-computing-database.md)
