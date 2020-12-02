---
title: Copia bulk da file nel database
description: Informazioni su come usare un modello di soluzione per copiare i dati in blocco da Azure Data Lake Storage Gen2 ad Azure Synapse Analytics/database SQL di Azure.
services: data-factory
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: ecf3c3954bd77962e56ee289b623806efb352410
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461268"
---
# <a name="bulk-copy-from-files-to-database"></a>Copia bulk da file nel database

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Questo articolo descrive un modello di soluzione che è possibile usare per copiare i dati in blocco da Azure Data Lake Storage Gen2 ad Azure Synapse Analytics/database SQL di Azure.

## <a name="about-this-solution-template"></a>Informazioni sul modello di soluzione

Questo modello recupera i file dall'origine Azure Data Lake Storage Gen2. Esegue quindi l'iterazione su ogni file nell'origine e copia i file nell'archivio dati di destinazione. 

Attualmente questo modello supporta solo la copia di dati in formato **DelimitedText**. È possibile recuperare anche i file in altri formati di dati dall'archivio dati di origine, ma non è possibile copiarli nell'archivio dati di destinazione.  

Il modello contiene tre attività:
- L'attività **Get Metadata** recupera i file da Azure Data Lake Storage Gen2 e li passa all'attività *ForEach* successiva.
- L'attività **ForEach** ottiene i file dall'attività *Get Metadata* ed esegue l'iterazione di ogni file all'attività *Copy*.
- L'attività **Copy** risiede nell'attività *ForEach* per copiare ogni file dall'archivio dati di origine all'archivio dati di destinazione.

Il modello definisce i due parametri seguenti:
- *SourceContainer* è il percorso del contenitore radice in cui i dati vengono copiati in Azure Data Lake Storage Gen2. 
- *SourceDirectory* è il percorso della directory nel contenitore radice da cui i dati vengono copiati in Azure Data Lake Storage Gen2.

## <a name="how-to-use-this-solution-template"></a>Come usare questo modello di soluzione

1. Passare al modello **Copia bulk dai file al database**. Creare una **nuova** connessione all'archivio Gen2 di origine. Tenere presente che "GetMetadataDataset" e "SourceDataset" sono riferimenti alla stessa connessione dell'archivio del file di origine.

    ![Creare una nuova connessione all'archivio dati di origine](media/solution-template-bulk-copy-from-files-to-database/source-connection.png)

2. Creare una **nuova** connessione all'archivio dati di sink in cui si stanno copiando i dati.

    ![Creare una nuova connessione all'archivio dati di sink](media/solution-template-bulk-copy-from-files-to-database/destination-connection.png)
    
3. Selezionare **Usa questo modello**.

    ![Usa questo modello](media/solution-template-bulk-copy-from-files-to-database/use-template.png)
    
4. Viene visualizzata una pipeline creata come illustrato nell'esempio seguente:

    ![Esaminare la pipeline](media/solution-template-bulk-copy-from-files-to-database/new-pipeline.png)

    > [!NOTE]
    > Se si sceglie **Azure sinapsi Analytics (in precedenza SQL DW)** come destinazione dei dati nel **passaggio 2** indicato in precedenza, è necessario immettere una connessione all'archivio BLOB di Azure per la gestione temporanea, come richiesto dalla polibase di analisi delle sinapsi di Azure. Come illustrato nello screenshot seguente, il modello genererà automaticamente un *percorso di archiviazione* per l'archiviazione BLOB. Controllare che il contenitore sia stato creato dopo l'esecuzione della pipeline.
        
    ![Impostazione di PolyBase](media/solution-template-bulk-copy-from-files-to-database/staging-account.png)

5. Selezionare **Debug**, immettere i valori in **Parametri** e quindi selezionare **Fine**.

    ![Fare clic su **Debug **](media/solution-template-bulk-copy-from-files-to-database/debug-run.png)

6. Al termine dell'esecuzione della pipeline, verranno visualizzati risultati simili a quelli dell'esempio seguente:

    ![Esaminare il risultato](media/solution-template-bulk-copy-from-files-to-database/run-succeeded.png)

       
## <a name="next-steps"></a>Passaggi successivi

- [Introduzione al servizio Azure Data Factory](introduction.md)