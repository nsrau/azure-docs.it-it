---
title: Aggiornare un'entità composita-LUIS
description: Aggiornare l'entità composita all'entità Machine-learned con il processo di aggiornamento nel portale LUIS.
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 9bbb03a2009bdcb6e2fa5d20aefadd4c7c99f025
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599425"
---
# <a name="upgrade-composite-entity-to-machine-learned-entity"></a>Aggiornare un'entità composita a un'entità appresa dal computer

Aggiornare l'entità composita all'entità Machine-learned per compilare un'entità che riceve stime più complete con una migliore decomposizione per il debug dell'entità.

## <a name="current-version-model-restrictions"></a>Restrizioni del modello di versione corrente

Il processo di aggiornamento crea entità apprese dal computer, basate sulle entità composite esistenti trovate nell'app, in una nuova versione dell'app. Sono inclusi gli elementi figlio e i ruoli dell'entità composita. Il processo passa anche le etichette nelle espressioni di esempio per usare la nuova entità.

## <a name="upgrade-process"></a>Processo di aggiornamento

Il processo di aggiornamento:
* Crea una nuova entità appresa dal computer per ogni entità composita.
* Entità figlio:
    * Se l'entità figlio viene utilizzata solo nell'entità composita, verrà aggiunta solo all'entità appresa dal computer.
    * Se l'entità figlio viene utilizzata in composito _e_ come entità separata (etichettata in espressioni di esempio), verrà aggiunta alla versione come entità e come sottoentità alla nuova entità appresa dal computer.
    * Se l'entità figlio utilizza un ruolo, ogni ruolo verrà convertito in una sottoentità con lo stesso nome.
    * Se l'entità figlio è un'entità non appresa dal computer (espressione regolare, entità di elenco o entità precompilata), viene creata una nuova sottoentità con lo stesso nome e la nuova sottoentità ha una funzionalità che usa l'entità non appresa dal computer con la funzionalità richiesta aggiunta.
* I nomi vengono conservati, ma devono essere univoci a livello di sottoentità/di pari livello. Vedere [limiti di denominazione univoci](luis-boundaries.md#name-uniqueness).
* Le etichette negli enunciati di esempio vengono passate a una nuova entità appresa dal computer con sottoentità.

Usare il grafico seguente per comprendere le modifiche apportate al modello:

|Oggetto precedente|Nuovo oggetto|Note|
|--|--|--|
|Entità composita|Entità Machine-learned con struttura|Entrambi gli oggetti sono oggetti padre.|
|L'entità figlio composita è un' **entità semplice**|sottoentità|Entrambi gli oggetti sono oggetti figlio.|
|L'entità figlio composita è un' **entità predefinita** , ad esempio Number|la sottoentità con il nome di un'entità predefinita, ad esempio Number, e SubEntità, presenta una _funzionalità_ di entità numero precompilata con l'opzione constraint impostata su _true_.|sottoentità contiene funzionalità con vincolo a livello di sottoentità.|
|L'entità figlio composita è un' **entità predefinita** , ad esempio Number, e l'entità precompilata ha un **ruolo**|la sottoentità con il nome del ruolo e la sottoentità hanno una funzionalità di entità numero precompilata con l'opzione constraint impostata su true.|sottoentità contiene funzionalità con vincolo a livello di sottoentità.|
|Ruolo|sottoentità|Il nome del ruolo diventa il nome della sottoentità. La sottoentità è un discendente diretto dell'entità appresa dal computer.|

## <a name="begin-upgrade-process"></a>Inizia processo di aggiornamento

Prima dell'aggiornamento, assicurarsi di:

* Modificare le versioni se non si dispone della versione corretta per l'aggiornamento


1. Avviare il processo di aggiornamento dalla notifica oppure attendere fino alla successiva richiesta pianificata.

    > [!div class="mx-imgBorder"]
    > ![Inizia aggiornamento da notifiche](./media/update-composite-entity/notification-begin-update.png)

1. Nella finestra popup selezionare **Aggiorna ora**.

1. Esaminare le **operazioni che si verificano quando si aggiornano** le informazioni, quindi selezionare **continua**.

1. Selezionare le entità composite dall'elenco da aggiornare e quindi selezionare **continue (continua**).

1. È possibile spostare le modifiche senza training dalla versione corrente nella versione aggiornata selezionando la casella di controllo.

1. Selezionare **continua** per avviare il processo di aggiornamento.

1. L'indicatore di stato indica lo stato del processo di aggiornamento.

1. Al termine del processo, si usa una nuova versione con training con le nuove entità acquisite dal computer. Selezionare **prova le nuove entità** per visualizzare la nuova entità.

    Se l'aggiornamento o il training non è riuscito per la nuova entità, una notifica fornisce ulteriori informazioni.

1. Nella pagina elenco entità le nuove entità sono contrassegnate con **nuovo** accanto al nome del tipo.

## <a name="next-steps"></a>Passaggi successivi

* [Autori e collaboratori](luis-how-to-collaborate.md)