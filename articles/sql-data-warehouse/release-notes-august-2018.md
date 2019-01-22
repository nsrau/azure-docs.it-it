---
title: Note sulla versione di Azure SQL Data Warehouse - Agosto 2018 | Microsoft Docs
description: Note sulla versione di Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 08/13/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: 11125fb1c78852a048787e99d78bf4cb941184fa
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2019
ms.locfileid: "54320362"
---
# <a name="whats-new-in-azure-sql-data-warehouse-august-2018"></a>Novità di Azure SQL Data Warehouse Agosto 2018
Azure SQL Data Warehouse riceve continuamente miglioramenti. Questo articolo illustra le nuove funzionalità e le modifiche introdotte nel mese di agosto 2018.

## <a name="automatic-intelligent-insights"></a>Analisi intelligenti automatiche
Microsoft ha introdotto le [analisi intelligenti automatiche](https://azure.microsoft.com/blog/automatic-intelligent-insights-to-optimize-performance-with-sql-data-warehouse/) per soddisfare la promessa di automazione del cloud per il data warehouse. Non sarà più necessario monitorare il data warehouse per l'asimmetria dei dati e le statistiche non ottimali delle tabelle. Senza costi aggiuntivi, SQL Data Warehouse fornisce analisi intelligenti per tutte le istanze di seconda generazione. Eseguendo l'integrazione con [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations), è possibile ricevere automaticamente le procedure consigliate per migliorare le prestazioni dei carichi di lavoro attivi. SQL Data Warehouse analizza il carico di lavoro e visualizza consigli in base all'utilizzo. L'analisi avviene giornalmente consentendo quindi di monitorare i report sull'utilizzo e i consigli per migliorare il carico di lavoro.

È possibile visualizzare i consigli nel portale di Azure Advisor: ![Consigli nel portale di Azure Advisor per Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/4e205b6d-df04-48db-8eec-d591f2592cf4.png)

È possibile esaminare ogni categoria per visualizzare i consigli per l'avviso specifico: ![Consigli nel portale di Azure Advisor per Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/3c42426e-6969-46e3-9025-c34c0755a302.png)


## <a name="bug-fixes"></a>Correzioni di bug

| Title | DESCRIZIONE |
|:---|:---|
| **Errori potenziali relativi alle query quando il numero di suddivisioni supera il limite massimo** |Quando veniva raggiunto il limite superiore di 1 milione di suddivisioni di file, un'eccezione non gestita causava il dump del motore SQL e le query non riuscivano. Questa correzione consente di risolvere il problema gestendo l'eccezione nel modo corretto e restituendo un errore senza impedire di eseguire le query. |
| **Il valore predefinito di ExternalMoveReadersPerNode è stato aumentato per migliorare le prestazioni di caricamento** |Questo problema era causato dall'impostazione della proprietà ExternalMoveReadersPerNode che non era sincronizzata con l'impostazione di Service Fabric. Questa regressione comportava delle prestazioni ridotte di caricamento della seconda generazione. La correzione riporta le prestazioni di caricamento della seconda generazione ai parametri di progettazione ottimizzati.|


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