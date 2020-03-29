---
title: Ripetizione delle operazioni dopo un erroreRetrying tasks after an error
description: Verificare la presenza di errori e riprovare.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
tags: azure-batch
ms.assetid: 16279b23-60ff-4b16-b308-5de000e4c028
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/15/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 94ed936e619461a2dbf7ec837c2d80e21c01c88e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919992"
---
# <a name="detecting-and-handling-batch-service-errors"></a>Rilevamento e gestione degli errori del servizio BatchDetecting and handling Batch service errors

È importante ricordare di verificare la presenza di errori quando si lavora con un'API del servizio REST. Non è raro che si verifichino errori durante l'esecuzione di processi batch.

## <a name="common-errors"></a>Errori comuni 

- Errori di rete: si tratta di richieste che non hanno mai raggiunto Batch o che la risposta Batch non ha raggiunto il client in tempo.
- Errori interni del server: si tratta di risposta HTTP del codice di stato 5xx standard.
- La limitazione delle richieste può causare errori come 429 o 503 risposte HTTP del codice di stato con l'intestazione Retry-after.
- 4xx errori che includono errori come AlreadyExists e InvalidOperation. Ciò significa che la risorsa non si trova nello stato corretto per la transizione di stato.

Per informazioni dettagliate sui vari tipi di codici di errore e codici di errore specifici, vedere [Stato batch e Codici di errore](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes).

## <a name="when-to-retry"></a>Quando riprovare

Le API Batch ti avviseranno se si verifica un errore. Tutti possono essere ritentati e tutti includono un gestore di tentativi globale a tale scopo. È consigliabile utilizzare questo meccanismo integrato.

Dopo un errore, è necessario attendere un po' (alcuni secondi tra i tentativi) prima di riprovare. Se si riprova troppo spesso o troppo rapidamente, il gestore dei tentativi limiterà.

### <a name="for-more-information"></a>Per ulteriori informazioni  

[Le API batch e i](batch-apis-tools.md) collegamenti agli strumenti vengono forniti alle informazioni di riferimento sull'API. L'API .NET, ad esempio, dispone di una [classe RetryPolicyProvider]( https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.retrypolicyprovider?view=azure-dotnet) in cui devono essere specificati i criteri di ripetizione dei tentativi necessari. 

Per informazioni dettagliate su ogni API e sui relativi criteri di ripetizione dei tentativi predefiniti, leggere [Batch Status and Error Codes](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes).
