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
ms.openlocfilehash: eb84dc1b5bf3f756e484ef27aaa998ab6b94cc51
ms.sourcegitcommit: a3a40ad60b8ecd8dbaf7f756091a419b1fe3208e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69891675"
---
Un esperimento viene eseguito su una destinazione di calcolo, una risorsa di calcolo collegata all'area di lavoro.  Dopo aver creato una destinazione di calcolo, è possibile riusarla per le esecuzioni future.

1. Selezionare **Run** (Esegui) nella parte inferiore per eseguire l'esperimento.

1. Quando viene visualizzata la finestra di dialogo **Setup Compute Targets** (Configura destinazioni di calcolo), se l'area di lavoro contiene già una risorsa di calcolo, è possibile selezionarla ora.  In alternativa, selezionare **Create new** (Crea nuovo).

    > [!NOTE]
    > L'interfaccia visiva può eseguire gli esperimenti solo nelle destinazioni dell'ambiente di calcolo di Machine Learning. Altre destinazioni di calcolo non verranno visualizzate.

1. Specificare un nome per la risorsa di calcolo.

1. Selezionare **Run** (Esegui).

    ![Configurare la destinazione di calcolo](./media/aml-ui-create-training-compute/set-compute.png)

    Verrà ora creata la risorsa di calcolo. Visualizzare lo stato nell'angolo in alto a destra dell'esperimento. 

    > [!NOTE]
    > La creazione di una risorsa di calcolo richiede circa 5 minuti. Dopo aver creato la risorsa, è possibile riusarla e saltare questo tempo di attesa per le esecuzioni future.
    >
    > La risorsa di calcolo verrà ridimensionata automaticamente a 0 nodi quando è inattiva per risparmiare sui costi.  Quando la si usa di nuovo dopo un breve intervallo di tempo, è possibile che si verifichi nuovamente un tempo di attesa di circa 5 minuti, mentre viene riaumentato il numero di nodi.
