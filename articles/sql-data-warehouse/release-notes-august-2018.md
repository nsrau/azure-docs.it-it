---
title: Note sulla versione di Azure SQL Data Warehouse - Agosto 2018 | Microsoft Docs
description: Note sulla versione di Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 08/06/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: 1f3b17f3163c29f9b9e1e47e14ccdbc1e37e1010
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2018
ms.locfileid: "39633657"
---
# <a name="whats-new-in-azure-sql-data-warehouse-august-2018"></a>Novità di Azure SQL Data Warehouse Agosto 2018
Azure SQL Data Warehouse riceve continuamente miglioramenti. Questo articolo illustra le nuove funzionalità e le modifiche introdotte nel mese di agosto 2018.

## <a name="automatic-intelligent-insights"></a>Analisi intelligenti automatiche
Microsoft ha introdotto le [analisi intelligenti automatiche](https://azure.microsoft.com/blog/automatic-intelligent-insights-to-optimize-performance-with-sql-data-warehouse/) per soddisfare la promessa di automazione del cloud per il data warehouse. Non sarà più necessario monitorare il data warehouse per l'asimmetria dei dati e le statistiche non ottimali delle tabelle. Senza costi aggiuntivi, SQL Data Warehouse fornisce analisi intelligenti per tutte le istanze di seconda generazione. Eseguendo l'integrazione con [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations), è possibile ricevere automaticamente le procedure consigliate per migliorare le prestazioni dei carichi di lavoro attivi. SQL Data Warehouse analizza il carico di lavoro e visualizza consigli in base all'utilizzo. L'analisi avviene giornalmente consentendo quindi di monitorare i report sull'utilizzo e i consigli per migliorare il carico di lavoro.

È possibile visualizzare i consigli nel portale di Azure Advisor: ![Azure Advisor Portal Recommendations for Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/4e205b6d-df04-48db-8eec-d591f2592cf4.png) (Consigli nel portale di Azure Advisor per Azure SQL Data Warehouse)

È possibile esaminare ogni categoria per visualizzare i consigli per l'avviso specifico: ![Azure Advisor Portal Recommendation Details for Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/3c42426e-6969-46e3-9025-c34c0755a302.png) (Dettagli dei consigli nel portale di Azure Advisor per Azure SQL Data Warehouse)

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
[Forum su Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Video]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md