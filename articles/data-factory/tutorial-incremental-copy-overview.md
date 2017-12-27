---
title: Eseguire la copia incrementale dei dati usando Azure Data Factory | Microsoft Docs
description: Queste esercitazioni mostrano come eseguire la copia incrementale di dati da un archivio dati di origine a un archivio dati di destinazione. Nella prima vengono copiati dati da una tabella.
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/05/2017
ms.author: shlo
ms.openlocfilehash: f23ca6862d0a0e67245f02dc723f61da8f41b6a0
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2017
---
# <a name="incrementally-load-data-from-a-source-data-store-to-a-destination-data-store"></a>Caricare dati in modo incrementale da un archivio dati di origine a un archivio dati di destinazione

In una soluzione di integrazione dei dati il caricamento incrementale (o differenziale) dei dati dopo un caricamento di dati iniziale completo è uno scenario ampiamente diffuso. Le esercitazioni incluse in questa sezione mostrano diversi metodi per caricare dati in modo incrementale usando Azure Data Factory versione 2.

## <a name="delta-data-loading-by-using-a-watermark"></a>Caricamento di dati differenziale usando un limite
In questo caso, è necessario definire un limite nel database di origine. Un limite è una colonna che contiene l'ultimo timestamp aggiornato o una chiave di incremento. La soluzione di caricamento differenziale carica i dati modificati tra un limite precedente e uno nuovo. Il flusso di lavoro per questo approccio è raffigurato nel diagramma seguente: 

![Flusso di lavoro per l'uso di un limite](media/tutorial-incremental-copy-overview/workflow-using-watermark.png)

Per istruzioni dettagliate, vedere le esercitazioni seguenti: 

- [Eseguire la copia incrementale di dati da una tabella del database SQL di Azure a un archivio BLOB di Azure](tutorial-incremental-copy-powershell.md)
- [Eseguire la copia incrementale di dati da più tabelle di un database di SQL Server locale al database SQL di Azure](tutorial-incremental-copy-multiple-tables-powershell.md)


## <a name="delta-data-loading-by-using-the-change-tracking-technology"></a>Caricamento di dati differenziale tramite la tecnologia Rilevamento modifiche
La tecnologia Rilevamento modifiche è una soluzione leggera inclusa in SQL Server e nel database SQL di Azure che offre un efficiente meccanismo di rilevamento delle modifiche per le applicazioni. Questa tecnologia consente a un'applicazione di identificare facilmente i dati inseriti, aggiornati o eliminati. 

Il flusso di lavoro per questo approccio è raffigurato nel diagramma seguente:

![Flusso di lavoro per l'uso di Rilevamento modifiche](media/tutorial-incremental-copy-overview/workflow-using-change-tracking.png)

Per istruzioni dettagliate, vedere l'esercitazione seguente: <br/>
[Eseguire la copia incrementale di dati dal database SQL di Azure a un archivio BLOB di Azure usando la tecnologia Rilevamento modifiche](tutorial-incremental-copy-change-tracking-feature-powershell.md)


## <a name="next-steps"></a>Passaggi successivi
Passare all'esercitazione seguente: 

> [!div class="nextstepaction"]
>[Eseguire la copia incrementale di dati da una tabella del database SQL di Azure a un archivio BLOB di Azure](tutorial-incremental-copy-powershell.md)
