---
title: Campionare i dati in contenitori BLOB di Azure, SQL Server e nelle tabelle Hive | Documentazione Microsoft
description: Come esplorare i dati archiviati in vari ambienti di Azure.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 80a9dfae-e3a6-4cfb-aecc-5701cfc7e39d
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: 0efd754936b67611a747c6c5756de92443a937e4
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2018
ms.locfileid: "34838000"
---
# <a name="heading"></a>Campionare i dati in contenitori BLOB di Azure, SQL Server e nelle tabelle Hive
Questo documento include articoli che illustrano come campionare i dati archiviati in uno fra tre diversi percorsi di Azure:

* **I dati del contenitore BLOB di Azure** vengono campionati scaricandoli a livello di programmazione ed eseguendo il successivo campionamento usando un codice Python di esempio.
* **Dati di SQL Server** vengono campionati utilizzando sia il linguaggio di programmazione Python che SQL. 
* **Dati della tabella hive** vengono campionati utilizzando le query Hive.

Il **menu** seguente contiene collegamenti ad argomenti che descrivono come campionare i dati da ognuno di questi ambienti di archiviazione di Azure. 

[!INCLUDE [cap-sample-data-selector](../../../includes/cap-sample-data-selector.md)]

Questo campionamento è un passaggio del [Processo di analisi scientifica dei dati per i team (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

**Perché campionare i dati?**

Se il set di dati da analizzare è grande, è in genere opportuno sottocampionare i dati per ridurlo e ottenere dimensioni inferiori più facilmente gestibili ma comunque rappresentative. Questa operazione facilita la comprensione e l'esplorazione dei dati, nonché la progettazione di funzionalità. Il suo ruolo nel Cortana Analytics Process consiste nell'abilitare la creazione relativa a prototipi di funzioni di elaborazione dei dati e di modelli per l'apprendimento automatico.

