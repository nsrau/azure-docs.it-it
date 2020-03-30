---
title: Crea soluzioni integrate
description: Strumenti di soluzione e partner che si integrano con un data warehouse di cui è stato eseguito il provisioning tramite SQL Analytics.
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
ms.openlocfilehash: 680e561c08c5113e3d7f26b00422f3696bb133e9
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350231"
---
# <a name="integrate-other-services-with-a-sql-analytics-data-warehouse"></a>Integrare altri servizi con un data warehouse di SQL AnalyticsIntegrate other services with a SQL Analytics data warehouse 
The SQL Analytics capability within Azure Synapse Analytics enables users to integrate with many of the other services in Azure. Utilizzando SQL Analytics, è possibile creare un data warehouse tramite la relativa risorsa del pool SQL, che può quindi utilizzare diversi servizi aggiuntivi, alcuni dei quali includono:Using SQL Analytics, you can create a data warehouse via its SQL Pool resource, which can then utilize several additional services, some of which include:

* Power BI
* Data factory di Azure
* Azure Machine Learning
* Analisi di flusso di Azure

Per altre informazioni sui servizi di integrazione in Azure, vedere l'articolo Partner di [integrazione.](sql-data-warehouse-partner-data-integration.md)

## <a name="power-bi"></a>Power BI
L'integrazione di Power BI consente di combinare la potenza di calcolo di un data warehouse con la creazione di report dinamici e la visualizzazione di Power BI. L’integrazione di Power BI include attualmente:

* **Direct Connect:** una connessione più avanzata con pushdown logico in un data warehouse di cui è stato eseguito il provisioning tramite pool SQL. Questa funzionalità fornisce un'analisi più rapida su vasta scala.
* **Apri in Power BI:** il pulsante 'Apri in Power BI' passa le informazioni sull'istanza a Power BI per un modo semplificato di connessione.

Per altre informazioni, vedere [Integrazione con Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md) o fare riferimento alla [documentazione di Power BI](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/).

## <a name="azure-data-factory"></a>Data factory di Azure
Azure Data Factory offre agli utenti una piattaforma gestita per creare pipeline di estrazione e caricamento complesse. L'integrazione del pool SQL con Azure Data Factory include:SQL pool integration with Azure Data Factory includes:

* **Stored procedure**: Orchestrare l'esecuzione di stored procedure.
* **Copia**: utilizzare ADF per spostare i dati nel pool SQL. Questa operazione può usare un meccanismo di spostamento dati ADF standard oppure PolyBase in background. 

Per altre informazioni, vedere [Integrazione con Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-sql-data-warehouse?toc=/azure/sql-data-warehouse/toc.json).

## <a name="azure-machine-learning"></a>Azure Machine Learning
Azure Machine Learning è un servizio di analisi completamente gestito, che consente di creare modelli complessi usando un ampio set di strumenti di previsione. Il pool SQL è supportato sia come origine che come destinazione per questi modelli e dispone delle funzionalità seguenti:SQL pool is supported as a source and destination for these models, and has the following functionality:

* **Lettura dei dati:** Guidare i modelli su larga scala utilizzando T-SQL rispetto al pool SQL.
* **Scrivi dati:** Eseguire il commit delle modifiche da qualsiasi modello al pool SQL.

Per altre informazioni, vedere [Integrazione con Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Analisi di flusso di Azure
L’Analisi dei flussi di Azure è un'infrastruttura complessa, completamente gestita per l'elaborazione e l'utilizzo di dati degli eventi generati da Hub eventi di Azure.  L'integrazione con il pool SQL consente di elaborare e archiviare in modo efficace i dati di streaming insieme ai dati relazionali, consentendo un'analisi più approfondita e avanzata.  

* **Output lavoro:** Invia l'output dai processi di Analisi di flusso direttamente al pool SQL.

Per altre informazioni, vedere [Integrazione con Analisi di flusso di Azure](sql-data-warehouse-integrate-azure-stream-analytics.md).


