---
title: Campionare i dati in contenitori BLOB di Azure, SQL Server e nelle tabelle Hive | Documentazione Microsoft
description: Come esplorare i dati archiviati in vari ambienti di Azure.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: (previous author=deguhath, ms.author=deguhath)
ms.openlocfilehash: b7b233532a526879e886950c3a0d8cfe169251a7
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2018
ms.locfileid: "52441503"
---
# <a name="heading"></a>Campionare i dati in contenitori BLOB di Azure, SQL Server e nelle tabelle Hive

Gli articoli seguenti illustrano come eseguire il campionamento di dati archiviati in una delle tre diverse posizioni di Azure:

* I [**dati del contenitore BLOB di Azure**](sample-data-blob.md) vengono campionati dopo il download a livello di codice tramite codice Python di campionamento.
* I [**dati di SQL Server**](sample-data-sql-server.md) vengono campionati usando sia il linguaggio di programmazione Python che SQL. 
* I [**dati delle tabelle Hive**](sample-data-hive.md) vengono campionati tramite query Hive.

Questo campionamento è un passaggio del [Processo di analisi scientifica dei dati per i team (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

**Perché campionare i dati?**

Se il set di dati da analizzare è grande, è in genere opportuno sottocampionare i dati per ridurlo e ottenere dimensioni inferiori più facilmente gestibili ma comunque rappresentative. Questa operazione facilita la comprensione e l'esplorazione dei dati, nonché la progettazione di funzionalità. Il suo ruolo nel Cortana Analytics Process consiste nell'abilitare la creazione relativa a prototipi di funzioni di elaborazione dei dati e di modelli per l'apprendimento automatico.

