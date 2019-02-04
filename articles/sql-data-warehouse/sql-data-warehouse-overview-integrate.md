---
title: Creare soluzioni integrate con SQL Data Warehouse | Documentazione Microsoft
description: 'Strumenti e partner con soluzioni che interagiscono con SQL Data Warehouse. '
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 08e144022c79182a63fd99dbce48a5e505aba3f2
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55477999"
---
# <a name="integrate-other-services-with-sql-data-warehouse"></a>Integrare altri servizi con SQL Data Warehouse
Oltre alle funzionalità di base, SQL Data Warehouse assicura agli utenti l'integrazione con molti altri servizi in Azure. Alcuni di questi servizi includono:

* Power BI
* Data factory di Azure
* Azure Machine Learning
* Analisi di flusso di Azure

SQL Data Warehouse garantisce l'integrazione con sempre più servizi di Azure e sempre più [partner di integrazione](sql-data-warehouse-partner-data-integration.md).

## <a name="power-bi"></a>Power BI
L'integrazione di Power BI consente di combinare la potenza di calcolo di SQL Data Warehouse con le funzionalità di creazione di report dinamici e visualizzazione di Power BI. L’integrazione di Power BI include attualmente:

* **Connessione diretta**: Una connessione più avanzata con distribuzione logica su SQL Data Warehouse. Questa funzionalità fornisce un'analisi più rapida su vasta scala.
* **Apri in Power BI**: Il pulsante "Apri in Power BI" passa informazioni sull'istanza a Power BI per una modalità di connessione semplificata.

Per altre informazioni, vedere [Integrazione con Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md) o fare riferimento alla [documentazione di Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/06/24/exploring-azure-sql-data-warehouse-with-power-bi.aspx).

## <a name="azure-data-factory"></a>Data factory di Azure
Azure Data Factory offre agli utenti una piattaforma gestita per creare pipeline di estrazione e caricamento complesse. L'integrazione di SQL Data Warehouse con Azure Data Factory include:

* **Procedure di archiviazione**: Orchestrare l'esecuzione delle procedure di archiviazione in SQL Data Warehouse.
* **Copia**: Usare ADF per spostare dati in SQL Data Warehouse. Questa operazione può usare un meccanismo di spostamento dati ADF standard oppure PolyBase in background. 

Per altre informazioni, vedere [Integrazione con Azure Data Factory](https://docs.microsoft.com/en-us/azure/data-factory/load-azure-sql-data-warehouse?toc=/azure/sql-data-warehouse/toc.json).

## <a name="azure-machine-learning"></a>Azure Machine Learning
Azure Machine Learning è un servizio di analisi completamente gestito, che consente di creare modelli complessi usando un ampio set di strumenti di previsione. SQL Data Warehouse è supportato come origine e destinazione per questi modelli con le funzionalità seguenti:

* **Lettura dei dati**: Guida i modelli su larga scala mediante T-SQL su SQL Data Warehouse.
* **Scrittura dei dati**: Registra le modifiche da qualsiasi modello a SQL Data Warehouse.

Per altre informazioni, vedere [Integrazione con Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Analisi di flusso di Azure
L’Analisi dei flussi di Azure è un'infrastruttura complessa, completamente gestita per l'elaborazione e l'utilizzo di dati degli eventi generati da Hub eventi di Azure.  L’integrazione con SQL Data Warehouse consente la trasmissione di dati in modo efficace da elaborare e archiviare insieme ai dati relazionali permettendo un’analisi più approfondita, più avanzata.  

* **Output del processo:** Inviare l'output da processi di analisi di flusso direttamente a SQL Data Warehouse.

Per altre informazioni, vedere [Integrazione con Analisi di flusso di Azure](sql-data-warehouse-integrate-azure-stream-analytics.md).

## <a name="next-steps"></a>Passaggi successivi
Per l'integrazione con il database SQL di Azure, vedere [Configurare la query elastica del database SQL](tutorial-elastic-query-with-sql-datababase-and-sql-data-warehouse.md).

