---
title: Combinazioni supportate di esecuzione e ambienti di dati per la preparazione dati di Azure Machine Learning | Microsoft Docs
description: Questo documento elenca tutte le combinazioni supportate di diversi runtime e origini dati disponibili per la preparazione dati di Azure ML
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/15/2017
ms.openlocfilehash: 02e0ca96bff7781c242b4c5e965b229065e71725
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="supported-matrix-for-this-release"></a>Matrice supportata per questa versione 
Quando il codice carica i dati usando le origini dati o la preparazione dati, recuperando i dataframe di Pandas o Spark, sono supportate le combinazioni seguenti di ambienti di calcolo per la sperimentazione e le posizioni dei dati:

|     |File locali  |BLOB di Azure  |Database SQL Server***  |
|---------|---------|---------|---------|---------|
|Python in locale    |     Supportato    |Non supportato         | Non supportato        |         |
|Python di Docker (macchina virtuale Linux)     |Supportato solo nei file di progetto*         | Non supportato        |        Non supportato |         |
|PySpark di Docker (macchina virtuale Linux)     |Supportato solo nei file di progetto*     |Supportato         | Supportato**        |         |
|Python della macchina virtuale per data science di Azure     |Supportato solo nei file di progetto*         |Non supportato         |Non supportato         |         |
|PySPark della macchina virtuale per data science di Azure     | Supportato solo nei file di progetto*        |Non supportato         |Non supportato         |         |
|PySPark HDInsight di Azure     | Non supportato        |Supportato         |Supportato**         |         |
|Python HDInsight di Azure     | Non supportato        | Non supportato        | Non supportato        |         |

Azure Data Lake Store non è attualmente supportato per le destinazioni di calcolo.

*Quando vengono usati percorsi di file locali, i file all'interno del progetto vengono copiati nell'ambiente di calcolo e quindi vengono letti qui. I file esterni al progetto non vengono copiati e i percorsi non saranno più risolvibili nell'ambiente di calcolo. È consigliabile usare la sostituzione dell'origine dati in modo che il codice possa usare un file locale quando viene eseguito in locale e quindi passare a un BLOB di Archiviazione di Azure per una configurazione di esecuzione diversa. È anche possibile usare il supporto di campionamento sulle origini dati per gestire le esecuzioni solo per dati di grandi dimensioni in determinate configurazioni di esecuzione.

**Usa il driver SQL Server JDBC di Maven 6.2.1. È necessario assicurarsi che questo pacchetto, o un pacchetto compatibile, sia incluso nel file spark_dependencies.yml per l'ambiente di calcolo.

***Supporta Database SQL di Azure, Azure SQL Data Warehouse o Microsoft SQL Server se il database può essere raggiunto dall'ambiente di calcolo. 
