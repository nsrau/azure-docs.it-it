---
title: Caricare i dati negli ambienti di Archiviazione di Azure - Team Data Science Process
description: Informazioni su come inserire dati in vari ambienti di destinazione in cui i dati vengono archiviati ed elaborati.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: f8eab59d810fb825dbebf80d01d8efd2dd0a9841
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720538"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Caricare i dati in ambienti di archiviazione per l'analisi

Il processo di analisi scientifica dei dati del team richiede che i dati vengano inseriti o caricati nel modo più appropriato in ogni fase. Le destinazioni dati possono includere Archiviazione BLOB di Azure, database SQL Azure, SQL Server in MACCHINE virtuali di Azure, HDInsight (Hadoop), Analisi Synapse e Azure Machine Learning.Data destinations can include Azure Blob Storage, SQL Azure databases, SQL Server on Azure VM, HDInsight (Hadoop), Synapse Analytics, and Azure Machine Learning. 

Gli articoli seguenti descrivono come inserire dati in diversi ambienti di destinazione dove i dati vengono archiviati ed elaborati.

* Da o in [Archiviazione BLOB di Azure](move-azure-blob.md)
* In [SQL Server in macchine virtuali di Azure](move-sql-server-virtual-machine.md)
* Nel database SQL di [AzureTo Azure SQL Database](move-sql-azure.md)
* In [tabelle Hive](move-hive-tables.md)
* In [tabelle partizionate SQL](parallel-load-sql-partitioned-tables.md)
* Da [SQL Server locale](move-sql-azure-adf.md)

Le esigenze tecniche e aziendali, nonché la posizione iniziale, il formato e le dimensioni dei dati determineranno il piano di inserimento dei dati migliore. Non è raro che un piano migliore abbia diversi passaggi. Questa sequenza di attività può includere, ad esempio, l'esplorazione dei dati, la pre-elaborazione, la pulizia, il sottocampionamento e il training del modello.  Azure Data Factory è una risorsa di Azure consigliata per orchestrare lo spostamento e la trasformazione dei dati.
