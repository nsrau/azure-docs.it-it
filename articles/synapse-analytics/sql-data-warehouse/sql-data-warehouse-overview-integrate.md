---
title: Creare soluzioni integrate
description: Strumenti e partner di soluzioni che si integrano con un pool Synapse SQL.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 16cfdfb475ce21ed4b51dc9140e59df701363f27
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85201005"
---
# <a name="integrate-other-services-with-a-synapse-sql-pool-data-warehouse"></a>Integrare altri servizi con un pool Synapse SQL (data warehouse)

La funzionalità pool SQL in Azure Synapse Analytics consente agli utenti di eseguire l'integrazione con molti degli altri servizi disponibili in Azure. Con Synapse SQL è possibile creare un data warehouse attraverso la risorsa pool SQL corrispondente, che può quindi utilizzare diversi servizi aggiuntivi, alcuni dei quali sono:

* Power BI
* Data factory di Azure
* Azure Machine Learning
* Analisi di flusso di Azure

Per altre informazioni sui servizi di integrazione in Azure, vedere l'articolo [Partner di integrazione](sql-data-warehouse-partner-data-integration.md).

## <a name="power-bi"></a>Power BI

L'integrazione di Power BI consente di combinare la potenza di calcolo di un data warehouse con le funzionalità di creazione di report dinamici e visualizzazione di Power BI. L’integrazione di Power BI include attualmente:

* **Connessione diretta**: Una connessione più avanzata con distribuzione logica in una data warehouse in cui è stato eseguito il provisioning con il pool SQL. Questa funzionalità fornisce un'analisi più rapida su vasta scala.
* **Apri in Power BI**: Il pulsante "Apri in Power BI" passa informazioni sull'istanza a Power BI per una modalità di connessione semplificata.

Per altre informazioni, vedere [Integrazione con Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md) o fare riferimento alla [documentazione di Power BI](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/).

## <a name="azure-data-factory"></a>Data factory di Azure

Azure Data Factory offre agli utenti una piattaforma gestita per creare pipeline di estrazione e caricamento complesse. L'integrazione di pool SQL con Azure Data Factory include:

* **Procedure di archiviazione**: per orchestrare l'esecuzione di stored procedure.
* **Copia**: usare file di definizione dell'applicazione (ADF) per spostare dati in un pool SQL. Questa operazione può usare un meccanismo di spostamento dati ADF standard oppure PolyBase in background.

Per altre informazioni, vedere [Integrazione con Azure Data Factory](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning è un servizio di analisi completamente gestito, che consente di creare modelli complessi usando un ampio set di strumenti di previsione. Un pool SQL è supportato sia come origine che come destinazione per questi modelli con le funzionalità seguenti:

* **Lettura dei dati**: guida i modelli su larga scala tramite T-SQL su pool SQL.
* **Scrittura dei dati**: esegue il commit delle modifiche da qualsiasi modello di nuovo nel pool SQL.

Per altre informazioni, vedere [Integrazione con Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Analisi di flusso di Azure

L’Analisi dei flussi di Azure è un'infrastruttura complessa, completamente gestita per l'elaborazione e l'utilizzo di dati degli eventi generati da Hub eventi di Azure.  L'integrazione con il pool SQL consente la trasmissione dei dati in modo da garantirne l'efficienza di elaborazione e archiviazione insieme ai dati relazionali, per un'analisi più approfondita e avanzata.  

* **Output del processo:** inviare l'output da processi di Analisi di flusso direttamente al pool SQL.

Per altre informazioni, vedere [Integrazione con Analisi di flusso di Azure](sql-data-warehouse-integrate-azure-stream-analytics.md).
