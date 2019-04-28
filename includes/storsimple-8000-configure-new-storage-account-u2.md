---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: d6bad1ec584b28fee77bc654eba8a2d0c7b5df30
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60632687"
---
#### <a name="to-add-a-storage-account-credential-in-the-same-azure-subscription-as-the-storsimple-device-manager-service"></a>Per aggiungere una credenziale dell'account di archiviazione nella stessa sottoscrizione di Azure del servizio Gestione dispositivi StorSimple

1. Passare al servizio Gestione dispositivi StorSimple. Nella sezione **Configurazione** fare clic su **Credenziali dell'account di archiviazione**.

    ![Credenziali dell'account di archiviazione](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct1.png)

2. Nel pannello **Credenziali dell'account di archiviazione** fare clic su **+ Aggiungi**.

    ![Aggiungere una credenziale dell'account di archiviazione](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct2.png)

3. Nel pannello **Aggiungi credenziali dell'account di archiviazione** seguire questa procedura:

    1. Poiché si deve aggiungere una credenziale dell'account di archiviazione nella stessa sottoscrizione di Azure del servizio, assicurarsi che sia selezionato **Corrente**.

    2. Nell'elenco a discesa **Account di archiviazione** selezionare un account di archiviazione esistente.

    3. In base all'account di archiviazione selezionato, verrà visualizzata la **posizione** (disabilitata e non modificabile qui).

    4. Selezionare **Abilita modalità SSL** per creare un canale sicuro per la comunicazione di rete tra il dispositivo e il cloud. Disabilitare **Abilita SSL** solo se si opera in un cloud privato.

        ![Pannello per l'aggiunta delle credenziali dell'account di archiviazione](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct3.png)

    5. Fare clic su **Aggiungi** per avviare la creazione del processo per la credenziale dell'account di archiviazione. Quando la creazione della credenziale dell'account di archiviazione viene completata, si riceverà una notifica.

        ![Notifica di operazione riuscita per le credenziali dell'account di archiviazione](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct5.png)

La nuova credenziale dell'account di archiviazione creata verrà visualizzata nell'elenco **Credenziali dell'account di archiviazione**.

![Elenco di credenziali dell'account di archiviazione](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct6.png)

