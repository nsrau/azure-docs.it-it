---
title: Che cos'è Azure Synapse Analytics (in precedenza SQL Data Warehouse)? | Microsoft Docs
description: Azure Synapse Analytics (in precedenza SQL Data Warehouse) è un servizio di analisi senza limiti che riunisce funzionalità aziendali di data warehousing e analisi di Big Data.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: d10ea99e2dc8513a9cfebec782535f9e3185a3b9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496293"
---
# <a name="what-is-azure-synapse-analytics-formerly-sql-dw"></a>Che cos'è Azure Synapse Analytics (in precedenza SQL Data Warehouse)?

Azure Synapse è un servizio di analisi senza limiti che riunisce funzionalità aziendali di data warehousing e analisi di Big Data. Offre la libertà di eseguire query sui dati in base alle proprie esigenze, usando risorse serverless su richiesta o con provisioning, su larga scala. Azure Synapse offre questi due mondi insieme a un'esperienza unificata per l'inserimento, la preparazione, la gestione e la distribuzione dei dati per esigenze immediate di business intelligence e Machine Learning

Azure Synapse è costituito da quattro componenti:
- Analisi SQL: analisi completa basata su T-SQL - disponibile a livello generale
    - Pool SQL (pagamento per ogni DWU con provisioning) 
    - SQL su richiesta (pagamento per ogni TB elaborato) - (anteprima)
- Spark: Apache Spark completamente integrato (anteprima) 
- Integrazione dei dati: Integrazione di dati ibridi (anteprima)
- Studio: esperienza utente unificata  (Anteprima)

> [!NOTE]
> Per accedere alle funzionalità di anteprima di Azure Synapse, richiedere l'accesso [qui](https://aka.ms/synapsepreview). Microsoft valuterà tutte le richieste e risponderà il prima possibile.

## <a name="sql-analytics-and-sql-pool-in-azure-synapse"></a>Analisi SQL e pool SQL in Azure Synapse

Analisi SQL si riferisce alle funzionalità di data warehousing aziendali disponibili a livello generale con Azure Synapse. 

Il pool SQL rappresenta una raccolta di risorse analitiche di cui viene effettuato il provisioning durante l'uso di Analisi SQL. Le dimensioni del pool SQL sono determinate dalle unità Data Warehouse (DWU).

Importare i Big Data con semplici query T-SQL [PolyBase](/sql/relational-databases/polybase/polybase-guide?view=sql-server-2017&viewFallbackFrom=azure-sqldw-latest) e quindi sfruttare la potenza dell'elaborazione MPP per eseguire analisi ad alte prestazioni. Man mano che si procede con l'integrazione e l'analisi, Analisi SQL diventerà il punto di riferimento dell'azienda da cui ricavare più rapidamente informazioni dettagliate più affidabili.  

## <a name="key-component-of-a-big-data-solution"></a>Componente chiave di una soluzione per Big Data

Il data warehouse è un componente chiave di una soluzione per Big Data end-to-end basata sul cloud.

![Soluzione di data warehouse](media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png) 

In una soluzione cloud per i dati, i dati vengono inseriti in archivi di Big Data da numerose origini. Una volta inseriti i dati in un archivio di Big Data, Hadoop, Spark e gli algoritmi di apprendimento automatico preparano i dati e ne eseguono il training. Quando i dati sono pronti per le analisi complesse, Analisi SQL usa PolyBase per eseguire query sugli archivi di Big Data. PolyBase usa query T-SQL standard per inserire i dati in tabelle di Analisi SQL.
 
Analisi SQL archivia i dati in tabelle relazionali con archiviazione a colonne. Questo formato consente di ridurre notevolmente i costi di archiviazione dei dati e di migliorare le prestazioni di query. Una volta archiviati i dati, è possibile eseguire analisi su larga scala. Rispetto ai sistemi di database tradizionali, le query di analisi vengono completate in secondi invece che in minuti oppure in ore invece che in giorni. 

I risultati delle analisi possono essere passati ad applicazioni o database di report in tutto il mondo. Gli analisti aziendali possono quindi ottenere informazioni dettagliate per prendere decisioni ben informate.

## <a name="next-steps"></a>Passaggi successivi

- Esplorare l'[architettura di Azure Synapse](/azure/sql-data-warehouse/massively-parallel-processing-mpp-architecture)
- [Creare un pool SQL](create-data-warehouse-portal.md) rapidamente
- [Caricare dati di esempio][load sample data].
- Esplorare i [video](/azure/sql-data-warehouse/sql-data-warehouse-videos)

Oppure vedere alcune delle altre risorse disponibili per Azure Synapse.  
* Eseguire ricerche nei [blog]
* Inviare [richieste di funzionalità]
* Eseguire ricerche nei [Blog Customer Advisory Team]
* [Creare un ticket di supporto]
* Eseguire ricerche nei [forum MSDN]
* Eseguire ricerche nel [Forum su Stack Overflow]


<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Creare un ticket di supporto]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a data warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[Azure Synapse Analytics solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Blog]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blog Customer Advisory Team]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Richieste di funzionalità]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Forum MSDN]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSQLDataWarehouse
[Forum su Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for Azure Synapse Analytics]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/
