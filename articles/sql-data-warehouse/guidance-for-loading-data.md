---
title: Procedure consigliate per il caricamento dei dati - Azure SQL Data Warehouse | Microsoft Docs
description: Raccomandazioni per il caricamento di dati e l'esecuzione di operazioni di estrazione, caricamento e trasformazione con Azure SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: 7b698cad-b152-4d33-97f5-5155dfa60f79
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 12/13/2017
ms.author: barbkess
ms.openlocfilehash: 10d06fd29640a350c5522c00c4c9ebd9c6b24c89
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2017
---
# <a name="best-practices-for-loading-data-into-azure-sql-data-warehouse"></a>Procedure consigliate per il caricamento dei dati in Azure SQL Data Warehouse
Raccomandazioni e ottimizzazioni delle prestazioni per il caricamento di dati in Azure SQL Data Warehouse. 

- Per altre informazioni su PolyBase e sulla progettazione di un processo di estrazione, caricamento e trasformazione, vedere l'articolo su come [progettare estrazione, caricamento e trasformazione per SQL Data Warehouse](design-elt-data-loading.md).
- Per un'esercitazione sul caricamento, vedere [Usare PolyBase per caricare dati dall'archivio BLOB di Azure ad Azure SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md).


## <a name="preparing-data-in-azure-storage"></a>Preparazione dei dati in Archiviazione di Azure
Per ridurre al minimo la latenza, usare un percorso condiviso per il livello di archiviazione e il data warehouse.

Quando si esportano dati in un formato file ORC, le colonne con molto testo possono essere limitate a 50, a causa di errori di memoria insufficiente di Java. Per risolvere questo problema, esportare solo un subset di colonne.

PolyBase non può caricare righe contenenti più di 1.000.000 di byte di dati. I dati inseriti nei file di testo nell'archivio BLOB di Azure o in Azure Data Lake Store devono corrispondere a meno di 1.000.000 di byte. Questa limitazione in termini di byte vale indipendentemente dallo schema di tabella.

Tutti i formati di file hanno caratteristiche di prestazioni diverse. Per ottenere la velocità di caricamento massima, usare file di testo delimitati compressi. La differenza di prestazioni tra UTF-8 e UTF-16 è minima.

Suddividere i file compressi di grandi dimensioni in file compressi di dimensioni inferiori.

## <a name="running-loads-with-enough-compute"></a>Esecuzione di carichi con risorse di calcolo sufficienti

Per ottenere la velocità di caricamento massima, eseguire un solo processo di caricamento alla volta. Se questo approccio non è fattibile, eseguire contemporaneamente un numero minimo di caricamenti. Se si prevede un processo di caricamento di grandi dimensioni, è consigliabile aumentare prima le prestazioni del data warehouse.

Per eseguire i caricamenti con risorse di calcolo appropriate, creare utenti designati addetti al caricamento. Assegnare ogni utente addetto al caricamento a una specifica classe di risorse. Per eseguire un caricamento, effettuare l'accesso come utente addetto al caricamento e quindi eseguire l'operazione. Il caricamento viene eseguito con la classe di risorse dell'utente.  Questo metodo è più semplice rispetto al tentativo di modificare la classe di risorse di un utente in base alla classe di risorse attualmente necessaria.

Questo codice crea un utente addetto al caricamento per la classe di risorse staticrc20. Concede all'utente l'autorizzazione di controllo per un database e quindi aggiunge l'utente come membro del ruolo del database staticrc20. Per eseguire un caricamento con le risorse della classe di risorse staticRC20, è sufficiente accedere come LoaderRC20 ed eseguire il caricamento. 

    ```sql
    CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
    EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
    ```

Eseguire i caricamenti con classi di risorse statiche anziché dinamiche. L'uso di classi di risorse statiche garantisce le stesse risorse indipendentemente dal [livello di servizio](performance-tiers.md#service-levels). Se si usa una classe di risorse dinamica, le risorse variano in base al livello di servizio. Per le classi dinamiche, un livello di servizio inferiore renderà probabilmente necessario usare una classe di risorse di maggiori dimensioni per l'utente addetto al caricamento.

## <a name="allowing-multiple-users-to-load"></a>Consentire il caricamento a più utenti

È spesso necessario fare in modo che più utenti possano caricare dati in un data warehouse. Il caricamento con [CREATE TABLE AS SELECT (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)] richiede autorizzazioni CONTROL per il database.  L'autorizzazione CONTROL fornisce il controllo degli accessi a tutti gli schemi. È consigliabile che non tutti gli utenti che eseguono caricamenti abbiano il controllo degli accessi a tutti gli schemi. Per limitare le autorizzazioni, usare l'istruzione DENY CONTROL.

