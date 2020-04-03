---
title: Limiti di capacità - Azure Synapse Analytics (in precedenza SQL DW)Capacity limits - Azure Synapse Analytics (formerly SQL DW)
description: Valori massimi consentiti per vari componenti del pool Synapse SQL in Azure Synapse.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 2/19/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: f35a5da15ca1a672046844282626a6cb7b8ecbdf
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80583521"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-capacity-limits"></a>Limiti di capacità di Azure Synapse Analytics (in precedenza SQL DW)

Valori massimi consentiti per vari componenti di Azure Synapse.

## <a name="workload-management"></a>Gestione dei carichi di lavoro

| Category | Descrizione | Massimo |
|:--- |:--- |:--- |
| [Unità Data Warehouse (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Max DWU per una singola unità di pool SQL (data warehouse) | Prima generazione: DW6000<br></br>Seconda generazione: DW30000c |
| [Unità Data Warehouse (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |DTU predefinita per server |54.000<br></br>Per impostazione predefinita, ogni server SQL (ad esempio, myserver.database.windows.net) ha una quota DTU di 54.000, che consente fino a DW5000c. Questa quota è semplicemente un limite di sicurezza. È possibile aumentare la quota [creando un ticket](sql-data-warehouse-get-started-create-support-ticket.md) di supporto e selezionando *Quota* come tipo di richiesta.  Per calcolare le vostre esigenze di DTU, moltiplicare il 7.5 per il totale DWU necessario, o moltiplicare 9.5 per il totale cDWU necessario. Ad esempio:<br></br>DW6000 x 7,5 = 45.000 DTU<br></br>DW5000c x 9,5 : 47.500 DTU.<br></br>È possibile visualizzare l'utilizzo di DTU attuale nell'opzione SQL Server del portale. I database in pausa e non in pausa vengono conteggiati nella quota di DTU. |
| Connessione del database |Numero massimo di sessioni aperte simultanee |1024<br/><br/>Il numero di sessioni aperte simultanee varia in base alla DWU selezionata. DWU600c e superiore supportano un massimo di 1024 sessioni aperte. DWU500c e inferiore, supportano un limite massimo simultaneo di sessioni aperte di 512. Si noti che vi sono limiti nel numero di query che è possibile eseguire contemporaneamente. Quando si supera il limite di concorrenza, la richiesta viene inviata a una coda interna in cui resta in attesa di elaborazione. |
| Connessione del database |Memoria massima per le istruzioni preparate |20 MB |
| [Gestione dei carichi di lavoro](resource-classes-for-workload-management.md) |Numero massimo di query simultanee |128<br/><br/>  Verrà eseguito un massimo di 128 query simultanee e verranno accodate le query rimanenti.<br/><br/>Il numero di query simultanee può diminuire quando gli utenti vengono assegnati a classi di risorse più elevate o quando l'impostazione [dell'unità](memory-concurrency-limits.md) di data warehouse viene ridotta. Per alcune query, come le query DMV, l'esecuzione è sempre consentita e non influisce sul limite di query simultanee. Per altre informazioni sull'esecuzione di query simultanee, vedere l'articolo sui [valori massimi di concorrenza](memory-concurrency-limits.md). |
| [Tempdb](sql-data-warehouse-tables-temporary.md) |GB massimi |399 GB per DW100c. Pertanto, in DWU1000c, tempdb viene dimensionato a 3,99 TB. |
||||

## <a name="database-objects"></a>Oggetti di database

| Category | Descrizione | Massimo |
|:--- |:--- |:--- |
| Database |Dimensioni massime | Prima generazione: 240 TB compressi su disco. Questo spazio è indipendente dallo spazio di tempdb o del log ed è dedicato alle tabelle permanenti.  La compressione stimata per columnstore cluster è 5X.  Questa compressione consente al database di crescere fino a circa 1 PB quando tutte le tabelle sono columnstore cluster (tipo di tabella predefinito). <br/><br/> Gen2: archiviazione illimitata per le tabelle columnstore.Gen2: Unlimited storage for columnstore tables.  La parte del database nell'archivio righe è ancora limitata a 240 TB compressi su disco. |
| Tabella |Dimensioni massime |Dimensioni illimitate per le tabelle columnstore. <br>60 TB per le tabelle rowstore compresse su disco. |
| Tabella |Tabelle per ogni database | 100,000 |
| Tabella |Colonne per ogni tabella |1024 colonne |
| Tabella |Byte per colonna |Dipende dalla colonna [tipo di dati](sql-data-warehouse-tables-data-types.md). Per i tipi di dati carattere, MAX Limit può archiviare fino a 2 GB nell'archiviazione fuori pagina (overflow di riga).  I caratteri non Unicode, ad esempio il limite char o varchar, sono 8000 in una pagina di dati, mentre i caratteri Unicode come il limite nchar o nvarchar sono 4000 in una pagina di dati.  Usare le dimensioni di archiviazione delle pagine di dati per migliorare le prestazioni. |
| Tabella |Byte per riga, dimensioni definite |8060 byte<br/><br/>Il numero di byte per riga viene calcolato come per SQL Server, con la compressione pagina. Analogamente a SQL ServerSQL Server, è supportato l'archiviazione di overflow delle righe, che consente di eseguire il push delle colonne di **lunghezza variabile** all'esta dalla riga. Quando le righe di lunghezza variabile vengono inviate all'esterno delle righe, viene archiviata nel record principale solo una radice 24 byte. Per altre informazioni, vedere [Dati di overflow della riga che superano 8 KB](https://msdn.microsoft.com/library/ms186981.aspx). |
| Tabella |Partizioni per tabella |15.000<br/><br/>Per prestazioni elevate, è consigliabile ridurre al minimo il numero di partizioni necessarie garantendo al tempo stesso il supporto dei requisiti aziendali. Con l'aumentare del numero di partizioni, l'overhead per le operazioni DDL (Data Definition Language) e DML (Data Manipulation Language ) aumenta e le prestazioni rallentano. |
| Tabella |Caratteri per valore limite della partizione. |4000 |
| Indice |Indici non in cluster per tabella. |50<br/><br/>Si applica solo alle tabelle rowstore. |
| Indice |Indici in cluster per tabella. |1<br><br/>Si applica sia alle tabelle rowstore che columnstore. |
| Indice |Dimensioni della chiave indice. |900 byte.<br/><br/>Si applica solo agli indici rowstore.<br/><br/>È possibile creare indici in colonne varchar con una dimensione massima di oltre 900 byte se al momento della creazione dell'indice i dati esistenti in tali colonne non superano i 900 byte. Tuttavia, le azioni INSERT o UPDATE successive eseguite nelle colonne che causano un aumento delle dimensioni totali oltre i 900 byte avranno esito negativo. |
| Indice |Colonne chiave per indice. |16<br/><br/>Si applica solo agli indici rowstore. Gli indici columnstore in cluster includono tutte le colonne. |
| Statistiche |Dimensione dei valori combinati delle colonne. |900 byte. |
| Statistiche |Colonne per oggetto statistiche. |32 |
| Statistiche |Statistiche create per le colonne per tabella. |30.000 |
| Stored procedure |Livello massimo di annidamento. |8 |
| Visualizza |Colonne per ogni vista |1.024 |
||||

## <a name="loads"></a>Operazioni di caricamento

| Category | Descrizione | Massimo |
|:--- |:--- |:--- |
| Operazioni di caricamento di PolyBase |MB per riga |1<br/><br/>Polybase carica le righe di dimensioni inferiori a 1 MB. Il caricamento di tipi di dati LOB in tabelle con un indice CCI (Clustered Columnstore Index) non è supportato.<br/><br/> |
||||

## <a name="queries"></a>Query

| Category | Descrizione | Massimo |
|:--- |:--- |:--- |
| Query |Query in coda nelle tabelle utente |1000 |
| Query |Query simultanee nelle viste di sistema |100 |
| Query |Query in coda nelle viste di sistema |1000 |
| Query |Parametri massimi |2098 |
| Batch |Dimensione massima |65.536*4096 |
| Risultati SELECT |Colonne per riga |4096<br/><br/>Nel risultato SELECT non è possibile avere più di 4096 colonne per riga. Non è garantito che si possa avere sempre 4096. Se il piano di query richiede una tabella temporanea, potrebbe venire applicato il valore massimo di 1024 colonne per tabella. |
| SELECT |Sottoquery nidificate |32<br/><br/>In un'istruzione SELECT non è possibile avere più di 32 sottoquery nidificate. Non è garantito che si possa averne sempre 32. Ad esempio, un JOIN può introdurre una sottoquery nel piano di query. Il numero di sottoquery può essere limitato anche dalla memoria disponibile. |
| SELECT |Colonne per JOIN |1024 colonne<br/><br/>Nel JOIN non è mai possibile avere più di 1024 colonne. Non è garantito che si possa averne sempre 1024. Se il piano JOIN richiede una tabella temporanea con più colonne del risultato JOIN, il limite di 1024 viene applicato alla tabella temporanea. |
| SELECT |Byte per le colonne GROUP BY. |8060<br/><br/>Le colonne presenti nella clausola GROUP BY possono avere un massimo di 8060 byte. |
| SELECT |Byte per le colonne ORDER BY |8060 byte<br/><br/>Le colonne presenti nella clausola ORDER BY possono avere un massimo di 8060 byte. |
| Identificatori per istruzione |Numero di identificatori di riferimento |65.535<br/><br/> Il numero di identificatori che possono essere contenuti in una singola espressione di una query è limitato. Il superamento di questo numero genera un errore 8632 di SQL Server. Per altre informazioni, vedere [Errore interno: è stato raggiunto un limite di servizi di espressione](https://support.microsoft.com/help/913050/error-message-when-you-run-a-query-in-sql-server-2005-internal-error-a). |
| Valori letterali stringa | Numero di valori letterali stringa in un'istruzione | 20.000 <br/><br/>Il numero di costanti stringa in una singola espressione di una query è limitato. Il superamento di questo numero genera un errore 8632 di SQL Server.|
||||

## <a name="metadata"></a>Metadati

| Vista di sistema | Numero massimo di righe |
|:--- |:--- |
| sys.dm_pdw_component_health_alerts |10,000 |
| sys.dm_pdw_dms_cores |100 |
| sys.dm_pdw_dms_workers |Numero totale di ruoli di lavoro DMS per le 1000 richieste SQL più recenti. |
| sys.dm_pdw_errors |10,000 |
| sys.dm_pdw_exec_requests |10,000 |
| sys.dm_pdw_exec_sessions |10,000 |
| sys.dm_pdw_request_steps |Numero totale di passaggi per le 1000 richieste SQL più recenti archiviate in sys.dm_pdw_exec_requests. |
| sys.dm_pdw_os_event_logs |10,000 |
| sys.dm_pdw_sql_requests |Le 1000 richieste SQL più recenti archiviate in sys.dm_pdw_exec_requests. |
|||

## <a name="next-steps"></a>Passaggi successivi

Per suggerimenti sull'uso di Azure Synapse, vedere [Cheat Sheet](cheat-sheet.md).
