---
title: 'Esercitazione: Introduzione ad Azure Synapse Analytics'
description: Questa esercitazione illustra i passaggi di base per la configurazione e l'uso di Azure Synapse Analytics.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: 7affc5aad89fd79e6ba6480f7bf10d37f90dc5e3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075857"
---
# <a name="get-started-with-azure-synapse-analytics"></a>Introduzione ad Azure Synapse Analytics

Questa esercitazione è una guida dettagliata sulle principali aree di funzionalità di Azure Synapse Analytics. L'esercitazione è il punto di partenza ideale per chi desidera una presentazione guidata degli scenari principali di Azure Synapse Analytics. Dopo aver seguito i passaggi dell'esercitazione, si avrà un'area di lavoro Synapse completamente funzionale in cui è possibile iniziare ad analizzare i dati con SQL, SQL su richiesta e Apache Spark.

Si apprenderà come:
* Effettuare il provisioning di un'area di lavoro Synapse in una sottoscrizione di Azure
* Configurare il controllo di accesso per un account ADLSGEN2 in modo che funzioni correttamente con l'area di lavoro Synapse
* Caricare i dati di esempio NYCTaxi nell'area di lavoro Synapse in modo che possano essere usati da SQL, SQL su richiesta e Spark
* Modificare ed eseguire script SQL e notebook di Synapse usando Synapse Studio
* Eseguire query su tabelle SQL e tabelle Spark
* Caricare dati di tabelle SQL in dataframe Spark
* Caricare i dati in tabelle SQL da dataframe Spark
* Esplorare il contenuto di un account ADLSGEN2
* Analizzare i file di dati Parquet in account ADLSGEN2 usando Spark e SQL su richiesta 
* Compilare una pipeline di dati che esegua automaticamente un notebook di Synapse ogni ora

Seguire i passaggi *in ordine*, come indicato di seguito, per avere una panoramica di molte delle funzionalità e apprendere l'utilizzo delle funzionalità principali.

* [PASSAGGIO 1 - Creare e configurare un'area di lavoro Synapse](get-started-create-workspace.md)
* [PASSAGGIO 2 - Usare un pool SQL per l'analisi](get-started-analyze-sql-pool.md)
* [PASSAGGIO 3 - Usare Spark per l'analisi](get-started-analyze-spark.md)
* [PASSAGGIO 4 - Usare SQL su richiesta per l'analisi](get-started-analyze-sql-on-demand.md)
* [PASSAGGIO 5 - Analizzare i dati in un account di archiviazione](get-started-analyze-storage.md)
* [PASSAGGIO 6 - Orchestrare con le pipeline](get-started-pipelines.md)
* [PASSAGGIO 7 - Visualizzare i dati con Power BI](get-started-visualize-power-bi.md)
