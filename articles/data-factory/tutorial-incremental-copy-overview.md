---
title: 'Eseguire la copia incrementale di dati con Azure Data Factory '
description: Queste esercitazioni mostrano come eseguire la copia incrementale di dati da un archivio dati di origine a un archivio dati di destinazione. Nella prima vengono copiati dati da una tabella.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: yexu
ms.openlocfilehash: 42dcd6ecc6df1c9877581d89bf22724054e305d0
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74217279"
---
# <a name="incrementally-load-data-from-a-source-data-store-to-a-destination-data-store"></a>Caricare dati in modo incrementale da un archivio dati di origine a un archivio dati di destinazione

In una soluzione di integrazione dei dati il caricamento incrementale (o differenziale) dei dati dopo un caricamento di dati iniziale completo è uno scenario ampiamente diffuso. Le esercitazioni incluse in questa sezione mostrano diversi metodi per caricare dati in modo incrementale usando Azure Data Factory.

## <a name="delta-data-loading-from-database-by-using-a-watermark"></a>Caricamento differenziale dei dati usando un limite
In questo caso, è necessario definire un limite nel database di origine. Un limite è una colonna che contiene l'ultimo timestamp aggiornato o una chiave di incremento. La soluzione di caricamento differenziale carica i dati modificati tra un limite precedente e uno nuovo. Il flusso di lavoro per questo approccio è raffigurato nel diagramma seguente: 

![Flusso di lavoro per l'uso di un limite](media/tutorial-incremental-copy-overview/workflow-using-watermark.png)

Per istruzioni dettagliate, vedere le esercitazioni seguenti: 
- [Eseguire la copia incrementale di dati da una tabella del database SQL di Azure a un archivio BLOB di Azure](tutorial-incremental-copy-powershell.md)
- [Eseguire la copia incrementale di dati da più tabelle di un database di SQL Server locale al database SQL di Azure](tutorial-incremental-copy-multiple-tables-powershell.md)

Per i modelli vedere l'argomento seguente:
- [Eseguire una copia differenziale con la tabella di controllo](solution-template-delta-copy-with-control-table.md)

## <a name="delta-data-loading-from-sql-db-by-using-the-change-tracking-technology"></a>Caricamento differenziale dei dati dal database SQL usando la tecnologia Rilevamento modifiche
La tecnologia Rilevamento modifiche è una soluzione leggera inclusa in SQL Server e nel database SQL di Azure che offre un efficiente meccanismo di rilevamento delle modifiche per le applicazioni. Questa tecnologia consente a un'applicazione di identificare facilmente i dati inseriti, aggiornati o eliminati. 

Il flusso di lavoro per questo approccio è raffigurato nel diagramma seguente:

![Flusso di lavoro per l'uso di Rilevamento modifiche](media/tutorial-incremental-copy-overview/workflow-using-change-tracking.png)

Per istruzioni dettagliate, vedere l'esercitazione seguente: <br/>
- [Eseguire la copia incrementale di dati dal database SQL di Azure a un archivio BLOB di Azure usando la tecnologia Rilevamento modifiche](tutorial-incremental-copy-change-tracking-feature-powershell.md)

## <a name="loading-new-and-changed-files-only-by-using-lastmodifieddate"></a>Caricamento dei soli file nuovi e modificati usando LastModifiedDate
È possibile copiare i file nuovi e modificati solo usando LastModifiedDate nell'archivio di destinazione. Azure Data Factory analizza tutti i file dall'archivio di origine, applica il filtro LastModifiedDate sui file e copia nell'archivio di destinazione solo quello nuovo e aggiornato più di recente.  Tenere presente che, se Azure Data Factory analizza enormi quantità di file, ma si copiano pochi file nella destinazione, bisognerà comunque aspettarsi un'operazione lunga, perché anche l'analisi dei file richiede tempo.   

Per istruzioni dettagliate, vedere l'esercitazione seguente: <br/>
- [Incrementally copy new and changed files based on LastModifiedDate from Azure Blob storage to Azure Blob storage](tutorial-incremental-copy-lastmodified-copy-data-tool.md) (Eseguire la copia incrementale dei file nuovi e modificati in base a LastModifiedDate da Archiviazione BLOB di Azure ad Archiviazione BLOB di Azure)

Per i modelli vedere l'argomento seguente:
- [Copiare i nuovi file in base a LastModifiedDate](solution-template-copy-new-files-lastmodifieddate.md)

## <a name="loading-new-files-only-by-using-time-partitioned-folder-or-file-name"></a>Caricamento dei soli file nuovi usando il nome di file o cartella partizionato in base al tempo
È possibile copiare solo i nuovi file se i file o le cartelle sono già stati partizionati in base al tempo con informazioni temporali inserite nel nome di file o cartella (ad esempio, /aaaa/mm/gg/file.csv). Questo è l'approccio più efficace in termini di prestazioni per il caricamento incrementale di nuovi file. 

Per istruzioni dettagliate, vedere l'esercitazione seguente: <br/>
- [Incrementally copy new files based on time partitioned folder or file name from Azure Blob storage to Azure Blob storage](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md) (Eseguire la copia incrementale dei file nuovi in base al nome di cartella o di file partizionato in base al tempo da Archiviazione BLOB di Azure ad Archiviazione BLOB di Azure)

## <a name="next-steps"></a>Passaggi successivi
Passare all'esercitazione seguente: 

> [!div class="nextstepaction"]
>[Eseguire la copia incrementale di dati da una tabella del database SQL di Azure a un archivio BLOB di Azure](tutorial-incremental-copy-powershell.md)
