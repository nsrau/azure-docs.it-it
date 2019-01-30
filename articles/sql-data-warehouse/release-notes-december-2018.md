---
title: Note sulla versione di Azure SQL Data Warehouse - Dicembre 2018 | Microsoft Docs
description: Note sulla versione di Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 12/12/2018
ms.author: mausher
ms.reviewer: twounder
ms.openlocfilehash: b897b50edf4d5a7eeabacc6da1505e165f2bb21a
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54431742"
---
# <a name="whats-new-in-azure-sql-data-warehouse-december-2018"></a>Novità di Azure SQL Data Warehouse Dicembre 2018
Azure SQL Data Warehouse riceve continuamente miglioramenti. Questo articolo illustra le nuove funzionalità e le modifiche introdotte nel mese di dicembre 2018.

## <a name="virtual-network-service-endpoints-generally-available"></a>Disponibilità generale degli endpoint del servizio Rete virtuale
Questa versione include la disponibilità generale di endpoint del servizio Rete virtuale (VNet) per Azure SQL Data Warehouse in tutte le aree di Azure. Gli endpoint del servizio Rete virtuale permettono di isolare la connettività a un server logico da una subnet o un set di subnet specifico nella rete virtuale. Il traffico verso Azure SQL Data Warehouse dalla rete virtuale rimane sempre all'interno della rete backbone di Azure. Questa route diretta è preferibile rispetto a eventuali route specifiche che trasmettono il traffico Internet tramite appliance virtuali o in locale. Non è prevista alcuna fatturazione aggiuntiva per l'accesso alla rete virtuale tramite gli endpoint di servizio. Il modello di prezzo corrente per [Azure SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/) si applica così com'è.

Con questa versione abbiamo abilitato anche la connettività PolyBase ad [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) (ADLS) tramite il driver del [file system BLOB di Azure](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-abfs-driver) (ABFS). Azure Data Lake Storage Gen2 è dotato di tutte le qualità necessarie per l'intero ciclo di vita dei dati di analisi di Archiviazione di Azure. Le funzionalità dei due servizi di archiviazione di Azure esistenti, Archiviazione BLOB di Azure e Azure Data Lake Store Gen1, sono state riunite. Le funzionalità di [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index), ad esempio la semantica dei file system, la sicurezza a livello di file e la scalabilità, sono state combinate con le funzionalità di archiviazione a basso costo e a più livelli e le funzionalità di elevata disponibilità/ripristino di emergenza di [Archiviazione BLOB di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). 

Con Polybase è anche possibile importare i dati in Azure SQL Data Warehouse da Archiviazione di Azure con protezione per la rete virtuale. PolyBase supporta anche l'esportazione di dati da Azure SQL Data Warehouse ad Archiviazione di Azure con protezione per la rete virtuale. 

