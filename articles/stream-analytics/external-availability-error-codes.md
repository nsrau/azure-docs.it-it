---
title: Codici errore di disponibilità esterna - Analisi di flusso di Azure
description: Risolvere i problemi di Analisi di flusso di Azure con i codici errore di disponibilità esterna.
ms.author: mamccrea
author: mamccrea
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: c70a3a2d0630148a077f9c149ba40d48a6b7c0ae
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045281"
---
# <a name="azure-stream-analytics-external-availability-error-codes"></a>Codici errore di disponibilità esterna di Analisi di flusso di Azure

È possibile usare log attività e log risorse per facilitare il debug di comportamenti imprevisti del processo di Analisi di flusso di Azure. Questo articolo riporta la descrizione di ogni codice errore di disponibilità esterna. Gli errori di disponibilità esterna si verificano quando un servizio dipendente non è disponibile.

## <a name="externalserviceunavailable"></a>ExternalServiceUnavailable

* **Causa**: Un servizio è temporaneamente non disponibile.
* **Raccomandazione**: Analisi di flusso continuerà a tentare di raggiungere il servizio.

## <a name="eventhubmessagingerror"></a>EventHubMessagingError

* **Causa**: Analisi di flusso ha rilevato un errore durante la comunicazione con EventHub. 


## <a name="next-steps"></a>Passaggi successivi

* [Risolvere i problemi delle connessioni di input](stream-analytics-troubleshoot-input.md)
* [Risolvere i problemi degli output di Analisi di flusso di Azure](stream-analytics-troubleshoot-output.md)
* [Risolvere i problemi delle query di Analisi di flusso di Azure](stream-analytics-troubleshoot-query.md)
* [Risolvere i problemi di Analisi di flusso di Azure usando i log delle risorse](stream-analytics-job-diagnostic-logs.md)
* [Errori dei dati di Analisi di flusso di Azure](data-errors.md)
