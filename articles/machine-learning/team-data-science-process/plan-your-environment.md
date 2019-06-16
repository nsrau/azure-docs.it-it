---
title: Identificare scenari e pianificare il processo di analisi - Processo di data science per i team | Azure Machine Learning
description: Identificare scenari e pianificare l'elaborazione dati dell'analisi avanzata prendendo in considerazione una serie di domande chiave.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d8eed4f2425cdbfec7d3addad11ddaba57e5370e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64704492"
---
# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>Come identificare scenari e pianificare l'elaborazione dei dati di analisi avanzata

Quali risorse sono necessarie per creare un ambiente in grado di eseguire l'elaborazione di analisi avanzata su un set di dati? Questo articolo suggerisce una serie di domande che contribuiscono a identificare le attività e le risorse attinenti allo scenario.

Per informazioni sull'ordine dei passaggi generali per l'analisi predittiva, vedere [Informazioni sul processo di data science per i team](overview.md). Ognuno di questi passaggi richiede risorse specifiche per le attività attinenti allo scenario specifico.

Rispondere a domande chiave nelle aree seguenti per identificare lo scenario:

* Logistica dei dati
* Caratteristiche dei dati
* Qualità del set di dati
* Strumenti e linguaggi preferiti

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="logistic-questions-data-locations-and-movement"></a>Logistica: percorsi dei dati e spostamento

Le domande di logistica riguardano gli elementi seguenti:

* Posizione dell'origine dati
* Destinazione in Azure
* Requisiti per lo spostamento di dati, inclusi pianificazione, quantità e risorse interessate

Potrebbe essere necessario spostare i dati diverse volte durante il processo di analisi. Uno scenario frequente consiste nello spostamento di dati locali in una qualche forma di archiviazione in Azure e quindi in Machine Learning Studio.

### <a name="what-is-your-data-source"></a>Che caratteristiche ha l'origine dati?

I dati sono locali o nel cloud? Le posizioni possibili includono:

* Un indirizzo HTTP disponibile pubblicamente
* Un percorso di file di rete o locale
* Un database di SQL Server
* Un contenitore di archiviazione di Azure

### <a name="what-is-the-azure-destination"></a>Qual è la destinazione di Azure?

Dove devono trovarsi i dati per l'elaborazione o la modellazione? 

* Archivio BLOB di Azure
* Database SQL Azure
* Macchine virtuali SQL Server in Azure
* Tabelle Hive o HDInsight (Hadoop in Azure)
* Azure Machine Learning
* Dischi rigidi virtuali di Azure montabili

### <a name="how-are-you-going-to-move-the-data"></a>Come verranno spostati i dati?

Per le procedure e le risorse disponibili per inserire o caricare i dati in vari ambienti di elaborazione e di archiviazione, vedere:

