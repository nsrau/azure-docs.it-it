---
title: Dati di esempio in diverse posizioni di archiviazione di Azure - Processo di analisi scientifica dei dati per i team
description: Dati di esempio in contenitori BLOB di Azure, SQL Server e tabelle Hive per ridurli a una dimensione più piccola ma rappresentativa e gestibile.
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
ms.openlocfilehash: fc91aec80f74488125649cfe807757ba5ae49c9b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60253105"
---
# <a name="heading"></a>Campionare i dati in contenitori BLOB di Azure, SQL Server e nelle tabelle Hive

Gli articoli seguenti illustrano come eseguire il campionamento di dati archiviati in una delle tre diverse posizioni di Azure:

* I [**dati del contenitore BLOB di Azure**](sample-data-blob.md) vengono campionati dopo il download a livello di codice tramite codice Python di campionamento.
* I [**dati di SQL Server**](sample-data-sql-server.md) vengono campionati usando sia il linguaggio di programmazione Python che SQL. 
* I [**dati delle tabelle Hive**](sample-data-hive.md) vengono campionati tramite query Hive.

Questo campionamento è un passaggio del [Processo di analisi scientifica dei dati per i team (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

**Perché campionare i dati?**

Se il set di dati da analizzare è grande, è in genere opportuno sottocampionare i dati per ridurlo e ottenere dimensioni inferiori più facilmente gestibili ma comunque rappresentative. Questa operazione facilita la comprensione e l'esplorazione dei dati, nonché la progettazione di funzionalità. Il suo ruolo nel Cortana Analytics Process consiste nell'abilitare la creazione relativa a prototipi di funzioni di elaborazione dei dati e di modelli per l'apprendimento automatico.

