---
title: Note sulla versione di Azure SQL Data Warehouse | Microsoft Docs
description: Note sulla versione di Azure SQL Data Warehouse.
services: sql-data-warehouse
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/09/2019
author: mlee3gsd
ms.author: anumjs
ms.reviewer: jrasnick
manager: craigg
ms.openlocfilehash: 0b0d302dcd86f18eef6bf7600dd93878d0d9bd99
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57902689"
---
# <a name="azure-sql-data-warehouse-release-notes"></a>Note sulla versione di Azure SQL Data Warehouse
Questo articolo presenta una sintesi delle nuove funzionalità e dei miglioramenti introdotti nelle versioni recenti di [Azure SQL Data Warehouse](sql-data-warehouse-overview-what-is.md). L'articolo elenca anche gli aggiornamenti del contenuto rilevanti non direttamente correlati alla versione ma pubblicati nello stesso intervallo di tempo. Per i miglioramenti apportati ad altri servizi di Azure, vedere [Aggiornamenti dei servizi](https://azure.microsoft.com/updates)

## <a name="sql-data-warehouse-version-100101060-january"></a>SQL Data Warehouse versione 10.0.10106.0 (gennaio)

### <a name="service-improvements"></a>Miglioramenti al servizio

| Miglioramenti al servizio | Dettagli |
| --- | --- |
|**Ottimizzazione dell'ordine di restituzione**|Questa versione presenta un miglioramento delle prestazioni delle query SELECT…ORDER BY.   Tutti i nodi di calcolo inviano ora i risultati a un singolo nodo di calcolo, che unisce e ordina i risultati. Questi ultimi vengono poi restituiti all'utente tramite il nodo di calcolo.  L'unione dei risultati tramite un singolo nodo consente di migliorare notevolmente le prestazioni quando il set di risultati delle query contiene un numero elevato di righe. In precedenza, il motore di esecuzione delle query ordinava i risultati in ogni nodo di calcolo e li trasmetteva al nodo di controllo, che univa i risultati.|
|**Spostamento dei dati migliorato per PartitionMove e BroadcastMove**|In Azure SQL Data Warehouse Gen2, il processo di spostamento dei dati di tipo ShuffleMove usa le tecniche di spostamento immediato dei dati descritte nel [blog sui miglioramenti delle prestazioni](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/). Con questa versione, anche i tipi di spostamento dei dati PartitionMove e BroadcastMove usano queste tecniche di spostamento immediato dei dati. Le query utente che usano questi tipi di processi di spostamento dei dati registreranno quindi un miglioramento delle prestazioni. Per usufruire di questi miglioramenti delle prestazioni non è necessaria alcuna modifica a livello di codice.|
|**Bug rilevanti**|Versione di Azure SQL Data Warehouse non corretta: 'SELECT @@VERSION' può restituire la versione non corretta, 10.0.9999.0. La versione corretta per la versione corrente è 10.0.10106.0. Questo bug è stato segnalato ed è in fase di revisione.

### <a name="documentation-improvements"></a>Miglioramenti alla documentazione

| Miglioramenti alla documentazione | Dettagli |
| --- | --- |
|Nessuno | |
| | |

## <a name="next-steps"></a>Passaggi successivi
- [Creare un Azure SQL Data Warehouse](./create-data-warehouse-portal.md)

## <a name="more-information"></a>Altre informazioni
- [Blog - Azure SQL Data Warehouse](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [Blog Customer Advisory Team](https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/)
- [Casi di successo dei clienti](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [Forum su Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)
- [Video](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [glossario di Azure](../azure-glossary-cloud-terminology.md)
