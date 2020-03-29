---
title: Come usare il feed di modifiche di Azure Cosmos DB con Funzioni di Azure
description: Usare Funzioni di Azure per connettersi al feed di modifiche di Azure Cosmos DB.Use Azure Functions to connect to Azure Cosmos DB change feed. Successivamente è possibile creare funzioni di Azure reattive che vengono attivate a ogni nuovo evento.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: 7a74635551d8416bf60689b1f1403f29883e81bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851361"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>Architetture basate su eventi senza server con Azure Cosmos DB e Funzioni di AzureServerless event-based architectures with Azure Cosmos DB and Azure Functions

Funzioni di Azure offre il modo più semplice per connettersi al feed di [modifiche.](change-feed.md) È possibile creare piccole funzioni di Azure reattive che verranno attivate automaticamente in ogni nuovo evento nel feed di modifiche del contenitore Cosmos di Azure.You can create small reactive Azure Functions that will be automatically triggered on each new event in your Azure Cosmos container's change feed.

![Funzioni basate su eventi senza server che utilizzano il trigger Funzioni di Azure per Cosmos DBServerless event-based Functions working with the Azure Functions trigger for Cosmos DB](./media/change-feed-functions/functions.png)

Con il trigger Funzioni di [Azure per Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md), è possibile sfruttare la funzionalità di scalabilità e rilevamento di eventi affidabile del processore del [feed](./change-feed-processor.md)di modifiche senza la necessità di gestire [l'infrastruttura](./change-feed-processor.md)di lavoro. Concentrarsi sulla logica della funzione di Azure senza preoccuparsi del resto della pipeline di origine degli eventi. È anche possibile combinare il trigger con qualsiasi altra associazione di Funzioni di Azure.You can even mix the Trigger with any other [Azure Functions bindings](../azure-functions/functions-triggers-bindings.md#supported-bindings).

> [!NOTE]
> Attualmente, il trigger Funzioni di Azure per Cosmos DB è supportato per l'uso solo con l'API Core (SQL).

## <a name="requirements"></a>Requisiti

Per implementare un flusso basato su eventi senza server, è necessario:To implement a serverless event-based flow, you need:

* **Contenitore monitorato:** il contenitore monitorato è il contenitore CosmoS di Azure monitorato e archivia i dati da cui viene generato il feed di modifiche. Eventuali inserimenti, gli aggiornamenti al contenitore monitorato si riflettono nel feed di modifiche del contenitore.
* **Contenitore di lease:** il contenitore di lease mantiene lo stato tra più e dinamiche istanze di funzione di Azure senza server e abilita il ridimensionamento dinamico. Questo contenitore di lease può essere creato manualmente o automaticamente dal trigger Funzioni di Azure per Cosmos DB. Per creare automaticamente il contenitore di lease, impostare il flag *CreateLeaseCollectionIfNotExists* nella [configurazione.](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) I contenitori di lease `/id` partizionati devono avere una definizione di chiave di partizione.

## <a name="create-your-azure-functions-trigger-for-cosmos-db"></a>Creare il trigger Funzioni di Azure per Cosmos DBCreate your Azure Functions trigger for Cosmos DB

La creazione della funzione di Azure con un trigger Di Azure Funzioni per Cosmos DB è ora supportata in tutte le integrazioni IDE e CLI di Funzioni di Azure:Creating your Azure Function with an Azure Functions trigger for Cosmos DB is now supported across all Azure Functions IDE and CLI integrations:

* [Estensione di Visual Studio](../azure-functions/functions-develop-vs.md) per gli utenti di Visual Studio.Visual Studio Extension for Visual Studio users.
* [Estensione di codice](/azure/javascript/tutorial-vscode-serverless-node-01) di Visual Studio per gli utenti di Visual Studio Code.
* E infine [gli strumenti Core CLI](../azure-functions/functions-run-local.md#create-func) per un'esperienza indipendente dall'IDE multipiattaforma.

## <a name="run-your-trigger-locally"></a>Eseguire il trigger in localeRun your trigger locally

È possibile eseguire la funzione di [Azure in locale](../azure-functions/functions-develop-local.md) con l'emulatore di database [Cosmos di Azure](./local-emulator.md) per creare e sviluppare i flussi basati su eventi senza server senza una sottoscrizione di Azure o incorrendo in costi.

Se si vogliono testare scenari live nel cloud, è possibile [provare Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) senza alcuna carta di credito o sottoscrizione di Azure richiesta.

## <a name="next-steps"></a>Passaggi successivi

È ora possibile continuare a ottenere ulteriori informazioni sul feed di modifiche negli articoli seguenti:

* [Panoramica del feed di modifiche](change-feed.md)
* [Metodi per leggere il feed di modifiche](read-change-feed.md)
* [Utilizzo della libreria del processore del feed di modifiche](change-feed-processor.md)
* [Uso della libreria del processore dei feed di modifiche di Azure Cosmos DB](change-feed-processor.md)
* [Elaborazione di database serverless con Azure Cosmos DB e Funzioni di Azure](serverless-computing-database.md)
