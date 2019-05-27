---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 02274bacb66a33ef54e07bc8113d7db46d4d5296
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66172183"
---
#### <a name="to-add-a-storsimple-backup-policy"></a>Per aggiungere un criterio di backup per StorSimple

1. Andare al dispositivo StorSimple e fare clic su **Criteri di backup**.

2. Nel pannello **Criteri di backup** fare clic su **Aggiungi criteri** dalla barra dei comandi.
   
    ![Aggiungi un criterio di backup](./media/storsimple-8000-add-backup-policy-u2/addbupol1.png)

3. Nel pannello **Creare i criteri di backup** seguire questa procedura:
   
   1. **Seleziona dispositivo** viene popolato automaticamente in base al dispositivo selezionato.
   
   2. Specificare un **nome** per i criteri di backup che contenga tra i 3 e i 150 caratteri. Dopo essere stati creati, i criteri non possono essere rinominati.
       
   3. Per assegnare volumi ai criteri di backup, selezionare **Aggiungi volumi** e nell'elenco tabulare dei volumi fare clic sulle caselle di controllo per assegnare uno o più volumi ai criteri di backup.

       ![Aggiungere un criterio di backup](./media/storsimple-8000-add-backup-policy-u2/addbupol2.png)

   4. Per definire una pianificazione per i criteri di backup, fare clic su **Prima pianificazione** e quindi modificare i parametri seguenti:

       ![Aggiungi un criterio di backup](./media/storsimple-8000-add-backup-policy-u2/addbupol3.png)

       1. Per **Tipo di snapshot** selezionare **Cloud** o **Locale**.

       2. Indicare la frequenza dei backup specificando un numero e quindi scegliendo **Giorni** o **Settimane** dall'elenco a discesa.

       3. Immettere una pianificazione di conservazione.

       4. Immettere una data e ora di inizio per il criterio di backup.

       5. Fare clic su **OK** per definire la pianificazione.

   5. Fare clic su **Crea** per creare criteri di backup.

       ![Aggiungi un criterio di backup](./media/storsimple-8000-add-backup-policy-u2/addbupol4.png)
   
   6. Dopo la creazione dei criteri di backup si riceve una notifica. I criteri appena aggiunti vengono visualizzati nella vista tabulare del pannello **Criteri di backup**.

       ![Aggiungi un criterio di backup](./media/storsimple-8000-add-backup-policy-u2/addbupol7.png)

