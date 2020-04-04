---
title: Procedure consigliate per il caricamento dei dati per il pool SQL SynapseData loading best practices for Synapse SQL pool
description: Suggerimenti e ottimizzazioni delle prestazioni per il caricamento dei dati tramite il pool SQL Synapse.Recommendations and performance optimizations for loading data using Synapse SQL pool.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: e5ad5f6f2f5be239af23ee4802cf09c388c93ae9
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632915"
---
# <a name="best-practices-for-loading-data-using-synapse-sql-pool"></a>Procedure consigliate per il caricamento dei dati tramite il pool SQL SynapseBest practices for loading data using Synapse SQL pool

In questo articolo verranno appesi suggerimenti e ottimizzazioni delle prestazioni per il caricamento dei dati tramite il pool SQL.

## <a name="preparing-data-in-azure-storage"></a>Preparazione dei dati in Archiviazione di Azure

Per ridurre al minimo la latenza, spostare il livello di archiviazione e il pool SQL.

In caso di esportazione di dati in un formato di file ORC, quando sono presenti colonne di testo di grandi dimensioni potrebbero verificarsi errori di memoria insufficiente di Java. Per risolvere questo problema, esportare solo un subset di colonne.

PolyBase non può caricare righe con più di 1.000.000 byte di dati. I dati inseriti nei file di testo nell'archivio BLOB di Azure o in Azure Data Lake Store devono corrispondere a meno di 1.000.000 di byte. Questa limitazione in termini di byte vale indipendentemente dallo schema di tabella.

Tutti i formati di file hanno caratteristiche di prestazioni diverse. Per ottenere la velocità di caricamento massima, usare file di testo delimitati compressi. La differenza di prestazioni tra UTF-8 e UTF-16 è minima.

Suddividere i file compressi di grandi dimensioni in file compressi di dimensioni inferiori.

## <a name="running-loads-with-enough-compute"></a>Esecuzione di carichi con risorse di calcolo sufficienti

Per ottenere la velocità di caricamento massima, eseguire un solo processo di caricamento alla volta. Se ciò non è fattibile, eseguire contemporaneamente un numero minimo di carichi. Se si prevede un processo di caricamento di grandi dimensioni, prendere in considerazione la scalabilità verticale del pool SQL prima del caricamento.

Per eseguire i caricamenti con risorse di calcolo appropriate, creare utenti designati addetti al caricamento. Assegnare ogni utente di caricamento a una classe di risorse o a un gruppo di carico di lavoro specifico. Per eseguire un caricamento, accedere come uno degli utenti di caricamento e quindi eseguire il caricamento. Il caricamento viene eseguito con la classe di risorse dell'utente.  

> [!NOTE]
> Questo metodo è più semplice rispetto al tentativo di modificare la classe di risorse di un utente in base alla classe di risorse attualmente necessaria.

### <a name="example-of-creating-a-loading-user"></a>Esempio di creazione di un utente addetto al caricamento

Questo esempio crea un utente addetto al caricamento per la classe di risorse staticrc20. Il primo passaggio consiste nel **connettersi al master** e creare un account di accesso.

```sql
   -- Connect to master
   CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
```

Connettersi al pool SQL e creare un utente. Nel codice seguente si presuppone che si sia connessi al database denominato mySampleDataWarehouse. Viene illustrato come creare un utente denominato LoaderRC20 e viene concessa all'utente l'autorizzazione di controllo su un database. Quindi, aggiunge l'utente come membro del ruolo del database staticrc20.  

```sql
   -- Connect to the database
   CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
   GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
   EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
```

Per eseguire un carico con risorse per le classi di risorse staticRC20, accedere come LoaderRC20 ed eseguiil carico.

Eseguire i caricamenti con classi di risorse statiche anziché dinamiche. L'uso di classi di risorse statiche garantisce le stesse risorse indipendentemente dalle [unità data warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md). Se si usa una classe di risorse dinamica, le risorse variano in base al livello di servizio.

