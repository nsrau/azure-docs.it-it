---
title: Panoramica dei notebook di Azure sinapsi Analytics
description: Questo articolo fornisce una panoramica delle funzionalità disponibili tramite i notebook di Azure sinapsi Analytics.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 4387aadd70ddb373df1bdfa61cbe9ed7f2af283d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919717"
---
# <a name="azure-synapse-analytics-notebooks"></a>Notebook di Azure sinapsi Analytics
Un notebook di sinapsi Studio (anteprima) è un'interfaccia Web che consente di creare file che contengono codice in tempo reale, visualizzazioni e testo descrittivo. I notebook possono essere usati per convalidare idee ed eseguire esperimenti rapidi per ottenere informazioni cognitive dettagliate dai dati. 

Con un notebook di Azure Synapse Studio è possibile:

* Iniziare senza attività di configurazione.
* Mantenere i dati protetti con le funzionalità di sicurezza aziendali predefinite.
* Analizzare i dati in formati non elaborati (CSV, txt, JSON e così via), formati di file elaborati (parquet, Delta Lake, ORC e così via) e file di dati tabulari SQL in Spark e SQL.
* Ottenere produttività con funzionalità avanzate di creazione e visualizzazione dei dati predefinite.

Questa sezione contiene articoli sulla combinazione di linguaggi, sulla creazione di visualizzazioni di dati, parametrizzazione notebook, sulla compilazione di pipeline e altro ancora. Contiene anche riferimenti ed esercitazioni su come iniziare a usare lo sviluppo di notebook.

## <a name="create-manage-and-use-notebooks"></a>Creare, gestire e usare notebook
È possibile gestire i notebook usando l'interfaccia utente di sinapsi Studio. 

Per ulteriori informazioni su come è possibile creare e gestire notebook, vedere gli articoli seguenti:
  - Gestisci notebook
    - [Creare notebook](./spark/../apache-spark-development-using-notebooks.md#create-a-notebook)
    - [Sviluppare notebook](./spark/../apache-spark-development-using-notebooks.md#develop-notebooks)
    - [Importare i dati in un notebook](./spark/../apache-spark-development-using-notebooks.md#bring-data-to-a-notebook)
    - [Usare più linguaggi usando i comandi Magic e le tabelle temporanee](./spark/../apache-spark-development-using-notebooks.md#integrate-a-notebook)
    - [Usa comandi Magic Cell](./spark/../apache-spark-development-using-notebooks.md#magic-commands)
  - Sviluppo
    - [Configurare le impostazioni della sessione Spark](./spark/../apache-spark-development-using-notebooks.md#spark-session-config)
    - [Usare le utilità di Microsoft Spark](./spark/../microsoft-spark-utilities.md)
    - [Visualizzare i dati tramite notebook e librerie](./spark/../apache-spark-data-visualization.md)
    - [Integrare un notebook in pipeline](./spark/../apache-spark-development-using-notebooks.md#integrate-a-notebook)


## <a name="next-steps"></a>Passaggi successivi
I notebook sono anche ampiamente usati per la preparazione dei dati, la visualizzazione dei dati, l'apprendimento automatico e altri scenari di Big Data. Per altre informazioni su come usare i notebook per l'analisi dei dati e gli scenari di Big Data, vedere le esercitazioni seguenti:
  - [Creare un notebook](./spark/../../quickstart-apache-spark-notebook.md)
  - [Creare visualizzazioni usando i notebook di sinapsi Studio](./spark/../apache-spark-data-visualization-tutorial.md)
  - [Creazione di modelli di Machine Learning con Apache Spark MLlib](./spark/../apache-spark-machine-learning-mllib-notebook.md)
  - [Creazione di modelli di Machine Learning con Azure AutoML](./spark/../apache-spark-azure-machine-learning-tutorial.md)