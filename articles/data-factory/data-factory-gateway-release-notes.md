---
title: Note sulla versione di Gateway di gestione dati | Microsoft Docs
description: Note sulla versione di Gateway di gestione dati
services: data-factory
documentationcenter: ''
author: spelluru
manager: jhubbard
editor: monicar

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/26/2016
ms.author: spelluru

---
# Note sulla versione di Gateway di gestione dati
Una delle maggiori difficoltà relative all'integrazione moderna dei dati consiste nello spostamento uniforme di dati da ambienti locali al cloud e viceversa. Data Factory facilita questa integrazione con Gateway di gestione dati, un agente che è possibile installare in locale per abilitare lo spostamento di dati ibridi.

Vedere gli articoli seguenti per informazioni dettagliate su Gateway di gestione dati e su come usarlo:

* [Gateway di gestione dati](data-factory-data-management-gateway.md)
* [Spostare dati tra un ambiente locale e il cloud mediante Azure Data Factory](data-factory-move-data-between-onprem-and-cloud.md)

## Versione corrente (2.2.6072.1)
* Supporta l'impostazione proxy HTTP per il gateway tramite Gestione configurazione del gateway. Se configurato, l'accesso tramite proxy HTTP è disponibile per il BLOB di Azure, le tabelle di Azure, Azure Data Lake e Document DB.
* Supporta la gestione delle intestazioni per il formato di testo quando si copiano dati da e verso BLOB di Azure, Azure Data Lake Store, File System locale e HDFS locale.
* Supporta la copia di dati dal BLOB di accodamento e BLOB di pagine, oltre al BLOB in blocchi già supportato.
* Introduce un nuovo stato del gateway **Online (Limited)** (Online (limitato)), che indica che la funzionalità principale del gateway funziona ad eccezione del supporto delle operazioni interattive per la copia guidata.
* Migliora la solidità della registrazione del gateway con la chiave di registrazione.

## Versioni precedenti
## 2\.1.6040.1
* Il driver DB2 è ora incluso nel pacchetto di installazione del gateway. Non è necessario installarlo separatamente.
* Il driver DB2 supporta ora z/OS e DB2 for i (AS/400) oltre alle piattaforme già supportate (Linux, Unix e Windows).
* Supporta l'uso di DocumentDB come origine o destinazione per gli archivi dati locali.
* Supporta la copia di dati da e nell'archivio BLOB ad accesso frequente o sporadico con l'account di archiviazione di uso generico già supportato.
* Consente di connettersi a SQL Server locale tramite il gateway con privilegi di accesso remoto.

## 2\.0.6013.1
* È possibile selezionare la lingua/cultura che verrà usata da un gateway durante l'installazione manuale.
* Quando il gateway non funziona come previsto, è possibile scegliere di inviare a Microsoft i log di gateway degli ultimi sette giorni per agevolare la risoluzione del problema. Se il gateway non è connesso al servizio cloud, è possibile scegliere di salvare e archiviare i log del gateway.
* Miglioramenti all'interfaccia utente per la gestione della configurazione gateway:
  * Stato del gateway più visibile sulla scheda Home.
  * Controlli riorganizzati e semplificati.
