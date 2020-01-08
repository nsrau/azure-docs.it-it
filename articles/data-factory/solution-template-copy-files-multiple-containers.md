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
ms.openlocfilehash: 0c4c26ba163f83483b3eb48e51d91f9a919a887c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75439800"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>Copiare file da più contenitori con Azure Data Factory

Questo articolo descrive un modello di soluzione che è possibile usare per copiare file da più contenitori tra archivi di file. Ad esempio, è possibile usarlo per eseguire la migrazione di data Lake da AWS S3 a Azure Data Lake Store. In alternativa, è possibile usare il modello per replicare tutti gli elementi da un account di archiviazione BLOB di Azure a un altro.

> [!NOTE]
> Se si desidera copiare i file da un singolo contenitore, è più efficiente utilizzare lo [strumento copia dati](copy-data-tool.md) per creare una pipeline con un'unica attività di copia. Il modello in questo articolo è più di quello necessario per questo semplice scenario.

## <a name="about-this-solution-template"></a>Informazioni sul modello di soluzione

Questo modello enumera i contenitori dall'archivio di archiviazione di origine. Quindi copia i contenitori nell'archivio di destinazione.

Il modello contiene tre attività:
- **GetMetadata** analizza l'archivio di archiviazione di origine e ottiene l'elenco dei contenitori.
- **Foreach** Ottiene l'elenco di contenitori dall'attività **GetMetadata** , quindi scorre l'elenco e passa ogni contenitore all'attività di copia.
- **Copy copia** ogni contenitore dall'archivio di archiviazione di origine nell'archivio di destinazione.

Il modello definisce i parametri seguenti:
- *SourceFileFolder* è il percorso della cartella dell'archivio dell'origine dati, in cui è possibile ottenere un elenco dei contenitori. Il percorso è la directory radice, che contiene più cartelle del contenitore. Il valore predefinito di questo parametro è `sourcefolder`.
- *SourceFileDirectory* è il percorso della sottocartella nella directory radice dell'archivio dell'origine dati. Il valore predefinito di questo parametro è `subfolder`.
- *DestinationFileFolder* è il percorso della cartella in cui verranno copiati i file nell'archivio di destinazione. Il valore predefinito di questo parametro è `destinationfolder`.
- *DestinationFileDirectory* è il percorso della sottocartella in cui verranno copiati i file nell'archivio di destinazione. Il valore predefinito di questo parametro è `subfolder`.

## <a name="how-to-use-this-solution-template"></a>Come usare questo modello di soluzione

1. Passare al modello **copia più file di contenitori tra archivi di file** . Creare una **nuova** connessione all'archivio di archiviazione di origine. L'archivio di archiviazione di origine è il punto in cui si vogliono copiare i file da più contenitori da.

    ![Creare una nuova connessione all'origine](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. Creare una **nuova** connessione all'archivio di archiviazione di destinazione.

    ![Creare una nuova connessione alla destinazione](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. Selezionare **Usa questo modello**.

    ![Usa questo modello](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. Verrà visualizzata la pipeline, come nell'esempio seguente:

    ![Mostra la pipeline](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. Selezionare **debug**, immettere i **parametri**e quindi fare clic su **fine**.

    ![Eseguire la pipeline](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. Esaminare il risultato.

    ![Esaminare il risultato](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire una copia bulk da un database tramite una tabella di controllo con Azure Data Factory](solution-template-bulk-copy-with-control-table.md)

- [Copia i file da più contenitori con Azure Data Factory](solution-template-copy-files-multiple-containers.md)
