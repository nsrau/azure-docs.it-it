---
title: Gestione degli errori di funzioni di Azure e istruzioni per i tentativi
description: Informazioni su come gestire gli errori e riprovare gli eventi in funzioni di Azure con collegamenti a errori di binding specifici.
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: cshoe
ms.openlocfilehash: e52babd5b0612b4590f1422a941b25ba30c3f169
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93284458"
---
# <a name="azure-functions-error-handling-and-retries"></a>Gestione degli errori e tentativi di funzioni di Azure

La gestione degli errori in funzioni di Azure è importante per evitare la perdita di dati, eventi mancanti e per monitorare l'integrità dell'applicazione.

Questo articolo descrive le strategie generali per la gestione degli errori insieme ai collegamenti ad errori specifici dell'associazione.

## <a name="handling-errors"></a>Gestione degli errori

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-retries.md)]

## <a name="binding-error-codes"></a>Codici degli errori di associazione

Quando si esegue l'integrazione con i servizi di Azure, gli errori possono provenire dalle API dei servizi sottostanti. Le informazioni relative agli errori specifici dell'associazione sono disponibili nella sezione **eccezioni e codici restituiti** degli articoli seguenti:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Archiviazione BLOB](functions-bindings-storage-blob-output.md#exceptions-and-return-codes)

+ [Hub eventi](functions-bindings-event-hubs-output.md#exceptions-and-return-codes)

+ [Hub Internet](functions-bindings-event-iot-output.md#exceptions-and-return-codes)

+ [Hub di notifica di Azure](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Archiviazione code](functions-bindings-storage-queue-output.md#exceptions-and-return-codes)

+ [Bus di servizio](functions-bindings-service-bus-output.md#exceptions-and-return-codes)

+ [Archiviazione tabelle](functions-bindings-storage-table-output.md#exceptions-and-return-codes)
