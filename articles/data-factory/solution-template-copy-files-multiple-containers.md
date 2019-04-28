---
title: Copiare i file da più contenitori con Azure Data Factory | Microsoft Docs
description: Informazioni su come usare un modello di soluzione per copiare i file da più contenitori con Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/1/2018
ms.openlocfilehash: a52729adf8d6df3f4e44e561b45b854db433628c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60635181"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>Copiare file da più contenitori con Azure Data Factory

Questo articolo descrive un modello di soluzione che è possibile usare per copiare i file da più contenitori di dati tra archivi di file. Ad esempio, è possibile utilizzare per eseguire la migrazione il lake dati da AWS S3 per Azure Data Lake Store. In alternativa, è possibile usare il modello di replicare tutti gli elementi da un account di archiviazione Blob di Azure a un altro.

> [!NOTE]
> Se si desidera copiare i file da un singolo contenitore, è preferibile usare il [lo strumento Copia dati](copy-data-tool.md) per creare una pipeline con una singola attività di copia. Il modello in questo articolo è più bisogno per tale scenario semplice.

## <a name="about-this-solution-template"></a>Informazioni sul modello di soluzione

Questo modello consente di enumerare i contenitori dall'archivio di archiviazione di origine. Copia quindi tali contenitori nell'archivio di destinazione.

Il modello contiene tre attività:
- **GetMetadata** analizza l'archivio di archiviazione di origine e ottiene l'elenco dei contenitori.
- **ForEach** Ottiene l'elenco dei contenitori dal **GetMetadata** attività scorre l'elenco e quindi passa ogni contenitore all'attività di copia.
- **Copia** copia ogni contenitore dall'archivio di archiviazione di origine nell'archivio di destinazione.

Il modello definisce due parametri:
- *Percorsofileorigine* rappresenta il percorso dell'archivio dell'origine dati, in cui è possibile ottenere un elenco dei contenitori. Nella maggior parte dei casi, il percorso è la cartella radice, che contiene più cartelle dei contenitori. Il valore predefinito per questo parametro è `/`.
- *Percorsofiledestinazione* è il percorso in cui verranno copiati i file nell'archivio di destinazione. Il valore predefinito per questo parametro è `/`.

## <a name="how-to-use-this-solution-template"></a>Come usare questo modello di soluzione

1. Andare alla **copiare più contenitori di file tra gli archivi di File** modello. Creare un **New** connessione all'archivio di archiviazione di origine. L'archivio di archiviazione di origine è in cui si desidera copiare i file da più contenitori da.

    ![Creare una nuova connessione all'origine](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. Creare un **New** connessione all'archivio di archiviazione di destinazione.

    ![Creare una nuova connessione alla destinazione](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. Selezionare **usare questo modello**.

    ![Usa questo modello](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. Verrà visualizzata la pipeline, come nell'esempio seguente:

    ![Mostra la pipeline](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. Selezionare **eseguire il Debug**, immettere il **parametri**, quindi selezionare **fine**.

    ![Eseguire la pipeline](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. Esaminare il risultato.

    ![Esaminare il risultato](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>Passaggi successivi

- [Copia bulk da un database usando una tabella di controllo con Azure Data Factory](solution-template-bulk-copy-with-control-table.md)

- [Copiare i file da più contenitori con Azure Data Factory](solution-template-copy-files-multiple-containers.md)