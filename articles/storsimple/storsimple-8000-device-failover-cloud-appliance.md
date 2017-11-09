---
title: Failover di StorSimple, ripristino di emergenza in un'appliance cloud StorSimple | Microsoft Docs
description: Informazioni su come eseguire il failover del dispositivo StorSimple serie 8000 su un'appliance cloud.
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
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: ec8bebf2854e84a37e84b45564e80fc20b63d8d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="fail-over-to-your-storsimple-cloud-appliance"></a>Failover nell'appliance cloud StorSimple

## <a name="overview"></a>Panoramica

Questa esercitazione descrive i passaggi necessari per eseguire il failover di un dispositivo StorSimple serie 8000 fisico su un'appliance cloud StorSimple in caso di emergenza. StorSimple usa la funzionalità di failover del dispositivo per eseguire la migrazione dei dati da un dispositivo fisico di origine nel data center a un'appliance cloud eseguita in Azure. Le indicazioni fornite in questa esercitazione si applicano ai dispositivi fisici StorSimple serie 8000 e alle applicazioni cloud in cui sono installate le versioni software Update 3 e successive.

Per altre informazioni sul failover del dispositivo e su come usarlo per il ripristino di emergenza, passare a [Failover e ripristino di emergenza per dispositivi StorSimple serie 8000](storsimple-8000-device-failover-disaster-recovery.md).

Per effettuare il failover di un dispositivo fisico StorSimple su un altro dispositivo fisico, vedere [Fail over to a StorSimple physical device](storsimple-8000-device-failover-physical-device.md) (Failover su un dispositivo fisico StorSimple). Per eseguire il failover di un dispositivo su se stesso, passare a [Eseguire il failover sullo stesso dispositivo fisico StorSimple](storsimple-8000-device-failover-same-device.md).

## <a name="prerequisites"></a>Prerequisiti

- Assicurarsi di aver esaminato le considerazioni relative al failover dei dispositivi. Per altre informazioni, vedere [Common considerations for device failover](storsimple-8000-device-failover-disaster-recovery.md) (Considerazioni comuni per il failover dei dispositivi).

- Prima di eseguire questa procedura, è necessario disporre di un'appliance cloud StorSimple creata e configurata. Se è in l'aggiornamento 3 o una versione successiva del software, è consigliabile usare un'appliance cloud 8020 per il ripristino di emergenza. Il modello 8020 dispone di 64 TB e usa l'Archiviazione Premium. Per altre informazioni, vedere [Distribuire e gestire un'appliance cloud StorSimple](storsimple-8000-cloud-appliance-u2.md).

## <a name="steps-to-fail-over-to-a-cloud-appliance"></a>Procedura di failover in un'appliance cloud

Eseguire i passaggi seguenti per ripristinare il dispositivo su un'appliance cloud StorSimple di destinazione.

1.  Verificare che il contenitore del volume di cui si desidera eseguire il failover sia associato alle snapshot cloud. Per altre informazioni, vedere [Use StorSimple Device Manager service to create backups](storsimple-8000-manage-backup-policies-u2.md) (Usare il servizio Gestione dispositivi StorSimple per creare backup).
2. Passare al servizio Gestione dispositivi StorSimple e fare clic su **Dispositivi**. Nel pannello **Dispositivi**, visualizzare l'elenco di dispositivi connessi al servizio.
    ![Selezionare il dispositivo](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev1.png)
3. Selezionare e fare clic sul dispositivo di origine. Il dispositivo di origine presenta i contenitori dei volumi di cui effettuare il failover. Passare a **Impostazioni > Contenitori dei volumi**.

    ![Selezionare il dispositivo](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev2.png)
    
4. Selezionare un contenitore di volumi di cui si desidera eseguire il failover a un altro dispositivo. Fare clic sul contenitore di volumi per visualizzare l'elenco dei volumi presenti nel contenitore. Selezionare un volume e fare clic con il pulsante destro del mouse su **Porta offline** per portare il volume offline.

    ![Selezionare il dispositivo](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev5.png)

5. Ripetere questo processo per tutti i volumi nel contenitore di volumi.

     ![Selezionare il dispositivo](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev7.png)

6. Ripetere il passaggio precedente per tutti i contenitori di volumi di cui si desidera eseguire il failover a un altro dispositivo.

7. Tornare al pannello **Dispositivi**. Nella barra dei comandi fare clic su **Failover**.

    ![Fare clic su Failover](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev8.png)
8. Nel pannello **Failover** attenersi alla procedura seguente:
   
    1. Fare clic su **Origine**. Selezionare i contenitori dei volumi per il failover. **Vengono visualizzati solo i contenitori di volumi con gli snapshot del cloud e i volumi offline associati.**
        ![Select source](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev11.png) (Seleziona origine)
    2. Fare clic su **Destinazione**. Nell'elenco a discesa dei dispositivi disponibili selezionare l'appliance cloud di destinazione. **Nell'elenco vengono visualizzati solo i dispositivi che hanno una capacità sufficiente a contenere i contenitori dei volumi di origine.**

        ![Selezionare la destinazione](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev12.png)

    3. Esaminare le impostazioni di failover in **Riepilogo** e selezionare la casella di controllo che indica che i volumi nei contenitori dei volumi selezionati sono offline. 

        ![Esaminare le impostazioni di failover](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev13.png)

9. Viene creato un processo di failover. Per monitorare il processo di failover, fare clic sulla notifica del processo.

    ![Monitorare il processo di failover](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

10. Dopo il completamento del failover, tornare al pannello **Dispositivi**.

    1. Selezionare il dispositivo usato come destinazione per il failover.

       ![Selezionare il dispositivo](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

    2. Fare clic su **Contenitori di volume**. Dovrebbero essere elencati tutti i contenitori di volumi,  insieme ai volumi del dispositivo precedente.

       Se il contenitore del volume di cui è stato eseguito il failover ha volumi aggiunti in locale, verrà eseguito il failover di questi volumi come volumi a livelli. I volumi aggiunti in locale non sono attualmente supportati in un'appliance cloud StorSimple.

       ![Visualizzare i contenitori dei volumi di destinazione](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev17.png)


## <a name="next-steps"></a>Passaggi successivi

* Dopo aver eseguito un failover, può essere necessario [disattivare o eliminare il dispositivo StorSimple](storsimple-8000-deactivate-and-delete-device.md).

* Per informazioni sull'uso del servizio Gestione dispositivi StorSimple, vedere [Use the StorSimple Device Manager service to administer your StorSimple device](storsimple-8000-manager-service-administration.md) (Usare il servizio Gestione dispositivi StorSimple per amministrare il dispositivo StorSimple).

