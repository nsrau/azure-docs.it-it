---
title: Caricare dati in ambienti di archiviazione di Azure - Processo di data science per i team (TDSP)
description: Spostamento dei dati da e verso l'archiviazione BLOB di Azure
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/09/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 56c45bf6db79ded64574a44399712951e82c1c3e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60303588"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Caricare i dati in ambienti di archiviazione per l'analisi

Il Processo di analisi scientifica dei dati per i team richiede l'inserimento o il caricamento di dati in una vasta gamma di ambienti di archiviazione diversi per l'elaborazione e l'analisi nei modi più appropriati in ogni fase del processo. Le destinazioni dei dati più comunemente usate per l'elaborazione includono l'archiviazione BLOB di Azure, i database di SQL Azure, SQL Server nella macchina virtuale di Azure, HDInsight (Hadoop) e Azure Machine Learning. 

Gli articoli seguenti descrivono come inserire dati in diversi ambienti di destinazione dove i dati vengono archiviati ed elaborati.

* Da o in [Archiviazione BLOB di Azure](move-azure-blob.md)
* In [SQL Server in macchine virtuali di Azure](move-sql-server-virtual-machine.md)
* In [Database SQL di Azure](move-sql-azure.md)
* In [tabelle Hive](move-hive-tables.md)
* In [tabelle partizionate SQL](parallel-load-sql-partitioned-tables.md)
* Da [SQL Server locale](move-sql-azure-adf.md)

Le esigenze aziendali e tecniche specifiche, nonché la posizione iniziale, il formato e le dimensioni dei dati, determinano gli ambienti di destinazione in cui i dati devono essere inseriti per raggiungere gli obiettivi dell'analisi. Non è insolito per uno scenario richiedere lo spostamento dei dati tra ambienti diversi per disporre di tutte le attività necessarie per creare un modello predittivo. Questa sequenza di attività può includere, ad esempio, l'esplorazione dei dati, la pre-elaborazione, la pulizia, il sottocampionamento e il training del modello.
