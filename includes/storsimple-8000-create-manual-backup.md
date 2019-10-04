---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 4fc92931979aa367bdead435c3d6fd758d66a397
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180222"
---
#### <a name="to-create-a-manual-backup"></a>Per creare un backup manuale

1. Passare al servizio Gestione dispositivi StorSimple, quindi fare clic su **Dispositivi**. Nell'elenco tabulare di dispositivi selezionare il dispositivo. Passare a **Impostazioni > Gestisci > Criteri di backup**.

2. Il pannello **Criteri di backup** elenca tutti i criteri di backup in formato tabulare, tra cui il criterio per il volume di cui si vuole eseguire il backup. Selezionare il criterio associato al volume di cui si vuole eseguire il backup e fare clic con il pulsante destro del mouse per richiamare il menu di scelta rapida. Nell'elenco a discesa selezionare **Esegui backup**.

    ![Creare il backup manuale](./media/storsimple-8000-create-manual-backup/createmanualbu1.png)

3. Nel pannello **Esegui backup** seguire questa procedura:

    1. Scegliere il **tipo di snapshot** appropriato dal menu a discesa: snapshot **locale** o **cloud**. Selezionare lo snapshot locale per backup o ripristini rapidi e lo snapshot cloud per la resilienza dei dati.

        ![Creare il backup manuale](./media/storsimple-8000-create-manual-backup/createmanualbu2.png)

    2. Fare clic su **OK** per avviare un processo di creazione di snapshot. Una volta completata la creazione del processo, verrà visualizzata una notifica nella parte superiore della pagina.

        ![Creare il backup manuale](./media/storsimple-8000-create-manual-backup/createmanualbu4.png)

    3. Per monitorare il processo, fare clic sulla notifica. Verrà aperto il pannello **Processi** in cui è possibile visualizzare lo stato del processo.


5. Una volta completato il processo di backup, andare alla scheda **Catalogo di backup** .

6. Impostare le selezioni filtro per il dispositivo appropriato, il criterio di backup e l’intervallo di tempo. Il backup dovrebbe comparire nell'elenco dei set di backup che viene visualizzato nel catalogo.