* [Caricare i dati in ambienti di archiviazione per l'analisi](ingest-data.md)
* [Importare dati di training in Azure Machine Learning Studio da varie origini dati](../studio/import-data.md)

### <a name="does-the-data-need-to-be-moved-on-a-regular-schedule-or-modified-during-migration"></a>I dati devono essere spostati a intervalli regolari o modificati durante la migrazione?

È consigliabile usare Azure Data Factory (ADF) quando è necessaria la migrazione continua dei dati. Azure Data Factory può essere utile per:

* Uno scenario ibrido che include sia risorse locali che cloud
* Uno scenario in cui i dati vengono sottoposti a transazioni, modificati o cambiati dalla logica di business durante la migrazione

Per altre informazioni, vedere [Spostare i dati da SQL Server locale a SQL Azure con Azure Data Factory](move-sql-azure-adf.md).

### <a name="how-much-of-the-data-is-to-be-moved-to-azure"></a>Che quantità di dati è necessario spostare in Azure?

I set di dati estremamente grandi potrebbero superare la capacità di archiviazione di determinati ambienti. Per un esempio, vedere la discussione sui limiti di dimensione per Machine Learning Studio nella sezione successiva. In questi casi è possibile usare un campione dei dati durante l'analisi. Per informazioni dettagliate su come sottocampionare un set di dati in vari ambienti di Azure, vedere [Sample data in the Team Data Science Process](sample-data.md)(Campionare i dati nel Processo di analisi scientifica dei dati per i team).

## <a name="data-characteristics-questions-type-format-and-size"></a>Domande sulle caratteristiche dei dati: tipo, formato e dimensione

Queste domande sono fondamentali per la pianificazione degli ambienti di archiviazione ed elaborazione. Saranno utili per scegliere lo scenario appropriato per il tipo di dati e comprendere le eventuali restrizioni.

### <a name="what-are-the-data-types"></a>Quali sono i tipi di dati?

* Numerico
* Categorical
* Stringhe
* Binary

### <a name="how-is-your-data-formatted"></a>Come sono formattati i dati?

* File flat delimitati da virgole (CSV) o delimitati da tabulazioni (TSV)
* Compressi o non compressi
* BLOB di Azure
* Tabelle Hive di Hadoop
* Tabelle di SQL Server

### <a name="how-large-is-your-data"></a>Quali sono le dimensioni dei dati?

* Piccole: meno di 2 GB
* Medie: comprese tra 2 GB e 10 GB
* Grandi: più di 10 GB

Si consideri ad esempio l'ambiente di Azure Machine Learning Studio:

* Per un elenco dei formati e dei tipi di dati supportati da Azure Machine Learning Studio, vedere la sezione [Tipi di dati e formati di dati supportati](../studio/import-data.md#supported-data-formats-and-data-types) .
* Per informazioni sulle limitazioni di altri servizi Azure usati nel processo di analisi, vedere [Sottoscrizione ad Azure e limiti, quote e vincoli del servizio](../../azure-subscription-service-limits.md).

## <a name="data-quality-questions-exploration-and-pre-processing"></a>Domande sulla qualità dei dati: esplorazione e pre-elaborazione

### <a name="what-do-you-know-about-your-data"></a>Quali sono le informazioni disponibili sui dati?

Comprendere le caratteristiche di base dei dati:

* Modelli o tendenze presenti
* Outlier presenti
* Quantità di valori mancanti

Questo passaggio è importante per:

* Determinare la quantità di pre-elaborazione necessaria
* Formulare ipotesi che suggeriscono le funzionalità o il tipo di analisi più appropriati
* Formulare piani per la raccolta di dati aggiuntivi

Alcune tecniche utili per l'analisi dei dati includono il calcolo di statistiche descrittive e i tracciati di visualizzazione. Per informazioni dettagliate su come esplorare un set di dati in vari ambienti di Azure, vedere [Esplorare i dati nel Processo di analisi scientifica dei dati per i team](explore-data.md).

### <a name="does-the-data-require-preprocessing-or-cleaning"></a>I dati necessitano di pre-elaborazione o pulizia?

Potrebbe essere necessario pre-elaborare e pulire i dati prima di poter usare il set di dati in modo efficace per l'apprendimento automatico. I dati non elaborati sono spesso poco significativi e inaffidabili. Potrebbero esserci valori mancanti. Utilizzare tali dati per la modellazione può produrre risultati fuorvianti. Per una descrizione, vedere [Attività per preparare i dati per operazioni avanzate con Machine Learning](prepare-data.md).

## <a name="tools-and-languages-questions"></a>Domande su strumenti e linguaggi

Sono disponibili numerose opzioni relative a linguaggi, ambienti di sviluppo e strumenti. Tenere in considerazione le proprie esigenze e preferenze.

### <a name="what-languages-do-you-prefer-to-use-for-analysis"></a>Quali linguaggi si preferisce usare per l'analisi?

* R
* Python
* SQL

### <a name="what-tools-should-you-use-for-data-analysis"></a>Quali strumenti è necessario usare per l'analisi dei dati?

* [Microsoft Azure PowerShell](/powershell/azure/overview): un linguaggio di script usato per amministrare le risorse di Azure in un linguaggio di script
* [Azure Machine Learning Studio](../studio/what-is-ml-studio.md)
* [Revolution Analytics](https://www.microsoft.com/sql-server/machinelearningserver)
* [RStudio](https://www.rstudio.com)
* [Python Tools per Visual Studio](https://aka.ms/ptvsdocs)
* [Anaconda](https://www.continuum.io/why-anaconda)
* [Notebook Jupyter](https://jupyter.org/)
* [Microsoft Power BI](https://powerbi.microsoft.com)

## <a name="identify-your-advanced-analytics-scenario"></a>Identificare lo scenario di analisi avanzata

Dopo aver risposto alle domande nella sezione precedente, si è pronti per determinare quale scenario è più adatto al caso specifico. Gli scenari di esempio sono descritti in [Scenari per l'analisi avanzata in Azure Machine Learning](plan-sample-scenarios.md).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sul processo di data science per i team](overview.md)
