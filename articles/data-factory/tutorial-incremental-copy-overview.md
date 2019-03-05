---
title: Eseguire la copia incrementale dei dati usando Azure Data Factory | Microsoft Docs
description: Queste esercitazioni mostrano come eseguire la copia incrementale di dati da un archivio dati di origine a un archivio dati di destinazione. Nella prima vengono copiati dati da una tabella.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: yexu
ms.openlocfilehash: cb75e943416c227730589ab5e7feeb7b8ba5e245
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56957930"
---
# <a name="incrementally-load-data-from-a-source-data-store-to-a-destination-data-store"></a>Caricare dati in modo incrementale da un archivio dati di origine a un archivio dati di destinazione

In una soluzione di integrazione dei dati il caricamento incrementale (o differenziale) dei dati dopo un caricamento di dati iniziale completo è uno scenario ampiamente diffuso. Le esercitazioni incluse in questa sezione mostrano diversi metodi per caricare dati in modo incrementale usando Azure Data Factory.

## <a name="delta-data-loading-from-database-by-using-a-watermark"></a>Caricamento differenziale dei dati usando un limite
In questo caso, è necessario definire un limite nel database di origine. Un limite è una colonna che contiene l'ultimo timestamp aggiornato o una chiave di incremento. La soluzione di caricamento differenziale carica i dati modificati tra un limite precedente e uno nuovo. Il flusso di lavoro per questo approccio è raffigurato nel diagramma seguente: 

![Flusso di lavoro per l'uso di un limite](media/tutorial-incremental-copy-overview/workflow-using-watermark.png)

Per istruzioni dettagliate, vedere le esercitazioni seguenti: 

- [Eseguire la copia incrementale di dati da una tabella del database SQL di Azure a un archivio BLOB di Azure](tutorial-incremental-copy-powershell.md)
- [Eseguire la copia incrementale di dati da più tabelle di un database di SQL Server locale al database SQL di Azure](tutorial-incremental-copy-multiple-tables-powershell.md)

## <a name="delta-data-loading-from-sql-db-by-using-the-change-tracking-technology"></a>Caricamento differenziale dei dati dal database SQL usando la tecnologia Rilevamento modifiche
La tecnologia Rilevamento modifiche è una soluzione leggera inclusa in SQL Server e nel database SQL di Azure che offre un efficiente meccanismo di rilevamento delle modifiche per le applicazioni. Questa tecnologia consente a un'applicazione di identificare facilmente i dati inseriti, aggiornati o eliminati. 

Il flusso di lavoro per questo approccio è raffigurato nel diagramma seguente:

![Flusso di lavoro per l'uso di Rilevamento modifiche](media/tutorial-incremental-copy-overview/workflow-using-change-tracking.png)

Per istruzioni dettagliate, vedere l'esercitazione seguente: <br/>
[Eseguire la copia incrementale di dati dal database SQL di Azure a un archivio BLOB di Azure usando la tecnologia Rilevamento modifiche](tutorial-incremental-copy-change-tracking-feature-powershell.md)

## <a name="loading-new-and-changed-files-only-by-using-lastmodifieddate"></a>Caricamento dei soli file nuovi e modificati usando LastModifiedDate
È possibile prima ottenere i metadati (LastModifiedDate) dei file e quindi copiare solo i file nuovi e modificati nell'archivio di destinazione.

Per istruzioni dettagliate, vedere l'esercitazione seguente: <br/>
[Incrementally copy new and changed files based on LastModifiedDate from Azure Blob storage to Azure Blob storage](tutorial-incremental-copy-lastmodified-copy-data-tool.md) (Eseguire la copia incrementale dei file nuovi e modificati in base a LastModifiedDate da Archiviazione BLOB di Azure ad Archiviazione BLOB di Azure)

## <a name="loading-new-files-only-by-using-time-partitioned-folder-or-file-name"></a>Caricamento dei soli file nuovi usando il nome di file o cartella partizionato in base al tempo
È possibile copiare solo i nuovi file se i file o le cartelle sono già stati partizionati in base al tempo con informazioni temporali inserite nel nome di file o cartella (ad esempio, /aaaa/mm/gg/file.csv). 

Per istruzioni dettagliate, vedere l'esercitazione seguente: <br/>
[Incrementally copy new files based on time partitioned folder or file name from Azure Blob storage to Azure Blob storage](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md) (Eseguire la copia incrementale dei file nuovi in base al nome di cartella o di file partizionato in base al tempo da Archiviazione BLOB di Azure ad Archiviazione BLOB di Azure)

## <a name="next-steps"></a>Passaggi successivi
Passare all'esercitazione seguente: 

> [!div class="nextstepaction"]
>[Eseguire la copia incrementale di dati da una tabella del database SQL di Azure a un archivio BLOB di Azure](tutorial-incremental-copy-powershell.md)
