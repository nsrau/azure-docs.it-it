---
title: Risolvere i problemi relativi ai flussi di dati
description: Informazioni su come risolvere i problemi del flusso di dati in Azure Data Factory.
services: data-factory
ms.author: makromer
author: kromerm
manager: anandsub
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 02/04/2020
ms.openlocfilehash: e2e1ddd031041f49107545cd0b3d3de4eaebcd6d
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472129"
---
# <a name="troubleshoot-data-flows-in-azure-data-factory"></a>Risolvere i problemi relativi ai flussi di dati in Azure Data Factory

Questo articolo illustra i metodi comuni per la risoluzione dei problemi per i flussi di dati in Azure Data Factory.

## <a name="common-errors-and-messages"></a>Messaggi e errori comuni

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Codice di errore: DF-Executor-SourceInvalidPayload
- **Messaggio**: esecuzione dell'anteprima dei dati, del debug e del flusso di dati della pipeline non riuscita. il contenitore non esiste
- **Cause**: quando il set di dati contiene un contenitore che non esiste nello spazio di archiviazione
- **Raccomandazione**: assicurarsi che il contenitore a cui si fa riferimento nel set di dati esista o accessibile.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Codice di errore: DF-Executor-SystemImplicitCartesian

- **Messaggio**: il prodotto cartesiano implicito per Inner join non è supportato. usare cross join. Le colonne utilizzate nel join devono creare una chiave univoca per le righe.
- **Cause**: il prodotto cartesiano implicito per Inner join tra piani logici non è supportato. Se le colonne utilizzate nel join creano la chiave univoca, sono necessarie almeno una colonna da entrambi i lati della relazione.
- **Raccomandazione**: per i join non basati sull'uguaglianza è necessario optare per il cross join personalizzato.

### <a name="error-code-df-executor-systeminvalidjson"></a>Codice di errore: DF-Executor-SystemInvalidJson

- **Messaggio**: errore di analisi JSON, codifica non supportata o su più righe
- **Cause**: possibili problemi con il file JSON: codifica non supportata, byte danneggiati o uso dell'origine JSON come documento singolo su molte righe annidate
- **Raccomandazione**: verificare che la codifica del file JSON sia supportata. Nella trasformazione di origine che usa un set di dati JSON, espandere ' JSON Settings ' e attivare ' Single Document '.
 
### <a name="error-code-df-executor-broadcasttimeout"></a>Codice di errore: DF-Executor-BroadcastTimeout

- **Messaggio**: errore di timeout di broadcast join, assicurarsi che flusso broadcast produca dati entro 60 secondi nelle esecuzioni di debug e 300 secondi nelle esecuzioni dei processi
- **Cause**: la trasmissione ha un timeout predefinito di 60 secondi nelle esecuzioni di debug e di 300 secondi nelle esecuzioni del processo. Il flusso scelto per la trasmissione sembra grande per produrre dati entro questo limite.
- **Raccomandazione**: evitare di trasmettere flussi di dati di grandi dimensioni in cui l'elaborazione può richiedere più di 60 secondi. Scegliere invece un flusso più piccolo da trasmettere. Le tabelle SQL/DW di grandi dimensioni e i file di origine sono in genere candidati non validi.

### <a name="error-code-df-executor-conversion"></a>Codice di errore: DF-Executor-Conversion

- **Messaggio**: conversione in una data o ora non riuscita a causa di un carattere non valido
- **Cause**: i dati non sono nel formato previsto
- **Raccomandazione**: usare il tipo di dati corretto

### <a name="error-code-df-executor-invalidcolumn"></a>Codice di errore: DF-Executor-InvalidColumn

- **Message**: è necessario specificare il nome della colonna nella query, impostare un alias se si usa una funzione SQL
- **Cause**: non è stato specificato alcun nome di colonna
- **Raccomandazione**: impostare un alias se si usa una funzione SQL, ad esempio min ()/Max () e così via.

## <a name="general-troubleshooting-guidance"></a>Indicazioni generali per la risoluzione dei problemi

1. Verificare lo stato delle connessioni del set di dati. In ogni trasformazione di origine e sink visitare il servizio collegato per ogni set di dati in uso e testare le connessioni.
1. Verificare lo stato delle connessioni di file e tabelle dalla finestra di progettazione del flusso di dati. Attivare il debug e fare clic su Anteprima dati nelle trasformazioni di origine per assicurarsi di poter accedere ai dati.
1. Se tutto sembra corretto dall'anteprima dei dati, passare alla finestra di progettazione della pipeline e inserire il flusso di dati in un'attività della pipeline. Eseguire il debug della pipeline per un test end-to-end.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla risoluzione dei problemi, provare a usare le risorse seguenti:
*  [Blog di Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Richieste di funzionalità Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Video su Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Forum Stack Overflow per Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informazioni su Twitter sui Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Guida alle prestazioni dei flussi di dati di mapping di ADF](concepts-data-flow-performance.md)
