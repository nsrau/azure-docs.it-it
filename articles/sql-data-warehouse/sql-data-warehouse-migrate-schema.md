---
title: Eseguire la migrazione dello schema in SQL Data Warehouse | Documentazione Microsoft
description: Suggerimenti per la migrazione dello schema in Azure SQL Data Warehouse per lo sviluppo di soluzioni.
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: 538b60c9-a07f-49bf-9ea3-1082ed6699fb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 10/31/2016
ms.author: joeyong;barbkess
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 07ca2321852e276502187e768177e7e82bdfd080
ms.contentlocale: it-it
ms.lasthandoff: 06/30/2017

---
# <a name="migrate-your-schemas-to-sql-data-warehouse"></a>Eseguire la migrazione degli schemi a SQL Data Warehouse
Indicazioni per la migrazione degli schemi SQL a SQL Data Warehouse. 

## <a name="plan-your-schema-migration"></a>Pianificare la migrazione dello schema

Per pianificare una migrazione vedere la [panoramica delle tabelle][table overview] per aspetti della creazione delle tabelle come statistiche, distribuzione, partizionamento e indicizzazione.  Vengono anche presentate alcune [funzionalità non supportate per le tabelle][unsupported table features] e le soluzioni alternative.

## <a name="use-user-defined-schemas-to-consolidate-databases"></a>Usare gli schemi definiti dall'utente per consolidare i database

È probabile che carico di lavoro esistente includa più di un database. Ad esempio un data warehouse di SQL Server può includere un database di staging, un database del data warehouse e alcuni database del data mart. In questa topologia ogni database viene eseguito come carico di lavoro separato con criteri di protezione separati.

Al contrario, SQL Data Warehouse esegue tutto il carico di lavoro del data warehouse all'interno di un database. I join tra database non sono consentiti. SQL Data Warehouse prevede pertanto che tutte le tabelle usate dal data warehouse siano archiviate in un unico database.

È consigliabile usare schemi definiti dall'utente per consolidare il carico di lavoro esistente in un unico database. Per esempi, vedere [Schemi definiti dall'utente](sql-data-warehouse-develop-user-defined-schemas.md)

## <a name="use-compatible-data-types"></a>Usare tipi di dati compatibili
Modificare i tipi di dati in modo che siano compatibili con SQL Data Warehouse. Per l'elenco dei tipi di dati supportati e non supportati, vedere [Tipi di dati][data types]. L'argomento offre soluzioni alternative per i tipi non supportati. Offre anche una query per identificare i tipi esistenti che non sono supportati in SQL Data Warehouse.

## <a name="minimize-row-size"></a>Ridurre al minimo le dimensioni delle righe
Per prestazioni ottimali, ridurre al minimo la lunghezza delle righe delle tabelle. Dato che a righe più brevi corrispondono prestazioni migliori, usare i tipi di dati più brevi che funzionano per i dati. 

Per la larghezza della riga della tabella, PolyBase ha un limite pari a 1 MB.  Se si prevede di caricare dati in SQL Data Warehouse con PolyBase, aggiornare le tabelle in modo che le righe abbiano una larghezza massima inferiore a 1 MB. 

<!--
- For example, this table uses variable length data but the largest possible size of the row is still less than 1 MB. PolyBase will load data into this table.

- This table uses variable length data and the defined row width is less than one MB. When loading rows, PolyBase allocates the full length of the variable-length data. The full length of this row is greater than one MB.  PolyBase will not load data into this table.  

-->

## <a name="specify-the-distribution-option"></a>Specificare l'opzione di distribuzione
SQL Data Warehouse è un sistema di database distribuito. Ogni tabella è distribuita o replicata nei nodi di calcolo. Un'opzione della tabella consente di specificare la modalità di distribuzione dei dati. Le scelte disponibili sono round robin, replica o distribuzione hash. Ognuna presenta vantaggi e svantaggi. Se non si specifica l'opzione di distribuzione, SQL Data Warehouse usa round robin come impostazione predefinita.

- La modalità round robin è la modalità predefinita. È la più semplice da usare e carica i dati alla massima velocità, ma lo spostamento di dati richiesto dai join determina un rallentamento delle prestazioni delle query.
- Nell'approccio con tabelle replicate, viene archiviata una copia della tabella in ogni nodo di calcolo. Le tabelle replicate sono efficienti perché non richiedono lo spostamento di dati per i join e le aggregazioni. Tuttavia richiedono risorse di archiviazione aggiuntive e di conseguenza sono la scelta migliore per tabelle di dimensioni contenute.
- La distribuzione hash distribuisce le righe su tutti i nodi tramite una funzione hash. Le tabelle con distribuzione hash sono il nucleo di SQL Data Warehouse e sono progettate per garantire alte prestazioni per le query su tabelle di grandi dimensioni. Questa opzione richiede un certo grado di pianificazione nella scelta della colonna ottimale per la distribuzione dei dati. Se tuttavia al primo tentativo non si sceglie la colonna migliore, è possibile ridistribuire facilmente i dati in base a un'altra colonna. 

Per scegliere la migliore opzione di distribuzione per ogni tabella, vedere [Tabelle con distribuzione](sql-data-warehouse-tables-distribute.md).


## <a name="next-steps"></a>Passaggi successivi
Dopo la migrazione dello schema del database a SQL Data Warehouse, passare a uno degli articoli seguenti:

* [Eseguire la migrazione dei dati][Migrate your data]
* [Eseguire la migrazione del codice][Migrate your code]

Per altre informazioni, vedere l'articolo con le [procedure consigliate][best practices] di SQL Data Warehouse.

<!--Image references-->

<!--Article references-->
[Migrate your code]: ./sql-data-warehouse-migrate-code.md
[Migrate your data]: ./sql-data-warehouse-migrate-data.md
[best practices]: ./sql-data-warehouse-best-practices.md
[table overview]: ./sql-data-warehouse-tables-overview.md
[unsupported table features]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[data types]: ./sql-data-warehouse-tables-data-types.md
[unsupported data types]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types

<!--MSDN references-->


<!--Other Web references-->

