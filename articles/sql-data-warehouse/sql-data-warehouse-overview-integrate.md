---
title: Creare soluzioni integrate con SQL Data Warehouse | Documentazione Microsoft
description: 'Strumenti e partner con soluzioni che interagiscono con SQL Data Warehouse. '
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: e2dc8f3f-10e3-4589-a4e2-50c67dfcf67f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 10/31/2016
ms.author: martinle;barbkess
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: d1cd23eac464d48ebc6dd618c52c252444b47e21
ms.contentlocale: it-it
ms.lasthandoff: 04/03/2017


---
# <a name="leverage-other-services-with-sql-data-warehouse"></a>Utilizzare altri servizi con SQL Data Warehouse
Oltre alle funzionalità di base, SQL Data Warehouse consente agli utenti di utilizzare molti altri servizi in Azure insieme a esso.  In particolare, sono state attualmente intraprese azioni per integrare completamente le informazioni seguenti:

* Power BI
* Data factory di Azure
* Azure Machine Learning
* Azure Stream Analytics

Stiamo lavorando per connettersi con altri servizi attraverso l'ecosistema di Azure.

## <a name="power-bi"></a>Power BI
Integrazione di Power BI consente di sfruttare la potenza di calcolo di Data Warehouse di SQL con la creazione di report dinamici e visualizzazione di Power BI. L’integrazione di Power BI include attualmente:

* **Connessione diretta**: una connessione più avanzata con caratteristiche logiche SQL Data Warehouse.  Questo fornisce un'analisi veloce su vasta scala.
* **Aprire in Power BI**: il tasto 'Apri in Power BI' passa informazioni istanza a Power BI, consentendo una connessione più trasparente.

Vedere [Integrazione con Power BI](sql-data-warehouse-integrate-power-bi.md) o fare riferimento alla [documentazione di Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/06/24/exploring-azure-sql-data-warehouse-with-power-bi.aspx) per altre informazioni.

## <a name="azure-data-factory"></a>Data factory di Azure
Azure Data Factory offre agli utenti una piattaforma gestita per creare pipeline estrazione-caricamento complesse.  L’integrazione di SQL Data Warehouse con Azure Data Factory include quanto segue:

* **Procedure di archiviazione**: orchestrare l'esecuzione delle procedure di archiviazione in SQL Data Warehouse.
* **Copia**: usare ADF per spostare dati in SQL Data Warehouse.  Questa operazione può usare un meccanismo di spostamento dati ADF standard oppure PolyBase in background. 

Vedere [Integrazione con Azure Data Factory](sql-data-warehouse-integrate-azure-data-factory.md) o [Documentazione di Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) per altre informazioni.

## <a name="azure-machine-learning"></a>Azure Machine Learning
Azure Machine Learning è un servizio di analisi completamente gestito che consente agli utenti di creare modelli complessi sfruttando un ampio set di strumenti di previsione.  SQL Data Warehouse è supportato come origine e destinazione per questi modelli con le funzionalità seguenti:

* **Lettura dati:** Guida i modelli su larga scala mediante T-SQL su SQL Data Warehouse.
* **Scrittura dati:** registra le modifiche da qualsiasi modello a SQL Data Warehouse.

Vedere [Integrazione con Azure Machine Learning](sql-data-warehouse-integrate-azure-machine-learning.md) o fare riferimento alla [documentazione di Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) per altre informazioni.

## <a name="azure-stream-analytics"></a>Analisi di flusso di Azure
L’Analisi dei flussi di Azure è un'infrastruttura complessa, completamente gestita per l'elaborazione e l'utilizzo di dati degli eventi generati da Hub eventi di Azure.  L’integrazione con SQL Data Warehouse consente la trasmissione di dati in modo efficace da elaborare e archiviare insieme ai dati relazionali permettendo un’analisi più approfondita, più avanzata.  

* **Output del processo:** inviare l'output da processi di analisi di flusso direttamente a SQL Data Warehouse.

Vedere [Integrazione con Analisi di flusso di Azure](sql-data-warehouse-integrate-azure-stream-analytics.md) o [Documentazione dell'analisi di flusso di Azure](https://azure.microsoft.com/documentation/services/stream-analytics/) per altre informazioni.

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop/

[Azure Data Factory]: sql-data-warehouse-integrate-azure-data-factory.md
[Azure Machine Learning]: sql-data-warehouse-integrate-azure-machine-learning.md
[Azure Stream Analytics]: sql-data-warehouse-integrate-azure-stream-analytics.md
[Power BI]: sql-data-warehouse-integrate-power-bi.md
[Partners]: sql-data-warehouse-partner-business-intelligence.md

<!--MSDN references-->

<!--Other Web references-->

