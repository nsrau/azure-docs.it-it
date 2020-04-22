---
title: Che cos'è Azure Synapse Analytics (in precedenza SQL Data Warehouse)?
description: Azure Synapse Analytics (in precedenza SQL Data Warehouse) è un servizio di analisi senza limiti che riunisce funzionalità aziendali di data warehousing e analisi di Big Data.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: a9506d45350a567e3643b6edd6afc7668662f6e5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416019"
---
# <a name="what-is-azure-synapse-analytics-formerly-sql-dw"></a>Che cos'è Azure Synapse Analytics (in precedenza SQL Data Warehouse)?

Azure Synapse è un servizio di analisi che riunisce funzionalità aziendali di data warehousing e analisi di Big Data. Offre la libertà di eseguire query sui dati in base alle proprie esigenze, usando risorse serverless su richiesta o con provisioning, su larga scala. Azure Synapse offre questi due mondi insieme a un'esperienza unificata per l'inserimento, la preparazione, la gestione e la distribuzione dei dati per esigenze immediate di business intelligence e Machine Learning.

Azure Synapse è costituito da quattro componenti:

- SQL Synapse: analisi completa basata su T-SQL - disponibile a livello generale
  - Pool SQL (pagamento per ogni DWU con provisioning)
  - SQL su richiesta (pagamento per ogni TB elaborato) - (anteprima)
- Spark: Apache Spark completamente integrato (anteprima)
- Pipeline di Synapse: Integrazione di dati ibridi (anteprima)
- Studio: esperienza utente unificata  (Anteprima)

> [!NOTE]
> Per accedere alle funzionalità di anteprima di Azure Synapse, richiedere l'accesso [qui](https://aka.ms/synapsepreview). Microsoft valuterà tutte le richieste e risponderà il prima possibile.
>
> Vedere la [documentazione di Azure Synapse (anteprima)](../overview-what-is.md).

## <a name="synapse-sql-pool-in-azure-synapse"></a>Pool SQL Synapse in Azure Synapse

Per pool SQL Synapse si intendono le funzionalità di data warehousing aziendali disponibili a livello generale in Azure Synapse.

Il pool SQL rappresenta una raccolta di risorse analitiche di cui viene effettuato il provisioning durante l'uso di SQL Synapse. Le dimensioni del pool SQL sono determinate dalle unità Data Warehouse (DWU).

Importare i Big Data con semplici query T-SQL [PolyBase](/sql/relational-databases/polybase/polybase-guide?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) e quindi sfruttare la potenza dell'elaborazione MPP per eseguire analisi ad alte prestazioni. Man mano che si procede con l'integrazione e l'analisi, il pool SQL Synapse diventerà il punto di riferimento dell'azienda da cui ricavare più rapidamente informazioni dettagliate più affidabili.  

## <a name="key-component-of-a-big-data-solution"></a>Componente chiave di una soluzione per Big Data

Il data warehouse è un componente chiave di una soluzione per Big Data end-to-end basata sul cloud.

![Soluzione di data warehouse](./media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png)

In una soluzione cloud per i dati, i dati vengono inseriti in archivi di Big Data da numerose origini. Una volta inseriti i dati in un archivio di Big Data, Hadoop, Spark e gli algoritmi di apprendimento automatico preparano i dati e ne eseguono il training. Quando i dati sono pronti per le analisi complesse, il pool SQL Synapse usa PolyBase per eseguire query sugli archivi di Big Data. PolyBase usa query T-SQL standard per inserire i dati in tabelle del pool SQL Synapse.

Il pool SQL Synapse archivia i dati in tabelle relazionali con archiviazione a colonne. Questo formato consente di ridurre notevolmente i costi di archiviazione dei dati e di migliorare le prestazioni di query. Una volta archiviati i dati, è possibile eseguire analisi su larga scala. Rispetto ai sistemi di database tradizionali, le query di analisi vengono completate in secondi invece che in minuti oppure in ore invece che in giorni.

I risultati delle analisi possono essere passati ad applicazioni o database di report in tutto il mondo. Gli analisti aziendali possono quindi ottenere informazioni dettagliate per prendere decisioni ben informate.

## <a name="next-steps"></a>Passaggi successivi

- Esplorare l'[architettura di Azure Synapse](massively-parallel-processing-mpp-architecture.md)
- [Creare un pool SQL](create-data-warehouse-portal.md) rapidamente
- [Caricare dati di esempio](load-data-from-azure-blob-storage-using-polybase.md).
- Esplorare i [video](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)

Oppure vedere alcune delle altre risorse disponibili per Azure Synapse.

- Eseguire ricerche nei [blog](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- Inviare [richieste di funzionalità](https://feedback.azure.com/forums/307516-sql-data-warehouse)
- [Creare un ticket di supporto](sql-data-warehouse-get-started-create-support-ticket.md)
- Eseguire ricerche nei [forum MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse)
- Eseguire ricerche nel [forum di Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw)
