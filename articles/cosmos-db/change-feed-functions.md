---
title: Come usare il feed di modifiche di Azure Cosmos DB con Funzioni di Azure
description: Usare il feed di modifiche di Azure Cosmos DB con Funzioni di Azure
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 95fec1ef57c1d70ea484de9ad49b3410ed8594a4
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757059"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>Architetture basate su eventi senza server con Azure Cosmos DB e funzioni di Azure

Funzioni di Azure fornisce il modo più semplice per connettersi al [feed delle modifiche](change-feed.md). È possibile creare piccole funzioni di Azure riattive che verranno attivate automaticamente per ogni nuovo evento nel feed di modifiche del contenitore di Azure Cosmos.

![Funzioni basate su eventi senza Server uso del trigger di funzioni di Azure per Cosmos DB](./media/change-feed-functions/functions.png)

Con il [trigger di funzioni di Azure per Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger), è possibile sfruttare le funzionalità di scalabilità e rilevamento degli eventi affidabili del [processore del feed delle modifiche](./change-feed-processor.md)senza dover gestire alcuna infrastruttura di [lavoro](./change-feed-processor.md). È sufficiente concentrarsi sulla logica della funzione di Azure senza doversi preoccupare del resto della pipeline di sourcing degli eventi. È anche possibile combinare il trigger con qualsiasi altra [associazione di funzioni di Azure](../azure-functions/functions-triggers-bindings.md#supported-bindings).

> [!NOTE]
> Attualmente, il trigger di funzioni di Azure per Cosmos DB è supportato per l'uso solo con l'API Core (SQL).

## <a name="requirements"></a>Requisiti

Per implementare un flusso basato su eventi senza server, è necessario:

* **Il contenitore monitorato**: il contenitore monitorato è il contenitore Azure Cosmos monitorato e archivia i dati da cui viene generato il feed delle modifiche. Eventuali inserimenti, aggiornamenti al contenitore monitorati vengono riflessi nel feed delle modifiche del contenitore.
* **Contenitore lease**: il contenitore lease mantiene lo stato tra più istanze di funzioni di Azure senza server dinamiche e consente la scalabilità dinamica. Questo contenitore di lease può essere creato manualmente o automaticamente dal trigger di funzioni di Azure per Cosmos DB. Per creare automaticamente il contenitore di lease, impostare il flag *CreateLeaseCollectionIfNotExists* nella [configurazione](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration). Per i contenitori di lease partizionati è necessario disporre di una definizione `/id` chiave di partizione.

## <a name="create-your-azure-functions-trigger-for-cosmos-db"></a>Creare il trigger di funzioni di Azure per Cosmos DB

La creazione della funzione di Azure con un trigger di funzioni di Azure per Cosmos DB è ora supportata in tutte le integrazioni dell'IDE e dell'interfaccia della riga di comando di Azure

* [Estensione di Visual Studio](../azure-functions/functions-develop-vs.md) per gli utenti di Visual Studio.
* [Estensione di base di Visual Studio](/azure/javascript/tutorial-vscode-serverless-node-01) per gli utenti Visual Studio Code.
* E infine gli [strumenti dell'interfaccia](../azure-functions/functions-run-local.md#create-func) della riga di comando di base per un'esperienza IDE multipiattaforma.

## <a name="run-your-trigger-locally"></a>Eseguire il trigger in locale

È possibile eseguire la [funzione di Azure in locale](../azure-functions/functions-develop-local.md) con l' [emulatore Azure Cosmos DB](./local-emulator.md) per creare e sviluppare i flussi basati su eventi senza server senza una sottoscrizione di Azure o per sostenere costi.

Se si vuole testare scenari Live nel cloud, è possibile [provare Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) gratuitamente senza richiedere una carta di credito o una sottoscrizione di Azure.

## <a name="next-steps"></a>Passaggi successivi

È ora possibile continuare a scoprire di più sul feed delle modifiche negli articoli seguenti:

* [Panoramica del feed di modifiche](change-feed.md)
* [Metodi per leggere il feed di modifiche](read-change-feed.md)
* [Uso della libreria del processore dei feed di modifiche](change-feed-processor.md)
* [Uso della libreria del processore dei feed di modifiche di Azure Cosmos DB](change-feed-processor.md)
* [Elaborazione di database serverless con Azure Cosmos DB e Funzioni di Azure](serverless-computing-database.md)
