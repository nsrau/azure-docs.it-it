---
title: Copiare file da più contenitori
description: Informazioni su come usare un modello di soluzione per copiare file da più contenitori usando Azure Data Factory.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/1/2018
ms.openlocfilehash: f78d0b02c9790234a63ef64200dcab72bc64c033
ms.sourcegitcommit: 3e8058f0c075f8ce34a6da8db92ae006cc64151a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92629426"
---
# <a name="copy-multiple-folders-with-azure-data-factory"></a>Copia più cartelle con Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Questo articolo descrive un modello di soluzione che è possibile usare più attività di copia per copiare contenitori o cartelle tra archivi basati su file, in cui ogni attività di copia dovrebbe copiare un singolo contenitore o una cartella. 

> [!NOTE]
> Se si desidera copiare i file da un singolo contenitore, è più efficiente utilizzare lo [strumento copia dati](copy-data-tool.md) per creare una pipeline con un'unica attività di copia. Il modello in questo articolo è più di quello necessario per questo semplice scenario.

## <a name="about-this-solution-template"></a>Informazioni sul modello di soluzione

Questo modello enumera le cartelle da una determinata cartella padre nell'archivio di archiviazione di origine. Ogni cartella viene quindi copiata nell'archivio di destinazione.

Il modello contiene tre attività:
- **GetMetadata** analizza l'archivio di archiviazione di origine e ottiene l'elenco di sottocartelle da una cartella padre specificata.
- **Foreach** Ottiene l'elenco di sottocartelle dall'attività **GetMetadata** , quindi scorre l'elenco e passa ogni cartella all'attività di copia.
- **Copia** copia ogni cartella dall'archivio di archiviazione di origine nell'archivio di destinazione.

Il modello definisce i parametri seguenti:
- *SourceFileFolder* fa parte del percorso della cartella padre dell'archivio dell'origine dati: *SourceFileFolder/SourceFileDirectory* , in cui è possibile ottenere un elenco delle sottocartelle. 
- *SourceFileDirectory* fa parte del percorso della cartella padre dell'archivio dell'origine dati: *SourceFileFolder/SourceFileDirectory* , in cui è possibile ottenere un elenco delle sottocartelle. 
- *DestinationFileFolder* fa parte del percorso della cartella padre: *DestinationFileFolder/DestinationFileDirectory* in cui verranno copiati i file nell'archivio di destinazione. 
- *DestinationFileDirectory* fa parte del percorso della cartella padre: *DestinationFileFolder/DestinationFileDirectory* in cui verranno copiati i file nell'archivio di destinazione. 

Se si desidera copiare più contenitori in cartelle radice tra gli archivi di archiviazione, è possibile immettere tutti e quattro i parametri come */* . In questo modo si eseguirà la replica di tutti gli archivi di archiviazione.

## <a name="how-to-use-this-solution-template"></a>Come usare questo modello di soluzione

1. Passare al modello **copia più file di contenitori tra archivi di file** . Creare una **nuova** connessione all'archivio di archiviazione di origine. L'archivio di archiviazione di origine è il punto in cui si vogliono copiare i file da più contenitori da.

    ![Creare una nuova connessione all'origine](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. Creare una **nuova** connessione all'archivio di archiviazione di destinazione.

    ![Creare una nuova connessione alla destinazione](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. Selezionare **Usa questo modello** .

    ![Usa questo modello](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. Verrà visualizzata la pipeline, come nell'esempio seguente:

    ![Mostra la pipeline](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. Selezionare **Debug** , immettere i valori in **Parametri** , quindi selezionare **Fine** .

    ![Eseguire la pipeline](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. Esaminare il risultato.

    ![Esaminare il risultato](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire una copia bulk da un database tramite una tabella di controllo con Azure Data Factory](solution-template-bulk-copy-with-control-table.md)

- [Copiare file da più contenitori con Azure Data Factory](solution-template-copy-files-multiple-containers.md)
