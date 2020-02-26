---
title: Linee guida per la gestione degli errori in funzioni di Azure
description: Informazioni su come gestire gli errori in funzioni di Azure con collegamenti a errori di binding specifici.
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: cshoe
ms.openlocfilehash: befdb4a8cceaef18961c1e9297e23ed5d405ff50
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77586190"
---
# <a name="azure-functions-error-handling"></a>Gestione degli errori di Funzioni di Azure

La gestione degli errori in funzioni di Azure è importante per evitare la perdita di dati, eventi mancanti e per monitorare l'integrità dell'applicazione.

Questo articolo descrive le strategie generali per la gestione degli errori insieme ai collegamenti ad errori specifici dell'associazione.

## <a name="handling-errors"></a>Gestione degli errori

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

## <a name="binding-error-codes"></a>Codici degli errori di associazione

Quando si esegue l'integrazione con i servizi di Azure, gli errori possono provenire dalle API dei servizi sottostanti. Le informazioni relative agli errori specifici dell'associazione sono disponibili nella sezione **eccezioni e codici restituiti** degli articoli seguenti:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Archiviazione BLOB](functions-bindings-storage-blob-output.md#exceptions-and-return-codes)

+ [Hub eventi](functions-bindings-event-hubs-output.md#exceptions-and-return-codes)

+ [Hub Internet](functions-bindings-event-iot-output.md#exceptions-and-return-codes)

+ [Hub di notifica di Azure](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Archiviazione code](functions-bindings-storage-queue-output.md#exceptions-and-return-codes)

+ [Bus di servizio](functions-bindings-service-bus-output.md#exceptions-and-return-codes)

+ [Archiviazione tabelle](functions-bindings-storage-table.md#exceptions-and-return-codes)
