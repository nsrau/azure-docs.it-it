---
title: Compila soluzioni integrate
description: Strumenti e partner della soluzione che si integrano con un pool SQL sinapsi.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 2f6e091b6e0285bea5fef9e4d0be40faec936c6b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80633134"
---
# <a name="integrate-other-services-with-a-sql-analytics-data-warehouse"></a>Integrare altri servizi con un data warehouse SQL Analytics

La funzionalità analisi SQL in Azure sinapsi Analytics consente agli utenti di eseguire l'integrazione con molti altri servizi in Azure. Con SQL Analytics è possibile creare un data warehouse tramite la risorsa del pool SQL, che può quindi usare diversi servizi aggiuntivi, alcuni dei quali:

* Power BI
* Data factory di Azure
* Azure Machine Learning
* Analisi di flusso di Azure

Per altre informazioni su Integration Services in Azure, vedere l'articolo [partner di integrazione](sql-data-warehouse-partner-data-integration.md).

## <a name="power-bi"></a>Power BI

Power BI integrazione consente di combinare la potenza di calcolo di una data warehouse con la visualizzazione e la creazione di report dinamici dei Power BI. L’integrazione di Power BI include attualmente:

* Connessione **diretta**: una connessione più avanzata con distribuzione logici rispetto a un data warehouse sottoposta a provisioning tramite il pool SQL. Questa funzionalità fornisce un'analisi più rapida su vasta scala.
* **Apri in Power bi**: il pulsante ' apri in Power bi ' passa le informazioni sull'istanza a Power BI per una modalità semplificata per la connessione.

Per altre informazioni, vedere [Integrazione con Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md) o fare riferimento alla [documentazione di Power BI](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/).

## <a name="azure-data-factory"></a>Data factory di Azure

Azure Data Factory offre agli utenti una piattaforma gestita per creare pipeline di estrazione e caricamento complesse. L'integrazione del pool SQL con Azure Data Factory include:

* **Stored procedure**: orchestrare l'esecuzione di stored procedure.
* **Copy (copia**): usare ADF per spostare i dati nel pool SQL. Questa operazione può usare un meccanismo di spostamento dati ADF standard oppure PolyBase in background.

Per altre informazioni, vedere [Integrazione con Azure Data Factory](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning è un servizio di analisi completamente gestito, che consente di creare modelli complessi usando un ampio set di strumenti di previsione. Il pool SQL è supportato sia come origine che come destinazione per questi modelli e presenta le funzionalità seguenti:

* **Leggere i dati:** Guidare i modelli su larga scala usando T-SQL sul pool SQL.
* **Scrittura dei dati:** Eseguire il commit delle modifiche da qualsiasi modello al pool SQL.

Per altre informazioni, vedere [Integrazione con Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Analisi di flusso di Azure

L’Analisi dei flussi di Azure è un'infrastruttura complessa, completamente gestita per l'elaborazione e l'utilizzo di dati degli eventi generati da Hub eventi di Azure.  L'integrazione con il pool SQL consente di eseguire lo streaming dei dati in modo da essere elaborati e archiviati insieme ai dati relazionali, consentendo un'analisi più approfondita e avanzata.  

* **Output del processo:** Inviare l'output da processi di analisi di flusso direttamente al pool SQL.

Per altre informazioni, vedere [Integrazione con Analisi di flusso di Azure](sql-data-warehouse-integrate-azure-stream-analytics.md).
