---
title: Risolvere i problemi di mapping dei flussi di dati
description: Informazioni su come risolvere problemi relativi ai flussi di dati in Azure Data Factory.
services: data-factory
ms.author: makromer
author: kromerm
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/11/2020
ms.openlocfilehash: e52432c01e649754116fcd0420fa52ae6c4e3733
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2020
ms.locfileid: "90031858"
---
# <a name="troubleshoot-mapping-data-flows-in-azure-data-factory"></a>Risolvere i problemi relativi al mapping dei flussi di dati in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Questo articolo illustra i metodi comuni per la risoluzione dei problemi relativi al mapping dei flussi di dati in Azure Data Factory.

## <a name="common-errors-and-messages"></a>Errori comuni e messaggi

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Codice errore: DF-Executor-SourceInvalidPayload
- **Messaggio**: data preview, debug, and pipeline data flow execution failed because container does not exist (esecuzione del flusso di dati di anteprima, debug e pipeline non riuscita. Il contenitore non esiste)
- **Cause**: il set di dati contiene un contenitore che non esiste nella risorsa di archiviazione
- **Raccomandazione**: verificare che il contenitore a cui si fa riferimento nel set di dati esista o sia accessibile.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Codice errore: DF-Executor-SystemImplicitCartesian

- **Messaggio**: implicit cartesian product for INNER join is not supported, use CROSS JOIN instead (il prodotto cartesiano implicito per INNER JOIN non è supportato. Usare CROSS JOIN). Le colonne usate nel join devono creare una chiave univoca per le righe.
- **Cause**: il prodotto cartesiano implicito per INNER JOIN tra i piani logici non è supportato. Se le colonne utilizzate nel join creano la chiave univoca, sono necessarie almeno una colonna da entrambi i lati della relazione.
- **Raccomandazione**: per i join basati sulla non uguaglianza è necessario scegliere un CROSS JOIN personalizzato.

### <a name="error-code-df-executor-systeminvalidjson"></a>Codice errore: DF-Executor-SystemInvalidJson

- **Messaggio**: JSON parsing error, unsupported encoding or multiline (errore di analisi JSON, codifica non supportata o su più righe)
- **Cause**: possibili problemi con il file JSON: codifica non supportata, byte danneggiati o uso dell'origine JSON come documento singolo su molte righe annidate
- **Raccomandazione**: verificare che la codifica del file JSON sia supportata. Nella trasformazione Source (Origine) che usa un set di dati JSON, espandere "JSON Settings" (Impostazioni JSON) e attivare "Single Document" (Documento singolo).
 
### <a name="error-code-df-executor-broadcasttimeout"></a>Codice errore: DF-Executor-BroadcastTimeout

- **Messaggio**: Broadcast join timeout error, make sure broadcast stream produces data within 60 secs in debug runs and 300 secs in job runs (errore di timeout del join di trasmissione. Assicurarsi che il flusso di trasmissione generi i dati entro 60 secondi nelle esecuzioni di debug ed entro 300 secondi nelle esecuzioni di processi)
- **Cause**: la trasmissione ha un timeout predefinito di 60 secondi nelle esecuzioni di debug e di 300 secondi durante l'esecuzione del processo. Il flusso scelto per la trasmissione sembra troppo grande per produrre dati entro questo limite.
- **Raccomandazione**: selezionare la scheda Optimize (Ottimizza) per le trasformazioni del flusso di dati per Join, Exists (Esistente) e Lookup (Ricerca). L'opzione predefinita per la trasmissione è "Auto". Se è impostato "auto" o se si imposta manualmente il lato sinistro o destro su broadcast in "Fixed", è possibile impostare una configurazione di Azure Integration Runtime più grande o disattivare broadcast. L'approccio consigliato per ottenere prestazioni ottimali nei flussi di dati consiste nel consentire a Spark di eseguire la trasmissione tramite l'opzione "Auto" e usare Azure IR ottimizzato per la memoria.

Se si esegue il flusso di dati in un'esecuzione di test di debug da un'esecuzione di pipeline di debug, è possibile che si verifichi questa condizione con maggiore frequenza. Questo perché ADF limita il timeout di trasmissione a 60 secondi per mantenere un'esperienza di debug più veloce. Se si desidera estendere il valore al timeout di 300 secondi da un'esecuzione attivata, è possibile utilizzare l'opzione debug > utilizza Runtime attività per utilizzare le Azure IR definite nell'attività Esegui pipeline flusso di dati.

