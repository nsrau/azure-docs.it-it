---
title: Caricare i dati in ambienti di archiviazione di Azure per l'analisi | Documentazione Microsoft
description: Spostamento dei dati da e verso l'archiviazione BLOB di Azure
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: deguhath
ms.openlocfilehash: d045bd37a4b3192672cc1bd37bc4bd14ea8d5402
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837185"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Caricare i dati in ambienti di archiviazione per l'analisi
Il Processo di analisi scientifica dei dati per i team richiede l'inserimento o il caricamento di dati in una vasta gamma di ambienti di archiviazione diversi per l'elaborazione e l'analisi nei modi più appropriati in ogni fase del processo. Le destinazioni dei dati più comunemente usate per l'elaborazione includono l'archiviazione BLOB di Azure, i database di SQL Azure, SQL Server nella macchina virtuale di Azure, HDInsight (Hadoop) e Azure Machine Learning. 

[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

Questo **menu** si collega ad argomenti che descrivono come inserire dati in questi ambienti di destinazione dove i dati vengono archiviati ed elaborati.

Le esigenze aziendali e tecniche specifiche, nonché la posizione iniziale, il formato e le dimensioni dei dati determinano gli ambienti di destinazione in cui i dati devono essere inseriti per raggiungere gli obiettivi dell'analisi. Non è insolito per uno scenario richiedere lo spostamento dei dati tra ambienti diversi per disporre di tutte le attività necessarie per creare un modello predittivo. Questa sequenza di attività può includere, ad esempio, l'esplorazione dei dati, la pre-elaborazione, la pulizia, il sottocampionamento e il training del modello.

