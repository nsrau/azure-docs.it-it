---
title: 'Esporta dati: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Usare il modulo Export data in Azure Machine Learning Designer per salvare i risultati e i dati intermedi al di fuori della Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/28/2020
ms.openlocfilehash: c6e3d56958168cd279c98a4ba4c021c2362c2694
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421312"
---
# <a name="export-data-module"></a>Modulo Export Data

Questo articolo descrive un modulo in Azure Machine Learning Designer.

Usare questo modulo per salvare i risultati, i dati intermedi e i dati di lavoro dalle pipeline nelle destinazioni di archiviazione cloud. 

Questo modulo supporta l'esportazione dei dati nei servizi cloud data seguenti:

- Contenitore BLOB di Azure
- Condivisione file di Azure
- Azure Data Lake Storage Gen1
- Azure Data Lake Storage Gen2
- Database SQL di Azure

Prima di esportare i dati, è necessario prima registrare un archivio dati nell'area di lavoro Azure Machine Learning. Per altre informazioni, vedere [accedere ai dati nei servizi di archiviazione di Azure](../how-to-access-data.md).

## <a name="how-to-configure-export-data"></a>Come configurare i dati di esportazione

1. Aggiungere il modulo **Export Data** alla pipeline nella finestra di progettazione. È possibile trovare questo modulo nella categoria **input e output** .

1. Connettere **Esporta dati** al modulo che contiene i dati che si desidera esportare.

1. Selezionare **Esporta dati** per aprire il riquadro **Proprietà** .

1. Per **archivio dati** selezionare un archivio dati esistente dall'elenco a discesa. È anche possibile creare un nuovo archivio dati. Per informazioni, vedere [accesso ai dati nei servizi di archiviazione di Azure](../how-to-access-data.md).

    > [!NOTE]
    > L'esportazione di dati di un determinato tipo di dati in una colonna del database SQL specificata come un altro tipo di dati non è supportata. Non è necessario che la tabella di destinazione esista per prima.

1. La casella di controllo **Rigenera output** , decide se eseguire il modulo per rigenerare l'output in fase di esecuzione. 

    Per impostazione predefinita, è deselezionata, ovvero se il modulo è stato eseguito con gli stessi parametri in precedenza, il sistema riutilizzerà l'output dell'ultima esecuzione per ridurre il tempo di esecuzione. 

    Se questa opzione è selezionata, il sistema eseguirà di nuovo il modulo per rigenerare l'output.

1. Definire il percorso nell'archivio dati in cui si trovano i dati. Il percorso è un percorso relativo. I percorsi vuoti o i percorsi URL non sono consentiti.


1. Per **formato file** selezionare il formato in cui archiviare i dati.
 
1. Inviare la pipeline.

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 
