---
title: Copiare file da più contenitori
description: Informazioni su come usare un modello di soluzione per copiare file da più contenitori usando Azure Data Factory.Learn how to use a solution template to copy files from multiple containers by using Azure Data Factory.
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
ms.openlocfilehash: 0c4c26ba163f83483b3eb48e51d91f9a919a887c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75439800"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>Copiare file da più contenitori con Azure Data Factory

In questo articolo viene descritto un modello di soluzione che è possibile utilizzare per copiare file da più contenitori tra archivi di file. Ad esempio, puoi usarlo per eseguire la migrazione del tuo data lake da AWS S3 ad Azure Data Lake Store. In alternativa, è possibile usare il modello per replicare tutto da un account di archiviazione BLOB di Azure a un altro.

> [!NOTE]
> Se si desidera copiare file da un singolo contenitore, è più efficiente usare lo [strumento Copia dati](copy-data-tool.md) per creare una pipeline con una singola attività di copia. Il modello in questo articolo è più di quanto necessario per questo semplice scenario.

## <a name="about-this-solution-template"></a>Informazioni sul modello di soluzione

Questo modello enumera i contenitori dall'archivio di archiviazione di origine. Copia quindi tali contenitori nell'archivio di destinazione.

Il modello contiene tre attività:
- **GetMetadata** analizza l'archivio di archiviazione di origine e ottiene l'elenco dei contenitori.
- **ForEach** ottiene l'elenco di contenitori dall'attività **GetMetadata** e quindi scorre l'elenco e passa ogni contenitore all'attività Copy.
- **Copia** ogni contenitore dall'archivio di archiviazione di origine all'archivio di destinazione.

Il modello definisce i parametri seguenti:
- *SourceFileFolder* è il percorso della cartella dell'archivio origine dati, in cui è possibile ottenere un elenco dei contenitori. Il percorso è la directory radice, che contiene più cartelle contenitore. Il valore predefinito di questo parametro è `sourcefolder`.
- *SourceFileDirectory* è il percorso della sottocartella nella directory radice dell'archivio origine dati. Il valore predefinito di questo parametro è `subfolder`.
- *DestinationFileFolder* è il percorso della cartella in cui verranno copiati i file nell'archivio di destinazione. Il valore predefinito di questo parametro è `destinationfolder`.
- *DestinationFileDirectory* è il percorso della sottocartella in cui verranno copiati i file nell'archivio di destinazione. Il valore predefinito di questo parametro è `subfolder`.

## <a name="how-to-use-this-solution-template"></a>Come usare questo modello di soluzione

1. Passare al modello Copia più contenitori di **file tra archivi file.** Creare una **nuova** connessione all'archivio di archiviazione di origine. L'archivio di archiviazione di origine è il punto da cui si desidera copiare i file da più contenitori.

    ![Creare una nuova connessione all'origine](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. Creare una **nuova** connessione all'archivio di archiviazione di destinazione.

    ![Creare una nuova connessione alla destinazione](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. Selezionare **Usa questo modello**.

    ![Usa questo modello](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. Verrà visualizzata la pipeline, come nell'esempio seguente:You'll see the pipeline, as in the following example:

    ![Mostra la pipeline](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. Selezionare **Debug**, immettere i **parametri**, quindi scegliere **Fine**.

    ![Eseguire la pipeline](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. Esaminare il risultato.

    ![Esaminare il risultato](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>Passaggi successivi

- [Copia di massa da un database usando una tabella di controllo con Azure Data FactoryBulk copy from a database by using a control table with Azure Data Factory](solution-template-bulk-copy-with-control-table.md)

- [Copiare file da più contenitori con Azure Data Factory](solution-template-copy-files-multiple-containers.md)