Per le classi dinamiche, un livello di servizio inferiore renderà probabilmente necessario usare una classe di risorse di maggiori dimensioni per l'utente addetto al caricamento.

## <a name="allowing-multiple-users-to-load"></a>Consentire il caricamento a più utenti

Spesso è necessario che più utenti carichino i dati in un pool SQL. Il caricamento con [CREATE TABLE AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) richiede autorizzazioni CONTROL per il database.  L'autorizzazione CONTROL fornisce il controllo degli accessi a tutti gli schemi.

È consigliabile che non tutti gli utenti che eseguono caricamenti abbiano il controllo degli accessi a tutti gli schemi. Per limitare le autorizzazioni, usare l'istruzione DENY CONTROL.

Si supponga ad esempio che esistano gli schemi di database schema_A per il reparto A e schema_B per il reparto B e che gli utenti di database user_A e user_B siano gli utenti addetti al caricamento PolyBase rispettivamente nel reparto A e nel reparto B. A entrambi gli utenti sono state concesse le autorizzazioni di database CONTROL. Gli autori di schema A e B usano a questo punto DENY per bloccare i rispettivi schemi:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```

User_A e user_B sono ora bloccati dallo schema dell'altro reparto.

## <a name="loading-to-a-staging-table"></a>Caricamento in una tabella di staging

Per ottenere la velocità di caricamento più veloce per lo spostamento dei dati in una tabella del pool SQL, caricare i dati in una tabella di staging.  Definire la tabella di staging come heap e usare round robin per l'opzione di distribuzione.

Si consideri che il caricamento è in genere un processo in due passaggi in cui si carica prima in una tabella di staging e quindi inserire i dati in una tabella del pool SQL di produzione. Se la tabella di produzione usa una distribuzione hash, il tempo totale necessario per il caricamento e l'inserimento può essere ridotto se si definisce una tabella di staging con la distribuzione hash.

Il caricamento nella tabella di staging richiede più tempo, ma il secondo passaggio di inserimento delle righe nella tabella di produzione non comporta lo spostamento dei dati tra le distribuzioni.

## <a name="loading-to-a-columnstore-index"></a>Caricamento in un indice columnstore

Gli indici columnstore richiedono una grande quantità di memoria per la compressione dei dati in rowgroup di qualità elevata. Per una compressione e un'efficienza dell'indice ottimali, l'indice columnstore deve comprimere il valore massimo di 1.048.576 righe in ogni rowgroup.

In caso di utilizzo elevato di memoria, l'indice columnstore potrebbe non riuscire a raggiungere i tassi di compressione massimi. Questo scenario, a sua volta, influisce sulle prestazioni delle query. Per un approfondimento, vedere l'articolo relativo alle [ottimizzazioni della memoria columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

- Per garantire all'utente addetto al caricamento una quantità di memoria sufficiente per raggiungere i massimi tassi di compressione, usare utenti addetti al caricamento che siano membri di una classe di risorse di medie o grandi dimensioni.
- Caricare un numero di righe sufficiente a riempire completamente i nuovi rowgroup. Durante un caricamento bulk, ogni gruppo di 1.048.576 righe viene compresso direttamente nel columnstore come rowgroup completo. In caso di caricamenti con meno di 102.400 righe, le righe vengono inviate nell'archivio differenziale, in cui vengono mantenute in un indice albero B.

> [!NOTE]
> Se si caricano troppe righe, è possibile che tutte si instradano al deltastore e non vengano compresse immediatamente nel formato columnstore.

## <a name="increase-batch-size-when-using-sqlbulkcopy-api-or-bcp"></a>Aumentare le dimensioni del batch quando si utilizza l'API SqLBulkCopy o bcp

Il caricamento con PolyBase fornirà la velocità effettiva massima con il pool SQL. Se non è possibile utilizzare PolyBase per caricare ed è necessario utilizzare [l'API SqLBulkCopy](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy.aspx) o [bcp](https://docs.microsoft.com/sql/tools/bcp-utility?view=sql-server-ver15), è consigliabile aumentare le dimensioni del batch per una migliore velocità effettiva.

> [!TIP]
> Una dimensione batch compresa tra 100 K e 1M righe è la linea di base consigliata per determinare la capacità ottimale delle dimensioni del batch.

## <a name="handling-loading-failures"></a>Gestione degli errori di caricamento

Un caricamento con una tabella esterna può avere esito negativo con l'errore *"Query interrotta. È stata raggiunta la soglia massima di rifiuti durante la lettura da un'origine esterna"*. Questo messaggio indica che i dati esterni contengono record dirty.

Un record di dati viene considerato dirty se soddisfa una delle condizioni seguenti:A data record is considered to be dirty if it meets one of the following conditions:

- I tipi di dati e il numero di colonne non corrispondono alle definizioni di colonna della tabella esterna.
- I dati non sono conformi al formato di file esterno specificato.

Per risolvere questo problema, assicurarsi che la tabella esterna e le definizioni del formato di file esterno siano corrette e che i dati esterni siano conformi a queste definizioni.

Se un sottoinsieme di record di dati esterni è dirty, è possibile scegliere di rifiutare questi record per le query utilizzando le opzioni di rifiuto in [CREATE EXTERNAL TABLE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql?view=sql-server-ver15).

## <a name="inserting-data-into-a-production-table"></a>Inserimento di dati in una tabella di produzione

Per una singola operazione di caricamento in una tabella di piccole dimensioni con un'[istruzione INSERT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) o anche un ricaricamento periodico di una ricerca, è possibile ottenere prestazioni soddisfacenti con un'istruzione come `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Gli inserimenti singleton non offrono tuttavia la stessa efficienza di un caricamento bulk.

