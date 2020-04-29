---
title: Campionare i dati in diverse posizioni di archiviazione di Azure-processo di Data Science per i team
description: Dati di esempio in contenitori BLOB di Azure, SQL Server e tabelle Hive per ridurli a una dimensione più piccola ma rappresentativa e gestibile.
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
ms.openlocfilehash: 22e91d50227fcb44c7b90478d76379c14161ae05
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76718604"
---
# <a name="sample-data-in-azure-blob-containers-sql-server-and-hive-tables"></a><a name="heading"></a>Campionare i dati in contenitori BLOB di Azure, in SQL Server e nelle tabelle Hive

Gli articoli seguenti illustrano come eseguire il campionamento di dati archiviati in una delle tre diverse posizioni di Azure:

* I [**dati del contenitore BLOB di Azure**](sample-data-blob.md) vengono campionati dopo il download a livello di codice tramite codice Python di campionamento.
* I [**dati di SQL Server**](sample-data-sql-server.md) vengono campionati usando sia il linguaggio di programmazione Python che SQL. 
* I [**dati delle tabelle Hive**](sample-data-hive.md) vengono campionati tramite query Hive.

Questo campionamento è un passaggio del [Processo di analisi scientifica dei dati per i team (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

**Perché campionare i dati?**

Se il set di dati da analizzare è grande, è in genere opportuno sottocampionare i dati per ridurlo e ottenere dimensioni inferiori più facilmente gestibili ma comunque rappresentative. Il ridimensionamento può facilitare la comprensione dei dati, l'esplorazione e la progettazione delle funzionalità. Questo ruolo di campionamento nel processo di analisi Cortana è l'abilitazione della prototipazione rapida delle funzioni di elaborazione dei dati e dei modelli di machine learning.