### <a name="error-code-df-executor-conversion"></a>Codice errore: DF-Executor-Conversion

- **Messaggio**: converting to a date or time failed due to an invalid character (conversione in una data o ora non riuscita a causa di un carattere non valido)
- **Cause**: i dati non sono nel formato previsto
- **Raccomandazione**: usare il tipo di dati corretto

### <a name="error-code-df-executor-invalidcolumn"></a>Codice errore: DF-Executor-InvalidColumn

- **Messaggio**: column name needs to be specified in the query, set an alias if using a SQL function (specificare il nome della colonna nella query, impostare un alias se si usa una funzione SQL)
- **Cause**: non è stato specificato un nome per la colonna
- **Raccomandazione**: impostare un alias se si usa una funzione SQL, ad esempio min()/max() e così via.

 ### <a name="error-code-df-executor-drivererror"></a>Codice di errore: DF-Executor-DriverError
- **Messaggio**: INT96 è di tipo timestamp legacy che non è supportato dal flusso di file ADF. Si consiglia di aggiornare il tipo di colonna ai tipi più recenti.
- **Cause**: errore del driver
- **Raccomandazione**: INT96 è di tipo timestamp legacy, che non è supportato dal flusso di file ADF. Provare ad aggiornare il tipo di colonna ai tipi più recenti.

 ### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>Codice di errore: DF-Executor-BlockCountExceedsLimitError
- **Messaggio**: il conteggio dei blocchi di cui non è stato eseguito il commit non può superare il limite massimo di 100.000 blocchi. Controllare la configurazione del BLOB.
- **Cause**: possono essere presenti al massimo 100.000 blocchi di cui non è stato eseguito il commit in un BLOB.
- **Consiglio**: per informazioni dettagliate, contattare il team del prodotto Microsoft in merito a questo problema

 ### <a name="error-code-df-executor-partitiondirectoryerror"></a>Codice di errore: DF-Executor-PartitionDirectoryError
- **Messaggio**: il percorso di origine specificato ha più directory partizionate, ad esempio <Source Path> /<directory radice della partizione 1>/a = 10/b = 20, <Source Path> /<directory radice partizione 2>/c = 10/d = 30) o directory partizionata con altro file o directory non partizionata (ad esempio <Source Path> /<directory radice partizione 1>/a = 10/b = 20, <Source Path> /Directory 2/file1), rimuovere la directory radice della partizione dal percorso di origine e leggerla tramite una trasformazione di origine separata.
- **Cause**: il percorso di origine contiene più directory partizionate o una directory partizionata con altro file o directory non partizionata.
- **Raccomandazione**: rimuovere la directory radice partizionata dal percorso di origine e leggerla tramite una trasformazione di origine separata.

 ### <a name="error-code-df-executor-outofmemoryerror"></a>Codice di errore: DF-Executor-OutOfMemoryError
- **Messaggio**: si è verificata un problema di memoria insufficiente durante l'esecuzione. riprovare a usare un runtime di integrazione con un numero di core maggiore e/o un tipo di calcolo con ottimizzazione per la memoria
- **Cause**: memoria insufficiente per il cluster
- **Raccomandazione**: i cluster di debug sono destinati a scopi di sviluppo. Sfruttare il campionamento dei dati, il tipo di calcolo appropriato e le dimensioni per eseguire il payload. Per ottenere prestazioni ottimali, vedere la [Guida alle prestazioni del flusso di dati di mapping](concepts-data-flow-performance.md) per l'ottimizzazione.

 ### <a name="error-code-df-executor-illegalargument"></a>Codice di errore: DF-Executor-illegalArgument
- **Messaggio**: assicurarsi che la chiave di accesso nel servizio collegato sia corretta
- **Cause**: il nome dell'account o la chiave di accesso non è corretta
- **Raccomandazione**: assicurarsi che il nome dell'account o la chiave di accesso specificata nel servizio collegato sia corretta. 

 ### <a name="error-code-df-executor-invalidtype"></a>Codice di errore: DF-Executor-InvalidType