Se nel corso di una giornata si eseguono migliaia di singoli inserimenti o più, inviare in batch gli inserimenti per poterne eseguire il caricamento bulk.  Sviluppare i processi per aggiungere i singoli inserimenti in un file e quindi creare un altro processo che carica periodicamente il file.

## <a name="creating-statistics-after-the-load"></a>Creazione di statistiche dopo il caricamento

Per migliorare le prestazioni delle query, è importante creare statistiche su tutte le colonne di tutte le tabelle dopo il primo caricamento o modifiche sostanziali ai dati. La creazione di statistiche può essere eseguita manualmente oppure è possibile abilitare [AUTO_CREATE_STATISTICS](sql-data-warehouse-tables-statistics.md#automatic-creation-of-statistic).

Per una spiegazione dettagliata delle statistiche, vedere [Statistiche](sql-data-warehouse-tables-statistics.md). Nell'esempio seguente viene illustrato come creare manualmente le statistiche su cinque colonne della tabella Customer_Speed.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys"></a>Ruotare le chiavi di archiviazione

Ai fini della sicurezza è consigliabile modificare regolarmente la chiave di accesso all'archivio BLOB. Sono disponibili due chiavi di archiviazione per l'account di archiviazione BLOB ed è quindi possibile passare da una chiave all'altra.

Per ruotare le chiavi dell'account di archiviazione di Azure:

Per ogni account di archiviazione la cui chiave ha subito modifiche, eseguire [ALTER DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/alter-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

Esempio:

Creazione della chiave originale

```sql
CREATE DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key1'
```

Rotazione della chiave dalla chiave 1 alla chiave 2

```sql
ALTER DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key2'
```

Non sono necessarie altre modifiche alle origini dati esterne.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni su PolyBase e sulla progettazione di un processo di estrazione, caricamento e trasformazione, vedere l'articolo su come [progettare estrazione, caricamento e trasformazione per SQL Data Warehouse](design-elt-data-loading.md).
- Per un'esercitazione sul caricamento, vedere [Usare PolyBase per caricare dati dall'archivio BLOB di Azure ad Azure SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md).
- Per monitorare il caricamento dei dati, vedere [Monitoraggio del carico di lavoro mediante DMV](sql-data-warehouse-manage-monitor.md).
