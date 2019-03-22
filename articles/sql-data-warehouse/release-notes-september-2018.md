---
title: Note sulla versione di Azure SQL Data Warehouse - Settembre 2018 | Microsoft Docs
description: Note sulla versione di Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 10/08/2018
ms.author: mausher
ms.reviewer: twounder
ms.openlocfilehash: bc559a1224aace2ee599c24c8dce07a6d55173fd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57863713"
---
# <a name="whats-new-in-azure-sql-data-warehouse-september-2018"></a>Novità di Azure SQL Data Warehouse Settembre 2018
Azure SQL Data Warehouse riceve continuamente miglioramenti. Questo articolo illustra le nuove funzionalità e le modifiche introdotte nel mese di settembre 2018.

## <a name="new-lower-entry-point-for-sql-data-warehouse-gen2"></a>Nuovo livello di servizio ridotto per SQL Data Warehouse di seconda generazione
Nell'aprile 2018 [Microsoft ha annunciato](https://azure.microsoft.com/blog/turbocharge-cloud-analytics-with-azure-sql-data-warehouse/) Azure SQL Data Warehouse di seconda generazione, che offre prestazioni 5 volte superiori, una scalabilità delle risorse di calcolo 5 volte superiore, un livello di concorrenza 4 volte superiore e uno spazio di archiviazione illimitato. Come indicato nel [benchmark sul data warehouse nel cloud](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/) di Gigaom, SQL Data Warehouse di seconda generazione **supera le prestazioni di Amazon Redshift del 42%**.

