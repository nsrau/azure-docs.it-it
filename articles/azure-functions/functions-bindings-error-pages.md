---
title: Linee guida per la gestione degli errori di Funzioni di Azure | Microsoft Docs
description: Indicazioni generali per la gestione degli errori che si verificano durante l'esecuzione delle funzioni e collegamenti agli argomenti sugli errori specifici.
services: functions
cloud: ''
documentationcenter: ''
author: craigshoemaker
manager: gwallace
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 02/01/2018
ms.author: cshoe
ms.openlocfilehash: bc0ffec1bfe55ee81573feffc58e1adb11421eec
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667437"
---
# <a name="azure-functions-error-handling"></a>Gestione degli errori di Funzioni di Azure

Questo argomento offre indicazioni generali per la gestione degli errori che si verificano durante l'esecuzione delle funzioni. Sono inoltre disponibili collegamenti ad argomenti che descrivono gli errori di associazione specifici che possono verificarsi. 

## <a name="handling-errors-in-functions"></a>Gestione degli errori nelle funzioni
[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

 
## <a name="binding-error-codes"></a>Codici degli errori di associazione

In caso di integrazione con i servizi di Azure, è possibile che vengano generati errori originati dalle API dei servizi sottostanti. I collegamenti alla documentazione sui codici di errore per questi servizi sono disponibili nella sezione **Eccezioni e codici restituiti** degli argomenti di riferimento su trigger e associazione seguenti:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Archiviazione BLOB](functions-bindings-storage-blob.md#exceptions-and-return-codes)

+ [Hub eventi](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [Hub di notifica di Azure](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Archiviazione code](functions-bindings-storage-queue.md#exceptions-and-return-codes)

+ [Bus di servizio](functions-bindings-service-bus.md#exceptions-and-return-codes)

+ [Archiviazione tabelle](functions-bindings-storage-table.md#exceptions-and-return-codes)