* È possibile copiare dati da un archivio tramite lo [strumento di anteprima della copia senza codice](data-factory-copy-data-wizard-tutorial.md). Per informazioni generiche su questa funzionalità, vedere [Copia di staging](data-factory-copy-activity-performance.md#staged-copy).
* Gateway di gestione dati consente di inserire i dati direttamente da un database di SQL Server locale in Azure Machine Learning.
* Miglioramenti delle prestazioni
  * Prestazioni di visualizzazione migliorate dello schema e dell'anteprima in SQL Server nello strumento di anteprima della copia senza codice.

## 1\.12.5953.1
* Correzioni di bug

## 1\.11.5918.1
* La dimensione massima del registro eventi del gateway è aumentata da 1 MB a 40 MB.
* Nel caso in cui sia necessario un riavvio durante l'aggiornamento automatico del gateway, viene visualizzata una finestra di dialogo di avviso. È possibile scegliere di riavviare subito o in un secondo tempo.
* In caso di errore dell'aggiornamento automatico, il programma di installazione del gateway ritenta l'aggiornamento automatico al massimo tre volte.
* Miglioramenti delle prestazioni
  * È possibile migliorare le prestazioni in caso di caricamento di tabelle di grandi dimensioni dal server locale in uno scenario di copia senza codice.
* Correzioni di bug

## 1\.10.5892.1
* Miglioramenti delle prestazioni
* Correzioni di bug

## 1\.9.5865.2
* Funzionalità di aggiornamento automatico senza intervento dell'utente
* Nuova icona dell'area di notifica con indicatori di stato del gateway
* Possibilità di scegliere "Aggiorna adesso" dal client
* Possibilità di impostare l'ora di pianificazione dell'aggiornamento
* Script di PowerShell per attivare o disattivare l'aggiornamento automatico
* Supporto per il formato JSON
* Miglioramenti delle prestazioni
* Correzioni di bug

## 1\.8.5822.1
* Miglioramento dell'esperienza di risoluzione dei problemi
* Miglioramenti delle prestazioni
* Correzioni di bug

### 1\.7.5795.1
* Miglioramenti delle prestazioni
* Correzioni di bug

### 1\.7.5764.1
* Miglioramenti delle prestazioni
* Correzioni di bug

### 1\.6.5735.1
* Supporto di origine/sink HDFS in locale
* Miglioramenti delle prestazioni
* Correzioni di bug

### 1\.6.5696.1
* Miglioramenti delle prestazioni
* Correzioni di bug

### 1\.6.5676.1
* Supporto degli strumenti di diagnostica in Gestione configurazione
* Supporto delle colonne di tabella per le origini dati tabulari per Data factory di Azure
* Supporto di SQL DW per Data factory di Azure
* Supporto dell'isolamento in BlobSource e FileSource per Data factory di Azure
* Supporto di CopyBehavior – MergeFiles, PreserveHierarchy e FlattenHierarchy in BlobSink e FileSink con copia binaria per Data Factory di Azure
* Supporto dello stato di avanzamento della creazione di report per l'attività di copia per Data factory di Azure
* Supporto della convalida della connettività dell'origine dati per Data factory di Azure
* Correzioni di bug

### 1\.6.5672.1
* Supporto del nome di tabella per l'origine dati ODBC per Data factory di Azure
* Miglioramenti delle prestazioni
* Correzioni di bug

### 1\.6.5658.1
* Supporto del sink dei file per Data factory di Azure
* Supporto del mantenimento della gerarchia nella copia binaria per Data factory di Azure
* Supporto dell'idempotenza dell'attività di copia per Data factory di Azure
* Correzioni di bug

### 1\.6.5640.1
* Supporto di altre 3 origini dati per Data factory di Azure (ODBC, OData, HDFS)
* Supporto delle virgolette nel parser CSV per Data factory di Azure
* Supporto della compressione (BZip2)
* Correzioni di bug

### 1\.5.5612.1
* Supporto di cinque database relazionali per Data Factory di Azure (MySQL, PostgreSQL, DB2, Teradata e Sybase)
* Supporto della compressione (Gzip e Deflate)
* Miglioramenti delle prestazioni
* Correzioni di bug

### 1\.4.5549.1
* Aggiunta del supporto dell'origine dati Oracle per Data factory di Azure
* Miglioramenti delle prestazioni
* Correzioni di bug

### 1\.4.5492.1
* File binario unificato che supporta il servizio Data factory di Microsoft Azure e il servizio Power BI di Office 365
* Perfezionamento dell'interfaccia utente di configurazione e del processo di registrazione
* Data Factory di Azure: supporto di ingresso e uscita in Azure per l'origine dati SQL Server

### 1\.2.5303.1
* Risoluzione del problema di timeout per supportare connessioni alle origini dati più dispersive in termini di tempo.

### 1\.1.5526.8
* Richiede .NET Framework 4.5.1 come prerequisito durante l'installazione.

### 1\.0.5144.2
* Nessuna modifica che interessi gli scenari di Data factory di Azure.

<!---HONumber=AcomDC_0831_2016-->