- **Messaggio**: assicurarsi che il tipo di parametro corrisponda al tipo di valore passato. Il passaggio di parametri float dalle pipeline non è attualmente supportato.
- **Cause**: tipi di dati non compatibili tra il tipo dichiarato e il valore del parametro effettivo
- **Raccomandazione**: controllare che i valori dei parametri passati in un flusso di dati corrispondano al tipo dichiarato.

 ### <a name="error-code-df-executor-columnunavailable"></a>Codice di errore: DF-Executor-ColumnUnavailable
- **Messaggio**: il nome della colonna utilizzato nell'espressione non è disponibile o non è valido
- **Cause**: nome di colonna non valido o non disponibile usato nelle espressioni
- **Raccomandazione**: controllare i nomi di colonna usati nelle espressioni

 ### <a name="error-code-df-executor-parseerror"></a>Codice di errore: DF-Executor-ParseError
- **Messaggio**: non è possibile analizzare l'espressione
- **Cause**: l'espressione presenta errori di analisi dovuti alla formattazione
- **Raccomandazione**: controllare la formattazione nell'espressione

### <a name="error-code-getcommand-outputasync-failed"></a>Codice errore: GetCommand OutputAsync failed

- **Messaggio**: durante il debug di Flusso di dati e l'anteprima dei dati: GetCommand OutputAsync failed with ... (l'operazione GetCommand OutputAsync non è riuscita con ...)
- **Cause**: si tratta di un errore del servizio back-end. È possibile ripetere l'operazione e anche riavviare la sessione di debug.
- **Raccomandazione**: se la ripetizione dell'operazione e il riavvio non risolvono il problema, contattare il supporto tecnico.

### <a name="error-code-hit-unexpected-exception-and-execution-failed"></a>Codice errore: Hit unexpected exception and execution failed

- **Messaggio**: durante l'esecuzione dell'attività Flusso di dati: hit unexpected exception and execution failed (eccezione imprevista ed esecuzione non riuscita).
- **Cause**: si tratta di un errore del servizio back-end. È possibile ripetere l'operazione e anche riavviare la sessione di debug.
- **Raccomandazione**: se la ripetizione dell'operazione e il riavvio non risolvono il problema, contattare il supporto tecnico.

### <a name="error-code-debug-data-preview-no-output-data-on-join"></a>Codice di errore: debug dei dati in anteprima senza dati di output in join

- **Messaggio**: numero elevato di valori null o valori mancanti che potrebbero essere causati dalla presenza di un numero insufficiente di righe campionate. Provare ad aggiornare il limite di righe di debug e ad aggiornare i dati.
- **Cause**: la condizione di join non corrisponde ad alcuna riga o ha prodotto un numero elevato di valori null durante l'anteprima dei dati.
- **Suggerimento**: passare a impostazioni di debug e aumentare il numero di righe nel limite di righe di origine. Assicurarsi di aver selezionato e Azure IR con un cluster di flussi di dati sufficientemente grande per gestire più dati.


## <a name="general-troubleshooting-guidance"></a>Indicazioni generali sulla risoluzione dei problemi

1. Verificare lo stato delle connessioni del set di dati. In ogni trasformazione Source (Origine) e Sink controllare il servizio collegato per ogni set di dati in uso e testare le connessioni.
1. Verificare lo stato delle connessioni di file e tabelle dalla finestra di progettazione del flusso di dati. Attivare il debug e fare clic su Data Preview (Anteprima dati) nelle trasformazioni Source (Origine) per assicurarsi di poter accedere ai dati.
1. Se dall'anteprima dei dati sembra tutto corretto, passare alla finestra di progettazione della pipeline e inserire il flusso di dati in un'attività della pipeline. Eseguire il debug della pipeline per un test end-to-end.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla risoluzione dei problemi, usare le risorse seguenti:
*  [Blog di Data Factory](https://techcommunity.microsoft.com/t5/azure-data-factory/bg-p/AzureDataFactoryBlog)
*  [Richieste di funzionalità di Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Video di Azure](https://www.youtube.com/channel/UC2S0k7NeLcEm5_IhHUwpN0g/videos)
*  [Pagina delle domande di Domande e risposte Microsoft](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Forum Stack Overflow per Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informazioni su Twitter su Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Guida sulle prestazioni dei flussi di dati di mapping di Azure Data Factory](concepts-data-flow-performance.md)
