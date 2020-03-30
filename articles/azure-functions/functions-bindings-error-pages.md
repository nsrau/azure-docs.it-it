---
title: Indicazioni sulla gestione degli errori di Funzioni di AzureAzure Functions error handling guidance
description: Informazioni su come gestire gli errori in Funzioni di Azure con collegamenti a errori di associazione specifici.
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: cshoe
ms.openlocfilehash: befdb4a8cceaef18961c1e9297e23ed5d405ff50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586190"
---
# <a name="azure-functions-error-handling"></a>Gestione degli errori di Funzioni di Azure

La gestione degli errori in Funzioni di Azure è importante per evitare perdite di dati, eventi persi e per monitorare l'integrità dell'applicazione.

In questo articolo vengono descritte le strategie generali per la gestione degli errori insieme ai collegamenti agli errori specifici dell'associazione.

## <a name="handling-errors"></a>Gestione degli errori

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

## <a name="binding-error-codes"></a>Codici degli errori di associazione

Durante l'integrazione con i servizi di Azure, gli errori possono derivare dalle API dei servizi sottostanti. Le informazioni relative agli errori specifici del binding sono disponibili nella sezione **Eccezioni e codici restituiti** dei seguenti articoli:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Archiviazione BLOB](functions-bindings-storage-blob-output.md#exceptions-and-return-codes)

+ [Hub eventi](functions-bindings-event-hubs-output.md#exceptions-and-return-codes)

+ [Hub IoT](functions-bindings-event-iot-output.md#exceptions-and-return-codes)

+ [Hub di notifica](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Archiviazione code](functions-bindings-storage-queue-output.md#exceptions-and-return-codes)

+ [Bus di servizio](functions-bindings-service-bus-output.md#exceptions-and-return-codes)

+ [Archiviazione tabelle](functions-bindings-storage-table.md#exceptions-and-return-codes)
