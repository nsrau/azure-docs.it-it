---
title: Risolvere i problemi relativi a codici errore di Analisi di flusso di Azure
description: Risolvere i problemi di Analisi di flusso di Azure con i codici errore interni.
ms.author: mamccrea
author: mamccrea
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 9dc3d873ddfef9a729f583cd914ca4700d562ff3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86045230"
---
# <a name="azure-stream-analytics-internal-error-codes"></a>Codici errore interni di Analisi di flusso di Azure

È possibile usare log attività e log risorse per facilitare il debug di comportamenti imprevisti del processo di Analisi di flusso di Azure. Questo articolo riporta la descrizione di ogni codice errore interno. Gli errori interni sono errori generici che vengono generati all'interno della piattaforma di Analisi di flusso quando Analisi di flusso non è in grado di distinguere se l'errore è un errore di disponibilità interna o un bug del sistema.

## <a name="cosmosdboutputbatchsizetoolarge"></a>CosmosDBOutputBatchSizeTooLarge

* **Causa**: Le dimensioni del batch per la scrittura in Cosmos DB sono troppo elevate. 
* **Raccomandazione**: Riprovare con dimensioni del batch inferiori.

## <a name="next-steps"></a>Passaggi successivi

* [Risolvere i problemi delle connessioni di input](stream-analytics-troubleshoot-input.md)
* [Risolvere i problemi degli output di Analisi di flusso di Azure](stream-analytics-troubleshoot-output.md)
* [Risolvere i problemi delle query di Analisi di flusso di Azure](stream-analytics-troubleshoot-query.md)
* [Risolvere i problemi di Analisi di flusso di Azure usando i log delle risorse](stream-analytics-job-diagnostic-logs.md)
* [Errori dei dati di Analisi di flusso di Azure](data-errors.md)