---
title: Linee guida per la gestione degli errori in funzioni di Azure
description: Informazioni su come gestire gli errori in funzioni di Azure con collegamenti a errori di binding specifici.
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: cshoe
ms.openlocfilehash: 0da8322a57d925608f7b52054c7a52e6cb3e5d06
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484656"
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

+ [Hub eventi](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [Hub di notifica di Azure](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Archiviazione code](functions-bindings-storage-queue-output.md#exceptions-and-return-codes)

+ [Bus di servizio](functions-bindings-service-bus-output.md#exceptions-and-return-codes)

+ [Archiviazione tabelle](functions-bindings-storage-table.md#exceptions-and-return-codes)