Si supponga ad esempio che esistano gli schemi di database schema_A per il reparto A e schema_B per il reparto B e che gli utenti di database user_A e user_B siano gli utenti addetti al caricamento PolyBase rispettivamente nel reparto A e nel reparto B. A entrambi gli utenti sono state concesse le autorizzazioni di database CONTROL. Gli autori di schema A e B usano a questo punto DENY per bloccare i rispettivi schemi:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```   

Gli utenti user_A e user_B saranno ora esclusi dall'accesso allo schema dell'altro reparto.


## <a name="loading-to-a-staging-table"></a>Caricamento in una tabella di staging

Per ottenere la massima velocità di caricamento per spostare i dati in una tabella di data warehouse, caricare i dati in una tabella di staging.  Definire la tabella di staging come heap e usare round robin per l'opzione di distribuzione. 

Il caricamento è in genere un processo in due fasi in cui i dati vengono prima caricati in una tabella di staging, quindi inseriti in una tabella di data warehouse di produzione. Se la tabella di produzione usa una distribuzione hash, il tempo totale necessario per il caricamento e l'inserimento può essere ridotto se si definisce una tabella di staging con la distribuzione hash. Il caricamento nella tabella di staging richiede più tempo, ma il secondo passaggio di inserimento delle righe nella tabella di produzione non comporta lo spostamento dei dati tra le distribuzioni.

## <a name="loading-to-a-columnstore-index"></a>Caricamento in un indice columnstore

Gli indici columnstore richiedono una grande quantità di memoria per la compressione dei dati in rowgroup di qualità elevata. Per una compressione e un'efficienza dell'indice ottimali, l'indice columnstore deve comprimere il valore massimo di 1.048.576 righe in ogni rowgroup. In caso di utilizzo elevato di memoria, l'indice columnstore potrebbe non riuscire a raggiungere i tassi di compressione massimi. Questo influisce a propria volta sulle prestazioni delle query. Per un approfondimento, vedere l'articolo relativo alle [ottimizzazioni della memoria columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

- Per garantire all'utente addetto al caricamento una quantità di memoria sufficiente per raggiungere i massimi tassi di compressione, usare utenti addetti al caricamento che siano membri di una classe di risorse di medie o grandi dimensioni. 
- Caricare un numero di righe sufficiente a riempire completamente i nuovi rowgroup. Durante un caricamento bulk, ogni gruppo di 1.048.576 righe viene compresso direttamente nel columnstore come rowgroup completo. In caso di caricamenti con meno di 102.400 righe, le righe vengono inviate nell'archivio differenziale, in cui vengono mantenute in un indice albero B. Se si carica un numero troppo basso di righe, le righe potrebbero essere inserite tutte nell'archivio differenziale e non essere immediatamente compresse nel formato columnstore.


## <a name="handling-loading-failures"></a>Gestione degli errori di caricamento

Un caricamento con una tabella esterna può avere esito negativo con l'errore *"Query interrotta. È stata raggiunta la soglia massima di rifiuti durante la lettura da un'origine esterna"*. Questo messaggio indica che i dati esterni contengono record dirty. Un record di dati viene considerato dirty se i tipi di dati e il numero di colonne non corrispondono alle definizioni di colonna della tabella esterna oppure se i dati non sono conformi al formato di file esterno specificato. 

Per risolvere questo problema, assicurarsi che la tabella esterna e le definizioni del formato di file esterno siano corrette e che i dati esterni siano conformi a queste definizioni. Nel caso in cui un subset di record di dati esterni sia dirty, è possibile scegliere di rifiutare tali record per le query usando le opzioni di rifiuto in CREATE EXTERNAL TABLE.

## <a name="inserting-data-into-a-production-table"></a>Inserimento di dati in una tabella di produzione
Per una singola operazione di caricamento in una tabella di piccole dimensioni con un'[istruzione INSERT](/sql/t-sql/statements/insert-transact-sql.md) o anche un ricaricamento periodico di una ricerca, è possibile ottenere prestazioni soddisfacenti con un'istruzione come `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Gli inserimenti singleton non offrono tuttavia la stessa efficienza di un caricamento bulk. 

Se nel corso di una giornata si eseguono migliaia di singoli inserimenti o più, inviare in batch gli inserimenti per poterne eseguire il caricamento bulk.  Sviluppare i processi per aggiungere i singoli inserimenti in un file e quindi creare un altro processo che carica periodicamente il file.

## <a name="creating-statistics-after-the-load"></a>Creazione di statistiche dopo il caricamento

Per migliorare le prestazioni delle query, è importante creare statistiche su tutte le colonne di tutte le tabelle dopo il primo caricamento o modifiche sostanziali ai dati.  Per una spiegazione dettagliata delle statistiche, vedere [Statistiche][Statistiche]. L'esempio seguente crea statistiche su cinque colonne della tabella Customer_Speed.

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

1. Creare una seconda credenziale con ambito database basata sulla chiave di accesso alle risorse di archiviazione secondaria.
2. Creare una seconda origine dati esterna in base a questa nuova credenziale.
3. Rimuovere le tabelle esterne e crearle in modo che puntino alle nuove origini dati esterne. 

Al termine della migrazione delle tabelle esterne alla nuova origine dati, eseguire queste attività di pulizia:

1. Rimuovere la prima origine dati esterna.
2. Rimuovere la prima credenziale con ambito database basata sulla chiave di accesso alle risorse di archiviazione primaria.
3. Accedere ad Azure e rigenerare la chiave di accesso primaria affinché sia pronta per la rotazione successiva.


## <a name="next-steps"></a>Passaggi successivi
Per monitorare il processo di caricamento, vedere [Monitoraggio del carico di lavoro mediante DMV](sql-data-warehouse-manage-monitor.md).



