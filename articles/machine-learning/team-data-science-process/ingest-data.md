---
title: Caricare i dati in ambienti di archiviazione di Azure-processo di Data Science per i team
description: Informazioni su come inserire i dati in diversi ambienti di destinazione in cui vengono archiviati ed elaborati i dati.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "76720538"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Caricare i dati in ambienti di archiviazione per l'analisi

Per il processo di analisi scientifica dei dati per i team è necessario inserire o caricare i dati nel modo più appropriato in ogni fase. Le destinazioni dei dati possono includere archiviazione BLOB di Azure, database SQL Azure, SQL Server in VM di Azure, HDInsight (Hadoop), sinapsi Analytics e Azure Machine Learning. 

Gli articoli seguenti descrivono come inserire dati in diversi ambienti di destinazione dove i dati vengono archiviati ed elaborati.

* Da o in [Archiviazione BLOB di Azure](move-azure-blob.md)
* In [SQL Server in macchine virtuali di Azure](move-sql-server-virtual-machine.md)
* Nel [database SQL di Azure](move-sql-azure.md)
* In [tabelle Hive](move-hive-tables.md)
* In [tabelle partizionate SQL](parallel-load-sql-partitioned-tables.md)
* Da [SQL Server locale](move-sql-azure-adf.md)

Le esigenze tecniche e aziendali, nonché la posizione iniziale, il formato e le dimensioni dei dati determineranno il migliore piano di inserimento dei dati. Non è insolito che un piano migliore abbia diversi passaggi. Questa sequenza di attività può includere, ad esempio, l'esplorazione dei dati, la pre-elaborazione, la pulizia, il sottocampionamento e il training del modello.  Azure Data Factory è una risorsa di Azure consigliata per orchestrare lo spostamento e la trasformazione dei dati.
