---
title: Risolvere i problemi relativi ai flussi di dati
description: Informazioni su come risolvere i problemi del flusso di dati in Azure Data Factory.
services: data-factory
ms.author: makromer
author: kromerm
manager: anandsub
ms.service: data-factory
ms.topic: troubleshooting
ms.custom: seo-lt-2019
ms.date: 12/06/2019
ms.openlocfilehash: b972bbeac419d88afdd257a7fd19587dbaedf0d9
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930164"
---
# <a name="troubleshoot-azure-data-factory-data-flows"></a>Risolvere i problemi di Azure Data Factory flussi di dati

Questo articolo illustra i metodi comuni per la risoluzione dei problemi per i flussi di dati in Azure Data Factory.

## <a name="common-errors-and-messages"></a>Messaggi e errori comuni

### <a name="error-message-df-sys-01-shadeddatabricksorgapachehadoopfsazureazureexception-commicrosoftazurestoragestorageexception-the-specified-container-does-not-exist"></a>Messaggio di errore: DF-SYS-01: shader. databricks. org. Apache. Hadoop. FS. Azure. Azureexception: com. Microsoft. Azure. storage. StorageException: il contenitore specificato non esiste.

- **Sintomi**: l'esecuzione dell'anteprima dei dati, del debug e del flusso di dati della pipeline non riesce perché il contenitore non esiste

- **Motivo**: quando il set di dati contiene un contenitore che non esiste nello spazio di archiviazione

- **Soluzione**: assicurarsi che il contenitore a cui si fa riferimento nel set di dati esista

### <a name="error-message-df-sys-01-javalangassertionerror-assertion-failed-conflicting-directory-structures-detected-suspicious-paths"></a>Messaggio di errore: DF-SYS-01: Java. lang. AssertionError: Assertion Failed: sono state rilevate strutture di directory in conflitto. Percorsi sospetti

- **Sintomi**: quando si usano i caratteri jolly nella trasformazione origine con i file parquet

- **Cause**: sintassi con caratteri jolly non corretta o non valida

- **Soluzione**: controllare la sintassi con caratteri jolly utilizzata nelle opzioni di trasformazione di origine

### <a name="error-message-df-src-002-container-container-name-is-required"></a>Messaggio di errore: DF-SRC-002:' container ' (nome contenitore) obbligatorio

- **Sintomi**: l'esecuzione dell'anteprima dei dati, del debug e del flusso di dati della pipeline non riesce perché il contenitore non esiste

- **Motivo**: quando il set di dati contiene un contenitore che non esiste nello spazio di archiviazione

- **Soluzione**: assicurarsi che il contenitore a cui si fa riferimento nel set di dati esista

### <a name="error-message-df-uni-001-primarykeyvalue-has-incompatible-types-integertype-and-stringtype"></a>Messaggio di errore: DF-UNI-001: i tipi di PrimaryKeyValue sono incompatibili IntegerType e StringType

- **Sintomi**: l'esecuzione dell'anteprima dei dati, del debug e del flusso di dati della pipeline non riesce perché il contenitore non esiste

- **Cause**: si verifica quando si tenta di inserire un tipo di chiave primaria errato nei sink di database

- **Soluzione**: utilizzare una colonna derivata per eseguire il cast della colonna utilizzata per la chiave primaria nel flusso di dati in modo che corrisponda al tipo di dati del database di destinazione

### <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-the-tcpip-connection-to-the-host-xxxxxdatabasewindowsnet-port-1433-has-failed-error-xxxxdatabasewindowsnet-verify-the-connection-properties-make-sure-that-an-instance-of-sql-server-is-running-on-the-host-and-accepting-tcpip-connections-at-the-port-make-sure-that-tcp-connections-to-the-port-are-not-blocked-by-a-firewall"></a>Messaggio di errore: DF-SYS-01: com. Microsoft. SqlServer. JDBC. SQLServerException: la connessione TCP/IP alla porta xxxxx.database.windows.net host 1433 non è riuscita. Errore: "xxxx.database.windows.net. Verificare le proprietà di connessione. Assicurarsi che un'istanza di SQL Server sia in esecuzione nell'host e accetti le connessioni TCP/IP sulla porta. Verificare che le connessioni TCP alla porta non siano bloccate da un firewall ".

