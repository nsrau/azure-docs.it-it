---
title: Creare soluzioni integrate
description: Strumenti e partner della soluzione che si integrano con un pool SQL dedicato (in precedenza SQL DW) in Azure sinapsi Analytics.
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
ms.openlocfilehash: 2afc274bf7c040eca6a83abbab24c41767f16482
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96453671"
---
# <a name="integrate-other-services-with-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Integrare altri servizi con un pool SQL dedicato (in precedenza SQL DW) in Azure sinapsi Analytics.

La funzionalità pool SQL dedicato (in precedenza SQL DW) in Azure sinapsi Analytics consente agli utenti di integrarsi con molti degli altri servizi di Azure. Un pool SQL dedicato può usare diversi servizi aggiuntivi, tra cui:

* Power BI
* Data factory di Azure
* Azure Machine Learning
* Analisi di flusso di Azure

Per altre informazioni sui servizi di integrazione in Azure, vedere l'articolo [Partner di integrazione](sql-data-warehouse-partner-data-integration.md).

## <a name="power-bi"></a>Power BI

L'integrazione di Power BI consente di combinare la potenza di calcolo di un data warehouse con le funzionalità di creazione di report dinamici e visualizzazione di Power BI. L’integrazione di Power BI include attualmente:

* Connessione **diretta**: una connessione più avanzata con distribuzione logici a un data warehouse provisioning con un pool SQL dedicato (in precedenza SQL DW). Questa funzionalità fornisce un'analisi più rapida su vasta scala.
* **Apri in Power BI**: Il pulsante "Apri in Power BI" passa informazioni sull'istanza a Power BI per una modalità di connessione semplificata.

Per altre informazioni, vedere [Integrazione con Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md) o fare riferimento alla [documentazione di Power BI](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/).

## <a name="azure-data-factory"></a>Data factory di Azure

Azure Data Factory offre agli utenti una piattaforma gestita per creare pipeline di estrazione e caricamento complesse. L'integrazione di pool SQL dedicato (in precedenza SQL DW) con Azure Data Factory include:

* **Procedure di archiviazione**: per orchestrare l'esecuzione di stored procedure.
* **Copy**: usare ADF per spostare i dati in un pool SQL dedicato (in precedenza SQL DW). Questa operazione può usare un meccanismo di spostamento dati ADF standard oppure PolyBase in background.

Per altre informazioni, vedere [Integrazione con Azure Data Factory](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning è un servizio di analisi completamente gestito, che consente di creare modelli complessi usando un ampio set di strumenti di previsione. Il pool SQL dedicato (noto in precedenza come SQL DW) è supportato sia come origine che come destinazione per questi modelli e presenta le funzionalità seguenti:

* **Leggere i dati:** Unità di modelli su larga scala con T-SQL su un pool SQL dedicato (in precedenza SQL DW).
* **Scrittura dei dati:** Eseguire il commit delle modifiche da un modello a un pool SQL dedicato (in precedenza SQL DW).

Per altre informazioni, vedere [Integrazione con Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Analisi di flusso di Azure

L’Analisi dei flussi di Azure è un'infrastruttura complessa, completamente gestita per l'elaborazione e l'utilizzo di dati degli eventi generati da Hub eventi di Azure.  L'integrazione con il pool SQL dedicato (in precedenza SQL DW) consente di eseguire lo streaming dei dati in modo da essere elaborati e archiviati insieme ai dati relazionali, consentendo un'analisi più approfondita e più avanzata.  

* **Output del processo:** Inviare l'output da processi di analisi di flusso direttamente a un pool SQL dedicato (in precedenza SQL DW).

Per altre informazioni, vedere [Integrazione con Analisi di flusso di Azure](sql-data-warehouse-integrate-azure-stream-analytics.md).
