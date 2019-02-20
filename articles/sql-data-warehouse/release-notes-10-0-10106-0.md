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
ms.openlocfilehash: 51932ebf7d5bdc6830098ce7136a3eee7255ffe1
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2019
ms.locfileid: "56245508"
---
# <a name="azure-sql-data-warehouse-release-notes"></a>Note sulla versione di Azure SQL Data Warehouse
Questo articolo presenta una sintesi delle nuove funzionalità e dei miglioramenti introdotti nelle versioni recenti di [SQL Server in macchine virtuali di Azure](sql-data-warehouse-overview-what-is.md). L'articolo elenca anche gli aggiornamenti del contenuto rilevanti non direttamente correlati alla versione ma pubblicati nello stesso tempo. Per i miglioramenti apportati ad altri servizi di Azure, vedere [Aggiornamenti dei servizi](https://azure.microsoft.com/updates)

## <a name="sql-data-warehouse-version-100101060-january"></a>SQL Data Warehouse versione 10.0.10106.0 (gennaio)

### <a name="service-improvements"></a>Miglioramenti al servizio

| Miglioramenti al servizio | Dettagli |
| --- | --- |
| **Riavviabilità delle query - CTAS e Insert/Select** | In rare situazioni (ad esempio, in caso di problemi di connessione di rete o di errori dei nodi) è possibile che le query eseguite in Azure SQL Data Warehouse abbiano esito negativo. Le istruzioni con tempi di esecuzione più lunghi, ad esempio [CREATE TABLE AS SELECT (CTAS)](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-ctas) e le operazioni INSERT-SELECT, sono più esposte a questo problema. Con questa versione, Azure SQL Data Warehouse implementa la logica di ripetizione dei tentativi per le istruzioni CTAS e INSERT-SELECT, oltre alle istruzioni SELECT annunciate in precedenza. Le modifiche consentono al servizio di gestire i problemi temporanei in modo trasparente ed evitare errori nelle query. Il numero di tentativi di ripetizione e l'elenco di errori temporanei gestiti sono configurati dal sistema.|
|**Ottimizzazione dell'ordine di restituzione**|Questa versione presenta un miglioramento delle prestazioni delle query SELECT…ORDER BY.   Tutti i nodi di calcolo inviano ora i risultati a un singolo nodo di calcolo, che unisce e ordina i risultati. Questi ultimi vengono poi restituiti all'utente tramite il nodo di calcolo.  L'unione dei risultati tramite un singolo nodo consente di migliorare notevolmente le prestazioni quando il set di risultati delle query contiene un numero elevato di righe. In precedenza, il motore di esecuzione delle query ordinava i risultati in ogni nodo di calcolo e li trasmetteva al nodo di controllo, che univa i risultati.|
|**Spostamento dei dati migliorato per PartitionMove e BroadcastMove**|In Azure SQL Data Warehouse Gen2, il processo di spostamento dei dati di tipo ShuffleMove usa le tecniche di spostamento immediato dei dati descritte nel [blog sui miglioramenti delle prestazioni](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/). Con questa versione, anche i tipi di spostamento dei dati PartitionMove e BroadcastMove usano queste tecniche di spostamento immediato dei dati. Le query utente che usano questi tipi di processi di spostamento dei dati registreranno quindi un miglioramento delle prestazioni. Per usufruire di questi miglioramenti delle prestazioni non è necessaria alcuna modifica a livello di codice.|

### <a name="documentation-improvements"></a>Miglioramenti alla documentazione

| Miglioramenti alla documentazione | Dettagli |
| --- | --- |
|Nessuno | |
| | |

## <a name="next-steps"></a>Passaggi successivi
[Creare un Azure SQL Data Warehouse](./create-data-warehouse-portal.md)

## <a name="more-information"></a>Altre informazioni
- [Blog - Azure SQL Data Warehouse](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [Blog Customer Advisory Team](https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/)
- [Casi di successo dei clienti](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [Forum su Stack Overflow](http://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)
- [Video](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [Glossario di Azure](../azure-glossary-cloud-terminology.md)