SQL Data Warehouse di seconda generazione è ora disponibile a livello generale al livello di servizio ridotto DWU500c, che consente di eseguire un data warehouse di dimensioni inferiori o ambienti di sviluppo/test con tutti i più recenti miglioramenti ai servizi. Il nuovo livello di servizio mantiene tutte le funzionalità della seconda generazione, tra cui la [memorizzazione adattiva nella cache](https://azure.microsoft.com/blog/adaptive-caching-powers-azure-sql-data-warehouse-performance-gains/), la [riproduzione casuale ultrarapida dei dati](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/) e il supporto del [data warehouse in tempo reale](https://azure.microsoft.com/blog/enabling-real-time-data-warehousing-with-azure-sql-data-warehouse/).

## <a name="sql-vulnerability-assessment"></a>Valutazione della vulnerabilità di SQL
[Valutazione della vulnerabilità di SQL](https://blogs.msdn.microsoft.com/sqlsecurity/2018/09/25/sql-vulnerability-assessment-now-supports-azure-sql-data-warehouse-and-azure-sql-database-managed-instance/) è un servizio di semplice utilizzo che consente di monitorare costantemente il data warehouse. Assicura un elevato livello di sicurezza in qualsiasi momento e la conformità ai criteri aziendali. Fornisce un report sulla sicurezza completo insieme a procedure di correzione di utilità pratica per ogni problema rilevato. Questo report semplifica la gestione in modo proattivo del livello di protezione del database e consente di concentrarsi sulle azioni con il maggiore impatto, anche se non si è esperti di sicurezza. Per gli ambienti dinamici in cui le modifiche sono frequenti e difficili da rilevare, la valutazione delle vulnerabilità è estremamente utile per rilevare le impostazioni che possono rendere il data warehouse vulnerabile agli attacchi.

## <a name="improved-availability-with-query-restartability"></a>Disponibilità migliorata grazie alla possibilità di riavviare le query
Durante l'esecuzione di una query possono verificarsi diversi problemi che ne impediscono il completamento. Un'interruzione della rete, un errore hardware o un altro tipo di disconnessione possono interrompere l'esecuzione della query. Ora SQL Data Warehouse supporta la riavviabilità delle query per le query di selezione a livello di passaggio o di istruzione. 

Grazie a questa funzionalità, una query in elaborazione che viene interrotta a causa di un errore verrà riavviata automaticamente. A seconda del numero di passaggi, della forma della query o del punto in cui l'esecuzione della query è stata interrotta, il motore di SQL Data Warehouse riavvierà l'intera query o ne riprenderà l'esecuzione dall'ultimo passaggio completato. Dal punto di vista dell'utente finale, la query viene semplicemente completata. 

## <a name="maintenance-scheduling-preview"></a>Pianificazione della manutenzione (anteprima)
La pianificazione della manutenzione di Azure SQL Data Warehouse è ora disponibile in anteprima. Questa nuova funzionalità si integra senza problemi con le notifiche di manutenzione pianificata dell'integrità dei servizi, il monitoraggio per il controllo dell'integrità risorse e il servizio di pianificazione della manutenzione di Azure SQL Data Warehouse. La pianificazione della manutenzione consente di pianificare una finestra temporale per ricevere al momento opportuno le nuove funzionalità, gli aggiornamenti e le patch.

La pianificazione della manutenzione sfrutta la piattaforma Monitoraggio di Azure e permette ai clienti di determinare come desiderano ricevere una notifica degli eventi di manutenzione imminenti e quali flussi automatizzati devono essere attivati per gestire i tempi di inattività e ridurre al minimo l'impatto sulle operazioni. Le notifiche possono essere inviate tramite posta elettronica o SMS. 

## <a name="wide-row-support-in-polybase"></a>Supporto per righe ampie in Polybase
Le linee guida generali per il caricamento di dati in SQL Data Warehouse indicano l'uso di [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/design-elt-data-loading#options-for-loading-with-polybase) con il relativo supporto del caricamento di dati parallelo. Questa versione supporta colonne più ampie, da 32 KB a 1 MB, consentendo di caricare tabelle con ampie dimensioni di riga. Il supporto per righe ampie semplifica il processo di caricamento dei dati per le tabelle con righe ampie.

> [!Note]
> Le dimensioni totali delle righe non possono essere superiori a 1 MB.

## <a name="additional-language-support"></a>Elementi del linguaggio aggiuntivi supportati
Questa versione introduce il supporto degli elementi del linguaggio T-SQL seguenti:

### <a name="stringsplit"></a>STRING_SPLIT
La funzione [STRING_SPLIT](https://docs.microsoft.com/sql/t-sql/functions/string-split-transact-sql) divide una stringa di caratteri usando il separatore specificato. STRING_SPLIT è utile negli scenari di caricamento di dati in cui una colonna può avere più valori da analizzare e inserire in un'altra tabella.

#### <a name="example"></a>Esempio
```sql
DECLARE @tags NVARCHAR(400) = 'clothing,road,,touring,bike';

SELECT
    value
FROM
    STRING_SPLIT(@tags, ',')
WHERE
    RTRIM(value) <> '';
```

### <a name="compressdecompress-functions"></a>Funzioni COMPRESS/DECOMPRESS
Le funzioni [COMPRESS](https://docs.microsoft.com/sql/t-sql/functions/compress-transact-sql) / [DECOMPRESS](https://docs.microsoft.com/sql/t-sql/functions/decompress-transact-sql) consentono di comprimere o decomprimere un input di stringa usando un algoritmo GZIP.

#### <a name="example"></a>Esempio

```sql
SELECT
    name [name_original]
    , COMPRESS(name) [name_compressed]
    , CAST(DECOMPRESS(COMPRESS(name)) AS NVARCHAR(MAX)) [name_decompressed]
FROM
    sys.objects;
```

### <a name="if-exists-clause-for-dropping-views"></a>Clausola IF EXISTS per l'eliminazione di viste
L'aggiunta della clausola IF EXISTS nell'istruzione [DROP VIEW](https://docs.microsoft.com/sql/t-sql/statements/drop-view-transact-sql) semplifica il codice T-SQL necessario per rimuovere una vista dal data warehouse. La sintassi IF EXISTS, quando applicata a un'istruzione DROP VIEW, elimina la vista se esistente o ignora l'istruzione se la vista non esiste.

#### <a name="example"></a>Esempio
```sql
DROP VIEW IF EXISTS dbo.TestView;
```
```
Message
--------------------------------------------------
Commands completed successfully.

```

## <a name="improved-compilation-time-for-singleton-inserts-and-ddl-statements"></a>Tempo di compilazione migliorato per gli inserimenti singleton e le istruzioni DDL 
Se si segue un modello tradizionale di estrazione, trasformazione e caricamento (ETL) per l'inserimento dei dati, il risultato è spesso l'esecuzione di un inserimento singleton ([INSERT-VALUES](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql)) in una tabella del database. Questa versione migliora le prestazioni delle operazioni di inserimento singleton riducendo il tempo di compilazione necessario per eseguire questo tipo di istruzione. In alcuni casi è stato osservato un tempo di compilazione fino a 3 volte più rapido. Questo miglioramento ridurrà il tempo necessario per eseguire un'istruzione di inserimento singleton. 

Il miglioramento interessa anche i data warehouse che contengono un numero elevato di oggetti, tramite un'analoga riduzione del tempo di compilazione delle query per le istruzioni DDL (Data Definition Language), incluse le operazioni CREATE, ALTER e DELETE. 

Infine, il miglioramento riduce il tempo di esecuzione complessivo delle istruzioni sulle tabelle che contengono un numero elevato di colonne. Si riduce il tempo di esecuzione del passaggio di compilazione della query, riducendo di conseguenza il tempo di esecuzione complessivo delle query.

## <a name="bug-fixes"></a>Correzioni di bug

| Title | DESCRIZIONE |
|:---|:---|
| **Correzione di un errore relativo alla creazione di statistiche sulle distribuzioni per i vincoli univoci** | Questa correzione risolve un errore riscontrato dagli utenti durante l'esecuzione dell'istruzione UPDATE STATISTICS con la sola tabella specificata, quando per la tabella era stato definito un vincolo univoco. |
| **Correzione di un errore relativo alla compilazione di query su tabelle esterne** | Questa correzione risolve un difetto del tempo di compilazione delle query che coinvolgono tabelle esterne.|
| **Correzione di un errore relativo all'esecuzione di un'istruzione con tipi large** | Questa correzione risolve un difetto della compilazione di istruzioni preparate con parametri dichiarati come tipi *large* (nvarchar(max), varchar(max) e varbinary(max)). |
| **Correzione di un errore relativo alle query con molti livelli di annidamento** | Questa correzione visualizza un messaggio di errore chiaro quando una query con molti livelli di annidamento supera i limiti di sistema.|
| **Correzione di un errore relativo agli errori in fase di compilazione che si verificano quando un'istruzione contiene una sottoquery correlata e una costante del tempo di esecuzione** |Questa correzione risolve un errore in fase di compilazione relativo alle query che contengono una particolare combinazione di sottoquery correlate e costanti del tempo di esecuzione (come GETDATE()).|
| **Correzione di un errore di timeout durante l'acquisizione dei blocchi di oggetti PDW e dello slot di concorrenza per le statistiche automatiche** |Questa correzione aumenta il timeout del blocco per impedire alle richieste di statistiche automatiche di bloccare per un lungo periodo le richieste di origine.|

## <a name="next-steps"></a>Passaggi successivi
Dopo aver appreso alcune informazioni su SQL Data Warehouse, vedere come [creare un SQL Data Warehouse][create a SQL Data Warehouse] rapidamente. Se non si ha familiarità con Azure, il [glossario di Azure][Azure glossary] può essere utile quando si incontrano termini nuovi. Oppure vedere alcune delle altre risorse disponibili per SQL Data Warehouse.  

* [Casi di successo dei clienti]
* [Blog]
* [Richieste di funzionalità]
* [Video]
* [Blog Customer Advisory Team]
* [Forum su Stack Overflow]
* [Twitter]


[Blog]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blog Customer Advisory Team]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Casi di successo dei clienti]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Richieste di funzionalità]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Forum su Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Video]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
