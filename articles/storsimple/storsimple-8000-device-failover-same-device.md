---
title: Failover e ripristino di emergenza di StorSimple per dispositivi serie 8000| Microsoft Docs
description: Informazioni su come effettuare il failover di un dispositivo StorSimple sullo stesso dispositivo.
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2017
ms.author: alkohli
ms.openlocfilehash: acc8929dc3476e9590e8e4d9526b38b7c0719570
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="fail-over-your-storsimple-physical-device-to-same-device"></a>Effettuare il failover di un dispositivo fisico StorSimple sullo stesso dispositivo

## <a name="overview"></a>Panoramica

Questa esercitazione descrive i passaggi necessari per effettuare il failover di un dispositivo fisico StorSimple serie 8000 sul dispositivo stesso in caso di emergenza. StorSimple usa la funzionalità di failover del dispositivo per eseguire la migrazione dei dati da un dispositivo fisico di origine nel data center a un altro dispositivo fisico. Le indicazioni fornite in questa esercitazione si applicano ai dispositivi fisici StorSimple serie 8000 in cui sono installate le versioni software Update 3 e successive.

Per altre informazioni sul failover dei dispositivi e su come viene usato per il ripristino di emergenza, vedere [Failover and disaster recovery for StorSimple 8000 series devices](storsimple-8000-device-failover-disaster-recovery.md) (Failover e ripristino di emergenza per dispositivi StorSimple serie 8000).

Per effettuare il failover di un dispositivo fisico su un altro dispositivo fisico, vedere [Fail over to the same StorSimple physical device](storsimple-8000-device-failover-physical-device.md) (Failover sullo stesso dispositivo fisico StorSimple). Per effettuare il failover di un dispositivo fisico StorSimple su un'appliance cloud StorSimple, vedere [Fail over to a StorSimple Cloud Appliance](storsimple-8000-device-failover-cloud-appliance.md) (Failover su un'appliance cloud StorSimple).


## <a name="prerequisites"></a>prerequisiti

- Assicurarsi di aver esaminato le considerazioni relative al failover dei dispositivi. Per altre informazioni, vedere [Common considerations for device failover](storsimple-8000-device-failover-disaster-recovery.md) (Considerazioni comuni per il failover dei dispositivi).


## <a name="steps-to-fail-over-to-the-same-device"></a>Procedura per il failover sullo stesso dispositivo

Per effettuare il failover sullo stesso dispositivo, seguire questa procedura.

1. Acquisire snapshot nel cloud di tutti i volumi nel dispositivo. Per altre informazioni, vedere [Use StorSimple Device Manager service to create backups](storsimple-8000-manage-backup-policies-u2.md) (Usare il servizio Gestione dispositivi StorSimple per creare backup).
2. Ripristinare le impostazioni predefinite del dispositivo. Seguire le istruzioni dettagliate in [come ripristinare le impostazioni predefinite di un dispositivo StorSimple](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).
3. Passare al servizio Gestione dispositivi StorSimple e selezionare **Dispositivi**. Nel pannello **Dispositivi** il dispositivo precedente dovrebbe essere visualizzato come **Offline**.

    ![Dispositivo di origine offline](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev2.png)

4. Configurare il dispositivo e registrarlo di nuovo nel servizio Gestione dispositivi StorSimple. Il dispositivo appena registrato verrà visualizzato come **Pronto per la configurazione**. Il nuovo dispositivo avrà lo stesso nome del dispositivo precedente, ma con l'aggiunta di un valore numerico per indicare il ripristino delle impostazioni predefinite e la nuova registrazione.

    ![Dispositivo appena registrato pronto per la configurazione](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev3.png)
5. Completare la configurazione del nuovo dispositivo. Per altre informazioni, vedere [Passaggio 4: Completare la configurazione minima del dispositivo](storsimple-8000-deployment-walkthrough-u2.md#step-4-complete-minimum-device-setup). Nel pannello **Dispositivi** lo stato del dispositivo passa a **Online**.

   > [!IMPORTANT]
   > **Completare prima la configurazione minima, altrimenti il ripristino di emergenza potrebbe non riuscire.**

    ![Dispositivo appena registrato online](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev7.png)

6. Selezionare il dispositivo precedente (stato offline) e dalla barra dei comandi fare clic su **Failover**. Nel pannello **Failover** selezionare il dispositivo precedente come origine e specificare come dispositivo di destinazione il dispositivo appena registrato.

    ![Riepilogo del failover](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev11.png)

    Per istruzioni dettagliate, fare riferimento a [Failover su un altro dispositivo fisico](#fail-over-to-another-physical-device).

7. Verrà creato un processo di ripristino del dispositivo che è possibile monitorare dal pannello **Processi**.

8. Al termine del processo, accedere al nuovo dispositivo e passare al pannello **Contenitori dei volumi**. Verificare che sia stata eseguita la migrazione di tutti i contenitori dei volumi dal dispositivo precedente al nuovo dispositivo.

   ![Migrazione dei contenitori dei volumi eseguita](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev13.png)

9. Al termine del failover, è possibile disattivare ed eliminare il dispositivo precedente dal portale. Selezionare il dispositivo precedente (offline), fare clic con il pulsante destro del mouse e quindi selezionare **Disattiva**. Dopo la disattivazione, lo stato del dispositivo verrà aggiornato.

     ![Dispositivo di origine disattivato](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev14.png)

10. Selezionare il dispositivo disattivato, fare clic con il pulsante destro del mouse e quindi selezionare **Elimina**. Il dispositivo verrà eliminato dall'elenco di dispositivi.

    ![Dispositivo di origine eliminato](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev15.png)



## <a name="next-steps"></a>Passaggi successivi

* Dopo aver eseguito un failover, può essere necessario [disattivare o eliminare il dispositivo StorSimple](storsimple-8000-deactivate-and-delete-device.md).
* Per informazioni sull'uso del servizio Gestione dispositivi StorSimple, vedere [Use the StorSimple Device Manager service to administer your StorSimple device](storsimple-8000-manager-service-administration.md) (Usare il servizio Gestione dispositivi StorSimple per amministrare il dispositivo StorSimple).

