---
title: Note sulla versione del gateway di gestione dati | Microsoft Docs
description: Note sulla versione di Gateway di gestione dati
services: data-factory
author: nabhishek
manager: jhubbard
editor: monicar
ms.assetid: 14762e82-76d9-41c4-ba9f-14a54da29c36
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2017
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 5b677990efe49b666396728767ee59587a280efe
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2017
---
# <a name="release-notes-for-data-management-gateway"></a>Note sulla versione di Gateway di gestione dati
Una delle maggiori difficoltà relative all'integrazione moderna dei dati consiste nello spostamento di dati da ambienti locali al cloud e viceversa. Data Factory esegue questa integrazione con Gateway di gestione dati, un agente che è possibile installare in locale per abilitare lo spostamento di dati ibridi.

Vedere gli articoli seguenti per informazioni dettagliate su Gateway di gestione dati e su come usarlo:

*  [Gateway di gestione dati](data-factory-data-management-gateway.md)
*  [Spostare dati tra un ambiente locale e il cloud mediante Azure Data Factory](data-factory-move-data-between-onprem-and-cloud.md)


## <a name="current-version"></a>VERSIONE ATTUALE 
Non è più possibile mantenere le note sulla versione qui. Ottenere le note sulla versione più recente [qui](https://go.microsoft.com/fwlink/?linkid=853077)




## <a name="earlier-versions"></a>Versioni precedenti
## <a name="21063477"></a>2.10.6347.7
### <a name="enhancements-"></a>Miglioramenti
- È possibile aggiungere le voci DNS per aggiungere il bus di servizio all'elenco elementi consentiti, invece di inserire in tale elenco tutti gli indirizzi IP di Azure IP dal firewall (se necessario). È possibile trovare la rispettiva voce DNS nel portale di Azure (Data Factory -> "Creare e distribuire" -> "Gateway" -> "serviceUrls" (in JSON)
- Il connettore HDFS supporta ora il certificato pubblico autofirmato, consentendo di saltare la convalida SSL.
- Corretto: problema relativo al gateway offline durante l'aggiornamento (a causa di uno sfasamento del clock)


## <a name="2963132"></a>2.9.6313.2
### <a name="enhancements-"></a>Miglioramenti
-   È possibile aggiungere le voci DNS per aggiungere il bus di servizio all'elenco elementi consentiti, invece di inserire in tale elenco tutti gli indirizzi IP di Azure IP dal firewall (se necessario). Altri dettagli sono disponibili qui.
-   È ora possibile copiare i dati in/da un singolo BLOB in blocchi fino a 4,75 TB, che corrisponde alle dimensioni massime supportate per i BLOB in blocchi. Il limite precedente era di 195 GB.
-   Corretto: problema relativo alla memoria esaurita durante la decompressione di alcuni file di piccole dimensioni durante l'attività di copia.
-   Corretto: problema relativo all'indice non compreso nell'intervallo durante la copia da Document DB a un'istanza locale di SQL Server con funzionalità di idempotenza.
-   Corretto: lo script di pulizia di SQL non funziona con l'istanza locale di SQL Server dalla Copia guidata.
-   Corretto: il nome di colonna con uno spazio finale non funziona nell'attività di copia.

## <a name="28662833"></a>2.8.66283.3
### <a name="enhancements-"></a>Miglioramenti
- Corretto: problema relativo alle credenziali mancanti al riavvio del computer gateway.
- Corretto: problema relativo alla registrazione durante il ripristino del gateway tramite un file di backup.


## <a name="2762401"></a>2.7.6240.1
### <a name="enhancements-"></a>Miglioramenti
- Corretto: lettura non corretta del valore Null decimale da Oracle come origine.

## <a name="2661922"></a>2.6.6192.2
### <a name="whats-new"></a>Novità
- Gli utenti possono fornire commenti e suggerimenti sull'esperienza di registrazione del gateway.
- Supporta il nuovo formato di compressione ZIP (Deflate)

### <a name="enhancements-"></a>Miglioramenti
- Miglioramento delle prestazioni per Oracle Sink, origine HDFS.
- Correzione di bug per l'aggiornamento automatico del gateway, capacità di elaborazione parallela del gateway.


## <a name="2561641"></a>2.5.6164.1
### <a name="enhancements"></a>Miglioramenti
- Esperienza di registrazione del gateway migliorata e più affidabile. L'esperienza risulta più efficiente grazie alla possibilità di tenere traccia dello stato di avanzamento durante il processo di registrazione del gateway.
- Miglioramento del processo di ripristino del gateway. Con questo aggiornamento è possibile ripristinare il gateway anche se non si ha il relativo file di backup. Sarà necessario reimpostare le credenziali del servizio collegato nel portale.
- Correzione di bug.

## <a name="2461511"></a>2.4.6151.1

### <a name="whats-new"></a>Novità

- È ora possibile archiviare localmente le credenziali dell'origine dati. Le credenziali vengono crittografate. Le credenziali dell'origine dati possono essere recuperate e ripristinate usando il file di backup che può essere esportato dal gateway esistente, operando in locale.

### <a name="enhancements-"></a>Miglioramenti

- Esperienza di registrazione gateway migliorata e più affidabile.
- Supporto del rilevamento automatico della configurazione QuoteChar per il formato di testo in copia guidata e aumento dell'accuratezza di rilevamento del formato generale.

## <a name="2361002"></a>2.3.6100.2

- Support del rilevamento automatico di firstRowAsHeader e SkipLineCount nella copia guidata per i file di testo in HDFS e nel file system locale.
- Miglioramento della stabilità della connessione di rete tra il gateway e il bus di servizio
- Alcune correzioni di bug


## <a name="2260721"></a>2.2.6072.1

*  Supporta l'impostazione proxy HTTP per il gateway tramite Gestione configurazione del gateway. Se configurato, l'accesso tramite proxy HTTP è disponibile per il BLOB di Azure, le tabelle di Azure, Azure Data Lake e Document DB.
*  Supporta la gestione delle intestazioni per il formato di testo quando si copiano dati da e verso BLOB di Azure, Azure Data Lake Store, File System locale e HDFS locale.
*  Supporta la copia di dati dal BLOB di accodamento e BLOB di pagine, oltre al BLOB in blocchi già supportato.
*  Introduce un nuovo stato del gateway **Online (Limited)**(Online (limitato)), che indica che la funzionalità principale del gateway funziona ad eccezione del supporto delle operazioni interattive per la copia guidata.
*  Migliora la solidità della registrazione del gateway con la chiave di registrazione.

## <a name="216040"></a>2.1.6040.

*  Il driver DB2 è ora incluso nel pacchetto di installazione del gateway. Non è necessario installarlo separatamente.
*  Il driver DB2 supporta ora z/OS e DB2 for i (AS/400) oltre alle piattaforme già supportate (Linux, Unix e Windows).
*  Supporta l'uso di Azure Cosmos DB come origine o destinazione per gli archivi dati locali.
*  Supporta la copia di dati da e nell'archivio BLOB ad accesso frequente o sporadico con l'account di archiviazione di uso generico già supportato.
*  Consente di connettersi a SQL Server locale tramite il gateway con privilegi di accesso remoto.  

## <a name="2060131"></a>2.0.6013.1

*  È possibile selezionare la lingua/cultura che verrà usata da un gateway durante l'installazione manuale.

*  Quando il gateway non funziona come previsto, è possibile scegliere di inviare a Microsoft i log di gateway degli ultimi sette giorni per agevolare la risoluzione del problema. Se il gateway non è connesso al servizio cloud, è possibile scegliere di salvare e archiviare i log del gateway.  

*  Miglioramenti all'interfaccia utente per la gestione della configurazione gateway:

    *  Stato del gateway più visibile sulla scheda Home.

    *  Controlli riorganizzati e semplificati.

    *  È possibile copiare dati da un archivio tramite lo [strumento di anteprima della copia senza codice](data-factory-copy-data-wizard-tutorial.md). Per informazioni generiche su questa funzionalità, vedere [Copia di staging](data-factory-copy-activity-performance.md#staged-copy) .
*  Gateway di gestione dati consente di inserire i dati direttamente da un database di SQL Server locale in Azure Machine Learning.

*  Miglioramenti delle prestazioni

    * Prestazioni di visualizzazione migliorate dello schema e dell'anteprima in SQL Server nello strumento di anteprima della copia senza codice.

## <a name="11259531"></a>1.12.5953.1

*  Correzioni di bug

## <a name="11159181"></a>1.11.5918.1

*  La dimensione massima del registro eventi del gateway è aumentata da 1 MB a 40 MB.

*  Nel caso in cui sia necessario un riavvio durante l'aggiornamento automatico del gateway, viene visualizzata una finestra di dialogo di avviso. È possibile scegliere di riavviare subito o in un secondo tempo.

*  In caso di errore dell'aggiornamento automatico, il programma di installazione del gateway ritenta l'aggiornamento automatico al massimo tre volte.

*  Miglioramenti delle prestazioni

    * È possibile migliorare le prestazioni in caso di caricamento di tabelle di grandi dimensioni dal server locale in uno scenario di copia senza codice.

*  Correzioni di bug

## <a name="11058921"></a>1.10.5892.1

*  Miglioramenti delle prestazioni

*  Correzioni di bug

## <a name="1958652"></a>1.9.5865.2

*  Funzionalità di aggiornamento automatico senza intervento dell'utente
*  Nuova icona dell'area di notifica con indicatori di stato del gateway
*  Possibilità di scegliere "Aggiorna adesso" dal client
*  Possibilità di impostare l'ora di pianificazione dell'aggiornamento
*  Script di PowerShell per attivare o disattivare l'aggiornamento automatico
*  Supporto per il formato JSON  
*  Miglioramenti delle prestazioni
*  Correzioni di bug

## <a name="1858221"></a>1.8.5822.1

*  Miglioramento dell'esperienza di risoluzione dei problemi
*  Miglioramenti delle prestazioni
*  Correzioni di bug

### <a name="1757951"></a>1.7.5795.1

*  Miglioramenti delle prestazioni
*  Correzioni di bug

### <a name="1757641"></a>1.7.5764.1

*  Miglioramenti delle prestazioni
*  Correzioni di bug

### <a name="1657351"></a>1.6.5735.1

*  Supporto di origine/sink HDFS in locale
*  Miglioramenti delle prestazioni
*  Correzioni di bug

### <a name="1656961"></a>1.6.5696.1

*  Miglioramenti delle prestazioni
*  Correzioni di bug

### <a name="1656761"></a>1.6.5676.1

*  Supporto degli strumenti di diagnostica in Gestione configurazione
*  Supporto delle colonne di tabella per le origini dati tabulari per Data factory di Azure
*  Supporto di SQL DW per Data factory di Azure
*  Supporto dell'isolamento in BlobSource e FileSource per Data factory di Azure
*  Supporto di CopyBehavior – MergeFiles, PreserveHierarchy e FlattenHierarchy in BlobSink e FileSink con copia binaria per Data Factory di Azure
*  Supporto dello stato di avanzamento della creazione di report per l'attività di copia per Data factory di Azure
*  Supporto della convalida della connettività dell'origine dati per Data factory di Azure
*  Correzioni di bug

### <a name="1656721"></a>1.6.5672.1

*  Supporto del nome di tabella per l'origine dati ODBC per Data factory di Azure
*  Miglioramenti delle prestazioni
*  Correzioni di bug

### <a name="1656581"></a>1.6.5658.1

*  Supporto del sink dei file per Data factory di Azure
*  Supporto del mantenimento della gerarchia nella copia binaria per Data factory di Azure
*  Supporto dell'idempotenza dell'attività di copia per Data factory di Azure
*  Correzioni di bug

### <a name="1656401"></a>1.6.5640.1

*  Supporto di altre 3 origini dati per Data factory di Azure (ODBC, OData, HDFS)
*  Supporto delle virgolette nel parser CSV per Data factory di Azure
*  Supporto della compressione (BZip2)
*  Correzioni di bug

### <a name="1556121"></a>1.5.5612.1

*  Supporto di cinque database relazionali per Data Factory di Azure (MySQL, PostgreSQL, DB2, Teradata e Sybase)
*  Supporto della compressione (Gzip e Deflate)
*  Miglioramenti delle prestazioni
*  Correzioni di bug

### <a name="1455491"></a>1.4.5549.1

*  Aggiunta del supporto dell'origine dati Oracle per Data factory di Azure
*  Miglioramenti delle prestazioni
*  Correzioni di bug

### <a name="1454921"></a>1.4.5492.1

*  File binario unificato che supporta il servizio Data factory di Microsoft Azure e il servizio Power BI di Office 365
*  Perfezionamento dell'interfaccia utente di configurazione e del processo di registrazione
*  Data Factory di Azure: supporto di ingresso e uscita in Azure per l'origine dati SQL Server

### <a name="1253031"></a>1.2.5303.1

*  Risoluzione del problema di timeout per supportare connessioni alle origini dati più dispersive in termini di tempo.

### <a name="1155268"></a>1.1.5526.8

*  Richiede .NET Framework 4.5.1 come prerequisito durante l'installazione.

### <a name="1051442"></a>1.0.5144.2

*  Nessuna modifica che interessi gli scenari di Data factory di Azure.
