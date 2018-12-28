---
title: Dati di esempio in diverse posizioni di archiviazione di Azure - Processo di analisi scientifica dei dati per i team
description: Dati di esempio in contenitori BLOB di Azure, SQL Server e tabelle Hive per ridurli a una dimensione più piccola ma rappresentativa e gestibile.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 7bc4174121a58353219e73eef86ec6c64f806647
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53133214"
---
# <a name="heading"></a>Campionare i dati in contenitori BLOB di Azure, SQL Server e nelle tabelle Hive

Gli articoli seguenti illustrano come eseguire il campionamento di dati archiviati in una delle tre diverse posizioni di Azure:

* I [**dati del contenitore BLOB di Azure**](sample-data-blob.md) vengono campionati dopo il download a livello di codice tramite codice Python di campionamento.
* I [**dati di SQL Server**](sample-data-sql-server.md) vengono campionati usando sia il linguaggio di programmazione Python che SQL. 
* I [**dati delle tabelle Hive**](sample-data-hive.md) vengono campionati tramite query Hive.

Questo campionamento è un passaggio del [Processo di analisi scientifica dei dati per i team (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

**Perché campionare i dati?**

Se il set di dati da analizzare è grande, è in genere opportuno sottocampionare i dati per ridurlo e ottenere dimensioni inferiori più facilmente gestibili ma comunque rappresentative. Questa operazione facilita la comprensione e l'esplorazione dei dati, nonché la progettazione di funzionalità. Il suo ruolo nel Cortana Analytics Process consiste nell'abilitare la creazione relativa a prototipi di funzioni di elaborazione dei dati e di modelli per l'apprendimento automatico.

