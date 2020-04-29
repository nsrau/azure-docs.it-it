---
title: Copia bulk da file nel database
description: Informazioni su come usare un modello di soluzione per copiare i dati in blocco da Azure Data Lake Storage Gen2 ad Azure sinapsi Analytics/database SQL di Azure.
services: data-factory
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 070b708f204006bc1ba90c4c3676696291fde902
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81414876"
---
# <a name="bulk-copy-from-files-to-database"></a>Copia bulk da file nel database

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo descrive un modello di soluzione che è possibile usare per copiare i dati in blocco da Azure Data Lake Storage Gen2 ad Azure sinapsi Analytics/database SQL di Azure.

## <a name="about-this-solution-template"></a>Informazioni sul modello di soluzione

Questo modello recupera i file dall'origine Azure Data Lake Storage Gen2. Esegue quindi l'iterazione di ogni file nell'origine e copia il file nell'archivio dati di destinazione. 

Attualmente questo modello supporta solo la copia di dati in formato **DelimitedText** . I file in altri formati di dati possono anche essere recuperati dall'archivio dati di origine, ma non possono essere copiati nell'archivio dati di destinazione.  

Il modello contiene tre attività:
- **Get Metadata** Activity recupera i file da Azure Data Lake storage Gen2 e li passa all'attività *foreach* successiva.
- L'attività **foreach** ottiene i file dall'attività *Recupera metadati* e scorre ogni file nell'attività di *copia* .
- L'attività di **copia** risiede nell'attività *foreach* per copiare ogni file dall'archivio dati di origine all'archivio dati di destinazione.

Il modello definisce i due parametri seguenti:
- *SourceContainer* è il percorso del contenitore radice in cui vengono copiati i dati nel Azure Data Lake storage Gen2. 
- *SourceDirectory* è il percorso della directory sotto il contenitore radice in cui vengono copiati i dati nel Azure Data Lake storage Gen2.

## <a name="how-to-use-this-solution-template"></a>Come usare questo modello di soluzione

1. Passare al modello di **copia bulk da file a database** . Creare una **nuova** connessione all'archivio Gen2 di origine. Tenere presente che "GetMetadataDataset" e "SourceDataset" sono riferimenti alla stessa connessione dell'archivio file di origine.

    ![Crea una nuova connessione all'archivio dati di origine](media/solution-template-bulk-copy-from-files-to-database/source-connection.png)

2. Creare una **nuova** connessione all'archivio dati sink in cui vengono copiati i dati.

    ![Crea una nuova connessione all'archivio dati sink](media/solution-template-bulk-copy-from-files-to-database/destination-connection.png)
    
3. Selezionare **Usa questo modello**.

    ![Usa questo modello](media/solution-template-bulk-copy-from-files-to-database/use-template.png)
    
4. Viene visualizzata una pipeline creata come illustrato nell'esempio seguente:

    ![Esaminare la pipeline](media/solution-template-bulk-copy-from-files-to-database/new-pipeline.png)

    > [!NOTE]
    > Se si sceglie **Azure sinapsi Analytics (in precedenza SQL DW)** come destinazione dei dati nel **passaggio 2** indicato in precedenza, è necessario immettere una connessione all'archivio BLOB di Azure per la gestione temporanea, come richiesto dalla SQL data warehouse di base. Come illustrato nello screenshot seguente, il modello genererà automaticamente un *percorso di archiviazione* per l'archivio BLOB. Controllare se il contenitore è stato creato dopo l'esecuzione della pipeline.
        
    ![Impostazione di PolyBase](media/solution-template-bulk-copy-from-files-to-database/staging-account.png)

5. Selezionare **debug**, immettere i **parametri**e quindi fare clic su **fine**.

    ![Fare clic su * * debug * *](media/solution-template-bulk-copy-from-files-to-database/debug-run.png)

6. Quando l'esecuzione della pipeline viene completata correttamente, verranno visualizzati risultati simili a quelli dell'esempio seguente:

    ![Esaminare il risultato](media/solution-template-bulk-copy-from-files-to-database/run-succeeded.png)

       
## <a name="next-steps"></a>Passaggi successivi

- [Introduzione a Data factory di Azure](introduction.md)