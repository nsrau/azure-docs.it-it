---
title: Copiare i file da più contenitori usando Azure Data Factory
description: Informazioni su come usare un modello di soluzione per copiare file da più contenitori usando Azure Data Factory.
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
ms.openlocfilehash: 004a623f0dfe251da9d452b53c2541e53339d965
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684256"
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

Il modello definisce due parametri:
- *Percorsofileorigine* è il percorso dell'archivio dell'origine dati, in cui è possibile ottenere un elenco dei contenitori. Nella maggior parte dei casi, il percorso è la cartella radice, che contiene più cartelle dei contenitori. Il valore predefinito per questo parametro è `/`.
- *Percorsofiledestinazione* è il percorso in cui verranno copiati i file nell'archivio di destinazione. Il valore predefinito per questo parametro è `/`.

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