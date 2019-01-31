---
title: Note sulla versione di Azure SQL Data Warehouse - Dicembre 2018 | Microsoft Docs
description: Note sulla versione di Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 12/12/2018
ms.author: mausher
ms.reviewer: twounder
ms.openlocfilehash: 8e82e352ebea4634b1b99864245adcf606352657
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55469338"
---
# <a name="whats-new-in-azure-sql-data-warehouse-version---100101060"></a>Novità della versione di Azure SQL Data Warehouse - 10.0.10106.0
Azure SQL Data Warehouse (SQL DW) è in continuo miglioramento. Questo articolo illustra le nuove funzionalità e le modifiche introdotte in SQL DW versione 10.0.10106.0.

## <a name="query-restartability---ctas-and-insertselect"></a>Riavviabilità delle query - CTAS e Insert/Select
In rare situazioni (ad esempio, in caso di problemi di connessione di rete o di errori dei nodi) è possibile che le query eseguite in Azure SQL Data Warehouse abbiano esito negativo. Le istruzioni con tempi di esecuzione più lunghi, ad esempio [CREATE TABLE AS SELECT (CTAS)](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-ctas) e le operazioni INSERT-SELECT, sono più esposte a questo problema. Con questa versione, Azure SQL Data Warehouse implementa la logica di ripetizione dei tentativi per le istruzioni CTAS e INSERT-SELECT (oltre alle istruzioni SELECT annunciate in precedenza), consentendo al sistema di gestire in modo trasparente questi problemi temporanei e impedendo errori nelle query. Il numero di tentativi di ripetizione e l'elenco di errori temporanei gestiti sono configurati dal sistema.

## <a name="return-order-by-optimization"></a>Ottimizzazione dell'ordine di restituzione
Questa versione presenta un miglioramento delle prestazioni delle query SELECT…ORDER BY.  In precedenza, il motore di esecuzione delle query ordinava i risultati in ogni nodo di calcolo e li trasmetteva al nodo di controllo, che univa i risultati. Con i nuovi miglioramenti, invece, tutti i nodi di calcolo inviano i risultati a un singolo nodo di calcolo, che li unisce e restituisce all'utente i risultati ordinati.  Ne consegue un considerevole miglioramento delle prestazioni, soprattutto se l'insieme dei risultati delle query contiene un elevato numero di righe.

## <a name="data-movement-enhancements-for-partitionmove-and-broadcastmove"></a>Spostamento dei dati migliorato per PartitionMove e BroadcastMove
In Azure SQL Data Warehouse Gen2, il processo di spostamento dei dati di tipo ShuffleMove usa le tecniche di spostamento immediato dei dati descritte nel [blog sui miglioramenti delle prestazioni](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/).  Con questa versione, anche i tipi di spostamento dei dati PartitionMove e BroadcastMove usano queste tecniche di spostamento immediato dei dati.  Le query utente che usano questi tipi di processi di spostamento dei dati registreranno quindi un miglioramento delle prestazioni.  Per usufruire di questi miglioramenti delle prestazioni non è necessaria alcuna modifica a livello di codice.

## <a name="next-steps"></a>Passaggi successivi
Dopo aver appreso alcune informazioni su SQL Data Warehouse, vedere come [creare un SQL Data Warehouse][create a SQL Data Warehouse] rapidamente. Se non si ha familiarità con Azure, può essere utile consultare il [glossario di Azure][Azure glossary] per apprendere la nuova terminologia. Oppure vedere alcune delle altre risorse disponibili per SQL Data Warehouse.  

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
[Forum su Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Video]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
