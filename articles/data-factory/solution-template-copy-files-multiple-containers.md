---
title: Copiare file da più contenitori con Azure Data Factory | Microsoft Docs
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
ms.openlocfilehash: aa5f32594c295ab6a8e60af8359370f64f75a72d
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55966559"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>Copiare file da più contenitori con Azure Data Factory

Il modello di soluzione descritto in questo articolo consente di copiare i file da più file, contenitori o bucket tra gli archivi di file. Ad esempio, forse si desidera eseguire la migrazione di data lake da AWS S3 ad Azure Data Lake Storage. Oppure si desidera replicare tutti gli elementi da un account di archiviazione BLOB di Azure a un altro account di archiviazione BLOB di Azure. Questo modello è progettato per tali casi d'uso.

Se si desidera copiare i file da un singolo contenitore o bucket, è preferibile usare lo **strumento Copia dati** per creare una pipeline con un'unica attività di copia. Per questo semplice caso d'uso, questo modello non è necessario.

## <a name="about-this-solution-template"></a>Informazioni sul modello di soluzione

Questo modello enumera i contenitori dall'archiviazione di origine e quindi copia ciascuno dei contenitori dall'archiviazione di origine nell'archivio di destinazione. 

Il modello contiene tre attività:
-   Un'attività **GetMetadata** per analizzare l'archiviazione di origine e ottenere l'elenco dei contenitori.
-   Un'attività **ForEach** per ottenere l'elenco dei contenitori dalle attività **GetMetadata** e quindi eseguire l'iterazione sull'elenco e passare ciascun contenitore all'attività di copia.
-   Un'attività **Copia** per copiare ciascun contenitore dall'archiviazione di origine nell'archivio di destinazione.

Il modello definisce due parametri:
-   Il parametro *SourceFilePath* rappresenta il percorso dell'archiviazione dei dati di origine, in cui è possibile ottenere un elenco di contenitori o bucket. Nella maggior parte dei casi, il percorso è la cartella radice, che contiene più cartelle dei contenitori. Il valore predefinito per questo parametro è `/`.
-   Il parametro *DestinationFilePath* è il percorso in cui verranno copiati i file nell'archivio di destinazione. Il valore predefinito per questo parametro è `/`.

## <a name="how-to-use-this-solution-template"></a>Come usare questo modello di soluzione

1. Andare al modello **Copiare più contenitori di file tra gli archivi di file** e creare una **nuova connessione** all'archivio di origine. L'archivio di origine è il luogo in cui si desidera copiare i file da più contenitori o bucket.

    ![Creare una nuova connessione all'origine](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. Creare una **nuova connessione** all'archivio di destinazione.

    ![Creare una nuova connessione alla destinazione](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. Fare clic su **Usa questo modello**.

    ![Usa questo modello](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. Nel pannello verrà visualizzata la pipeline disponibile, come illustrato nell'esempio seguente:

    ![Mostra la pipeline](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. Fare clic su **Debug**, immettere i **Parametri** e fare clic su **Fine**.

    ![Eseguire la pipeline](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. Esaminare il risultato.

    ![Esaminare il risultato](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione al servizio Azure Data Factory](introduction.md)