- **Sintomi**: non è possibile visualizzare in anteprima i dati o eseguire la pipeline con l'origine o il sink del database

- **Motivo**: il database è protetto da firewall

- **Soluzione**: aprire il firewall accesso al database

### <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-there-is-already-an-object-named-xxxxxx-in-the-database"></a>Messaggio di errore: DF-SYS-01: com. Microsoft. SqlServer. JDBC. SQLServerException: esiste già un oggetto denominato ' XXXXXX ' nel database.

- **Sintomi**: il sink non riesce a creare la tabella

- **Causa**: nel database di destinazione è già presente un nome di tabella con lo stesso nome definito nell'origine o nel set di dati

- **Soluzione**: modificare il nome della tabella che si sta tentando di creare

### <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-string-or-binary-data-would-be-truncated"></a>Messaggio di errore: DF-SYS-01: com. Microsoft. SqlServer. JDBC. SQLServerException: i dati di tipo String o binary verrebbero troncati. 

- **Sintomi**: durante la scrittura di dati in un sink SQL, il flusso di dati non riesce nell'esecuzione della pipeline con possibile errore di troncamento.

- **Causa**: un campo del flusso di dati mappato a una colonna nel database SQL non è sufficientemente ampio per archiviare il valore, causando la generazione di questo errore da parte del driver SQL

- **Soluzione**: è possibile ridurre la lunghezza dei dati per le colonne stringa usando ```left()``` in una colonna derivata o implementare il [modello "riga di errore".](how-to-data-flow-error-rows.md)

### <a name="error-message-since-spark-23-the-queries-from-raw-jsoncsv-files-are-disallowed-when-the-referenced-columns-only-include-the-internal-corrupt-record-column"></a>Messaggio di errore: a partire da Spark 2,3, le query da file JSON/CSV non elaborati non sono consentite quando le colonne a cui si fa riferimento includono solo la colonna del record danneggiato interna. 

- **Sintomi**: la lettura da un'origine JSON non riesce

- **Causa**: durante la lettura da un'origine JSON con un singolo documento su molte righe annidate, ADF, tramite Spark, non è in grado di determinare la posizione in cui inizia un nuovo documento e il documento precedente termina.

- **Soluzione**: nella trasformazione di origine che usa un set di dati JSON, espandere "impostazioni JSON" e attivare "documento singolo".

### <a name="error-message-duplicate-columns-found-in-join"></a>Messaggio di errore: colonne duplicate trovate in join

- **Sintomi**: la trasformazione join ha generato colonne dal lato sinistro e destro che includono nomi di colonna duplicati

- **Motivo**: i flussi da unire in join hanno nomi di colonna comuni

- **Soluzione**: aggiungere un transforamtion SELECT dopo il join e selezionare "Rimuovi colonne duplicate" per l'input e l'output.


## <a name="general-troubleshooting-guidance"></a>Indicazioni generali per la risoluzione dei problemi

1. Verificare lo stato delle connessioni del set di dati. In ogni trasformazione di origine e sink visitare il servizio collegato per ogni set di dati in uso e testare le connessioni.
2. Verificare lo stato delle connessioni di file e tabelle dalla finestra di progettazione del flusso di dati. Attivare il debug e fare clic su Anteprima dati nelle trasformazioni di origine per assicurarsi di poter accedere ai dati.
3. Se tutto sembra corretto dall'anteprima dei dati, passare alla finestra di progettazione della pipeline e inserire il flusso di dati in un'attività della pipeline. Eseguire il debug della pipeline per un test end-to-end.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla risoluzione dei problemi, provare a usare le risorse seguenti:

*  [Blog di Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Richieste di funzionalità Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Video su Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Forum Stack Overflow per Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informazioni su Twitter sui Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Guida alle prestazioni dei flussi di dati di mapping di ADF](concepts-data-flow-performance.md)
