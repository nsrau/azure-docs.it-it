---
title: Risolvere i problemi relativi ai flussi di datiTroubleshoot
description: Informazioni su come risolvere i problemi relativi al flusso di dati in Azure Data Factory.Learn how to troubleshoot data flow issues in Azure Data Factory.
services: data-factory
ms.author: makromer
author: kromerm
manager: anandsub
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 02/04/2020
ms.openlocfilehash: e2e1ddd031041f49107545cd0b3d3de4eaebcd6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472129"
---
# <a name="troubleshoot-data-flows-in-azure-data-factory"></a>Risolvere i problemi relativi ai flussi di dati in Azure Data FactoryTroubleshoot data flows in Azure Data Factory

Questo articolo illustra i metodi di risoluzione dei problemi comuni per i flussi di dati in Azure Data Factory.This article explores common troubleshooting methods for data flows in Azure Data Factory.

## <a name="common-errors-and-messages"></a>Errori e messaggi comuni

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Codice di errore: DF-Executor-SourceInvalidPayload
- **Messaggio:** Anteprima dei dati, debug e esecuzione del flusso di dati della pipeline non riuscita perché il contenitore non esiste
- **Cause**: quando il set di dati contiene un contenitore che non esiste nell'archivio
- **Consiglio:** assicurarsi che il contenitore a cui viene fatto riferimento nel set di dati esista o accessibile.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Codice di errore: DF-Executor-SystemImplicitCartesian

- **Messaggio:** Il prodotto cartesiale implicito per il join INNER non è supportato, utilizzare invece CROSS JOIN. Le colonne usate nel join devono creare una chiave univoca per le righe.
- **Cause**: Il prodotto cartesiale implicito per l'unione INNER tra piani logici non è supportato. Se le colonne utilizzate nel join creano la chiave univoca, sono necessarie almeno una colonna da entrambi i lati della relazione.
- **Consiglio:** per i join basati sulla non uguaglianza è necessario optare per CUSTOM CROSS JOIN.

### <a name="error-code-df-executor-systeminvalidjson"></a>Codice di errore: DF-Executor-SystemInvalidJson

- **Messaggio**: Errore di analisi JSON, codifica non supportata o multilinea
- **Cause**: Possibili problemi con il file JSON: codifica non supportata, byte danneggiati o utilizzo dell'origine JSON come singolo documento in molte righe nidificate
- **Consiglio:** verificare che la codifica del file JSON sia supportata. Nella trasformazione Origine che usa un set di dati JSON espandere 'Impostazioni JSON' e attivare 'Documento singolo'.
 
### <a name="error-code-df-executor-broadcasttimeout"></a>Codice di errore: DF-Executor-BroadcastTimeout

- **Messaggio:** Errore di timeout di join di trasmissione, assicurarsi che il flusso di trasmissione produca dati entro 60 secondi nelle esecuzioni di debug e 300 secondi nelle esecuzioni dei processi
- **Cause**: Broadcast ha un timeout predefinito di 60 secondi nelle esecuzioni di debug e 300 secondi nelle esecuzioni dei processi. Stream scelto per la trasmissione sembra grande per produrre dati entro questo limite.
- **Consiglio**: evitare di trasmettere flussi di dati di grandi dimensioni in cui l'elaborazione può richiedere più di 60 secondi. Scegliere invece un flusso più piccolo da trasmettere. Le tabelle SQL/DW di grandi dimensioni e i file di origine sono in genere candidati non valida.

### <a name="error-code-df-executor-conversion"></a>Codice di errore: DF-Executor-Conversion

- **Messaggio**: La conversione in una data o in un'ora non è riuscita a causa di un carattere non valido
- **Cause**: I dati non sono nel formato previsto
- **Raccomandazione**: Utilizzare il tipo di dati corretto

### <a name="error-code-df-executor-invalidcolumn"></a>Codice di errore: DF-Executor-InvalidColumn

- **Messaggio**: Il nome della colonna deve essere specificato nella query, impostare un alias se si utilizza una funzione SQL
- **Cause**: non è stato specificato alcun nome di colonna
- **Raccomandazione**: Impostare un alias se si utilizza una funzione SQL come min()/max(), ecc.

## <a name="general-troubleshooting-guidance"></a>Indicazioni generali per la risoluzione dei problemi

1. Controllare lo stato delle connessioni del set di dati. In ogni trasformazione Di origine e sink visitare il servizio collegato per ogni set di dati in uso e testare le connessioni.
1. Controllare lo stato delle connessioni a file e tabelle da Progettazione flussi di dati. Attivare Debug e fare clic su Anteprima dati nelle trasformazioni Origine per assicurarsi di poter accedere ai dati.
1. Se tutto sembra buono dall'anteprima dei dati, passare a Progettazione pipeline e inserire il flusso di dati in un'attività della pipeline. Eseguire il debug della pipeline per un test end-to-end.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla risoluzione dei problemi, prova queste risorse:For more troubleshooting help, try these resources:
*  [Blog di Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Richieste di funzionalità di Data FactoryData Factory feature requests](https://feedback.azure.com/forums/270578-data-factory)
*  [Video di Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Forum sull'overflow dello stack per Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informazioni su Twitter su Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Guida alle prestazioni dei flussi di dati di mapping ADFADF mapping data flows Performance Guide](concepts-data-flow-performance.md)
