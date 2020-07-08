---
title: 'Esporta dati: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Export Data (Esporta dati) in Azure Machine Learning per salvare i risultati, i dati intermedi e i dati di lavoro dalle pipeline in destinazioni di archiviazione cloud all'esterno di Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 807771fd4018c9666f059c965370ebc36d0105df
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79456302"
---
# <a name="export-data-module"></a>Modulo Export Data

Questo articolo descrive un modulo disponibile nella finestra di progettazione di Azure Machine Learning (anteprima).

Usare questo modulo per salvare i risultati, i dati intermedi e i dati di lavoro dalle pipeline nelle destinazioni di archiviazione cloud. 

Questo modulo supporta l'esportazione dei dati nei servizi cloud data seguenti:

- Contenitore BLOB di Azure
- Condivisione file di Azure
- Azure Data Lake
- Azure Data Lake Gen2

Prima di esportare i dati, è necessario prima registrare un archivio dati nell'area di lavoro Azure Machine Learning. Per altre informazioni, vedere [accedere ai dati nei servizi di archiviazione di Azure](../how-to-access-data.md).

## <a name="how-to-configure-export-data"></a>Come configurare i dati di esportazione

1. Aggiungere il modulo **Export Data** alla pipeline nella finestra di progettazione. È possibile trovare questo modulo nella categoria **input e output** .

1. Connettere **Esporta dati** al modulo che contiene i dati che si desidera esportare.

1. Selezionare **Esporta dati** per aprire il riquadro **Proprietà** .

1. Per **archivio dati**selezionare un archivio dati esistente dall'elenco a discesa. È anche possibile creare un nuovo archivio dati. Per informazioni, vedere [accesso ai dati nei servizi di archiviazione di Azure](../how-to-access-data.md).

1. La casella di controllo **Rigenera output**, decide se eseguire il modulo per rigenerare l'output in fase di esecuzione. 

    Per impostazione predefinita, è deselezionata, ovvero se il modulo è stato eseguito con gli stessi parametri in precedenza, il sistema riutilizzerà l'output dell'ultima esecuzione per ridurre il tempo di esecuzione. 

    Se questa opzione è selezionata, il sistema eseguirà di nuovo il modulo per rigenerare l'output.

1. Definire il percorso nell'archivio dati in cui si trovano i dati. Il percorso è un percorso relativo. I percorsi vuoti o i percorsi URL non sono consentiti.


1. Per **formato file**selezionare il formato in cui archiviare i dati.
 
1. Inviare la pipeline.

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 
