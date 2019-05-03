---
title: File di inclusione
description: File di inclusione
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 05/06/2019
ms.openlocfilehash: cf35651f7dd839e8792029851b9bfe278036624c
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028516"
---
Un esperimento viene eseguito su una destinazione di calcolo, una risorsa di calcolo che è collegata all'area di lavoro.  Dopo aver creato una destinazione di calcolo, è possibile riutilizzarlo per le esecuzioni future.

1. Selezionare **eseguiti** in basso per eseguire l'esperimento.

     ![Eseguire esperimento](./media/aml-ui-create-training-compute/run-experiment.png)

1. Quando la **destinazioni di calcolo il programma di installazione** finestra di dialogo viene visualizzata, se l'area di lavoro dispone già di una risorsa di calcolo, è possibile selezionarli ora.  In caso contrario, selezionare **Crea nuovo**.

    > [!NOTE]
    > L'interfaccia visiva può solo eseguire esperimenti in destinazioni di calcolo di Machine Learning. Altre destinazioni di calcolo non verranno visualizzati.

1. Specificare un nome per la risorsa di calcolo.

1. Selezionare **Run** (Esegui).

    ![Destinazione di calcolo il programma di installazione](./media/aml-ui-create-training-compute/set-compute.png)

    Verrà ora creata la risorsa di calcolo. Visualizzare lo stato nell'angolo superiore destro dell'esperimento. 

    > [!NOTE]
    > Richiede circa 5 minuti per creare una risorsa di calcolo. Dopo aver creata la risorsa, è possibile riutilizzarlo e ignorare il tempo di attesa per le esecuzioni future.
    >
    > La risorsa di calcolo verrà ridimensionato automaticamente in specificando 0 nodi quando è inattivo per risparmiare sui costi.  Quando si utilizza questo tentativo successivo, anche in questo caso durante potrebbe verificarsi circa 5 minuti di tempo di attesa aumenta back.