Per altre informazioni sugli endpoint del servizio Rete virtuale in Azure SQL Data Warehouse, fare riferimento al [post del blog](https://azure.microsoft.com/blog/general-availability-of-vnet-service-endpoints-for-azure-sql-data-warehouse/) o alla [documentazione](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json).

## <a name="automatic-performance-monitoring-preview"></a>Monitoraggio automatico delle prestazioni (anteprima)
[Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?view=sql-server-2017) è ora disponibile in anteprima per Azure SQL Data Warehouse. Query Store è progettato per facilitare la risoluzione dei problemi relativi alle prestazioni delle query, tenendo traccia delle query, dei piani di query, delle statistiche di runtime e della cronologia delle query, per monitorare l'attività e le prestazioni del data warehouse. Query Store è un set di archivi interni e di viste a gestione dinamica (DMV) che consentono di:

- Identificare e ottimizzare le query che hanno il maggior consumo di risorse
- Identificare e migliorare i carichi di lavoro ad hoc
- Valutare le prestazioni delle query e l'impatto sul piano che hanno le modifiche apportate a statistiche, indici o dimensioni del sistema (impostazione della DWU)
- Visualizzare il testo completo delle query per tutte le query eseguite

Query Store contiene tre archivi effettivi:  
- Un archivio piani per rendere persistenti le informazioni sul piano di esecuzione 
- Un archivio delle statistiche di runtime per rendere persistenti le informazioni sulle statistiche di esecuzione
- Un archivio delle statistiche di attesa per rendere persistenti le informazioni sulle statistiche di attesa. 

SQL Data Warehouse gestisce automaticamente questi archivi e offre un numero illimitato di query archiviate negli ultimi sette giorni senza costi aggiuntivi. Abilitare Query Store è semplice come eseguire un'istruzione ALTER DATABASE T-SQL:

```sql
ALTER DATABASE [Database Name] SET QUERY_STORE = ON;
```
Per altre informazioni su Query Store in Azure SQL Data Warehouse, vedere l'articolo [Monitoraggio delle prestazioni con Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?view=sql-server-2017)e le viste DMV di Query Store, ad esempio [sys.query_store_query](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-query-transact-sql?view=sql-server-2017). Di seguito è riportato il [post di blog](https://azure.microsoft.com/blog/automatic-performance-monitoring-in-azure-sql-data-warehouse-with-query-store/) che annuncia il rilascio.

## <a name="lower-compute-tiers-for-azure-sql-data-warehouse-gen2"></a>I livelli inferiori di calcolo di Azure SQL Data Warehouse Gen2
Azure SQL Data Warehouse Gen2 supporta ora i livelli inferiori di calcolo. I clienti possono scoprire le principali funzionalità di prestazioni, flessibilità e sicurezza di Azure SQL Data Warehouse, partendo da 100 DWU ([Unità Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/what-is-a-data-warehouse-unit-dwu-cdwu)) a elevato utilizzo di calcolo e ridimensionare fino a 30.000 DWU a elevato utilizzo di calcolo in pochi minuti. Da metà dicembre 2018 i clienti possono usufruire delle prestazioni e della flessibilità di Gen2 con livelli inferiori di calcolo in alcune [aree](https://docs.microsoft.com/azure/sql-data-warehouse/gen2-lower-tier-regions). Le altre aree verranno rese disponibili nell'arco del 2019.

Rilascinando il punto di ingresso per il data warehouse di nuova generazione, Microsoft apre le porte ai clienti orientati al valore che desiderano valutare tutti i vantaggi di un data warehouse sicuro e a prestazioni elevate, senza dover intuire quale sia l'ambiente di prova più appropriato. I clienti possono cominciare da un minimo di 100 DWU a elevato utilizzo di calcolo, fino all'attuale punto di ingresso di 500 DWU a elevato utilizzo di calcolo. SQL Data Warehouse Gen2 continua a supportare le operazioni di pausa e ripresa e va oltre la semplice flessibilità di calcolo. Gen2 supporta anche la capacità di archiviazione a colonne illimitata insieme a una memoria per query 2,5 volte superiore, fino a 128 query simultanee e funzionalità di [memorizzazione adattiva nella cache](https://azure.microsoft.com/blog/adaptive-caching-powers-azure-sql-data-warehouse-performance-gains/). Queste funzionalità offrono prestazioni in media cinque volte superiori rispetto alla stessa unità Data Warehouse su Gen1, a pari prezzo. I backup con ridondanza geografica sono standard per Gen2 con protezione dei dati garantita integrata. Azure SQL Data Warehouse Gen2 è scalabile in base alle esigenze.

## <a name="columnstore-background-merge"></a>Unione in background di columnstore
Per impostazione predefinita, Azure SQL Data Warehouse (Azure SQL DW) archivia i dati in formato a colonne, con micro-partizioni denominate [rowgroup](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-memory-optimizations-for-columnstore-compression). In alcuni casi, a causa di vincoli di memoria al momento della compilazione dell'indice o del caricamento dei dati, è possibile che i rowgroup vengano compressi con una dimensione inferiore di quella ottimale composta da un milione di righe. È possibile anche che i rowgroup vengano frammentati a seguito di eliminazioni. Rowgroup piccoli o frammentati possono determinare un maggiore consumo di memoria, oltre a un'esecuzione inefficiente delle query. Con questa versione di Azure SQL Data Warehouse, l'attività di manutenzione in background di columnstore unisce rowgroup compressi di piccole dimensioni in rowgroup più grandi, in modo da migliorare l'utilizzo della memoria e velocizzare l'esecuzione delle query.

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
