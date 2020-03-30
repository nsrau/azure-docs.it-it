---
title: Copia bulk da file nel database
description: Informazioni su come usare un modello di soluzione per copiare i dati in blocco da Azure Data Lake Storage Gen2 ad Azure Synapse Analytics / Database SQL di Azure.Learn how to use a solution template to copy data in bulk from Azure Data Lake Storage Gen2 to Azure Synapse Analytics/ Azure SQL Database.
services: data-factory
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: ae250c7d15801789ad22955845cfa535ed91f2c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75921150"
---
# <a name="bulk-copy-from-files-to-database"></a>Copia bulk da file nel database

Questo articolo descrive un modello di soluzione che è possibile usare per copiare i dati in blocco da Azure Data Lake Storage Gen2 ad Azure Synapse Analytics / Database SQL di Azure.This article describes a solution template that you can use to copy data in bulk from Azure Data Lake Storage Gen2 to Azure Synapse Analytics / Azure SQL Database.

## <a name="about-this-solution-template"></a>Informazioni sul modello di soluzione

Questo modello recupera i file dall'origine Gen2 di Archiviazione di Azure Data Lake.This template retrieves files from Azure Data Lake Storage Gen2 source. Quindi scorre ogni file nell'origine e copia il file nell'archivio dati di destinazione. 

Attualmente questo modello supporta solo la copia dei dati in formato **DelimitedText.** I file in altri formati di dati possono anche essere recuperati dall'archivio dati di origine, ma non possono essere copiati nell'archivio dati di destinazione.  

Il modello contiene tre attività:
- **L'attività Get Metadata** recupera i file da Azure Data Lake Storage Gen2 e li passa all'attività *ForEach* successiva.
- **L'attività ForEach** ottiene i file dall'attività *Get Metadata* e scorre ogni file all'attività *Copy.*
- **L'attività di** copia si trova nell'attività *ForEach* per copiare ogni file dall'archivio dati di origine all'archivio dati di destinazione.

Il modello definisce i due parametri seguenti:
- *SourceContainer* è il percorso del contenitore radice da cui vengono copiati i dati in Azure Data Lake Storage Gen2.SourceContainer is the root container path where the data is copied from in your Azure Data Lake Storage Gen2. 
- *SourceDirectory* è il percorso della directory nel contenitore radice da cui vengono copiati i dati in Azure Data Lake Storage Gen2.

## <a name="how-to-use-this-solution-template"></a>Come usare questo modello di soluzione

1. Passare al modello **Copia globale da file nel database.** Creare una **nuova** connessione all'archivio Gen2 di origine. Tenere presente che "GetMetadataDataset" e "SourceDataset" sono riferimenti alla stessa connessione dell'archivio file di origine.

    ![Creare una nuova connessione all'archivio dati di origineCreate a new connection to the source data store](media/solution-template-bulk-copy-from-files-to-database/source-connection.png)

2. Creare una **nuova** connessione all'archivio dati sink in cui si stanno copiando i dati.

    ![Creare una nuova connessione all'archivio dati sinkCreate a new connection to the sink data store](media/solution-template-bulk-copy-from-files-to-database/destination-connection.png)
    
3. Selezionare **Usa questo modello**.

    ![Usa questo modello](media/solution-template-bulk-copy-from-files-to-database/use-template.png)
    
4. Verrà visualizzata una pipeline creata come illustrato nell'esempio seguente:You would see a pipeline created as shown in the following example:

    ![Esaminare la pipeline](media/solution-template-bulk-copy-from-files-to-database/new-pipeline.png)

    > [!NOTE]
    > Se si sceglie **Analisi synapse di Azure (in precedenza SQL DW)** come destinazione dei dati nel **passaggio 2** indicato in precedenza, è necessario immettere una connessione all'archiviazione BLOB di Azure per la gestione temporanea, come richiesto da SQL Data Warehouse Polybase. Come illustrato nella schermata seguente, il modello genererà automaticamente un *percorso di archiviazione* per l'archiviazione BLOB. Verificare se il contenitore è stato creato dopo l'esecuzione della pipeline.
        
    ![Impostazione di PolyBase](media/solution-template-bulk-copy-from-files-to-database/staging-account.png)

5. Selezionare **Debug**, immettere i **parametri**, quindi scegliere **Fine**.

    ![Fare clic su Debug](media/solution-template-bulk-copy-from-files-to-database/debug-run.png)

6. Quando l'esecuzione della pipeline viene completata correttamente, vengono visualizzati risultati simili all'esempio seguente:When the pipeline run completes successfully, you would see results similar to the following example:

    ![Esaminare il risultato](media/solution-template-bulk-copy-from-files-to-database/run-succeeded.png)

       
## <a name="next-steps"></a>Passaggi successivi

- [Introduzione a Data factory di Azure](introduction.md)