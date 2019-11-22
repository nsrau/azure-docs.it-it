---
title: File di inclusione
description: File di inclusione
services: machine-learning
author: peterclu
ms.service: machine-learning
ms.author: peterlu
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 10/09/2019
ms.openlocfilehash: 1ee90e0c99234497b072bbee0b92d76129baea48
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73929677"
---
Una pipeline viene eseguita in una destinazione di calcolo, ossia una risorsa di calcolo collegata all'area di lavoro. Dopo aver creato una destinazione di calcolo, è possibile riusarla per le esecuzioni future.

1. Selezionare **Esegui** nella parte superiore dell'area di disegno per eseguire la pipeline.

1. Quando viene visualizzato il riquadro **Impostazioni**, selezionare **Select compute target** (Seleziona destinazione di calcolo).

    Se è già disponibile una destinazione di calcolo, è possibile selezionarla per eseguire questa pipeline.

    > [!NOTE]
    > La finestra di progettazione può eseguire esperimenti nelle destinazioni dell'ambiente di calcolo di Azure Machine Learning. Altre destinazioni di calcolo non vengono visualizzate.

1. Immettere un nome per la risorsa di calcolo.

1. Selezionare **Salva**.

    ![Configurare la destinazione di calcolo](./media/aml-ui-create-training-compute/set-compute.png)

1. Selezionare **Run** (Esegui).

1. Nella finestra di dialogo **Configura esecuzione della pipeline** selezionare **+ Nuovo esperimento** per **Esperimento**.

    > [!NOTE]
    > Gli esperimenti raggruppano esecuzioni di pipeline simili. Se una pipeline viene eseguita più volte, è possibile selezionare lo stesso esperimento per le esecuzioni successive.

    1. Immettere un nome descrittivo per il **Nome esperimento**.

    1. Selezionare **Run** (Esegui).
    
    È possibile visualizzare lo stato di esecuzione e i dettagli nella parte superiore destra dell'area di disegno.

    > [!NOTE]
    > La creazione di una risorsa di calcolo richiede circa cinque minuti. Dopo aver creato la risorsa, è possibile riusarla e saltare questo tempo di attesa per le esecuzioni future.
    >
    > La risorsa di calcolo si ridimensiona automaticamente a zero nodi quando è inattiva per risparmiare sui costi. Quando la si usa di nuovo dopo un breve intervallo di tempo, è possibile che si verifichi nuovamente un tempo di attesa di circa cinque minuti, mentre viene riaumentato il numero di nodi.
