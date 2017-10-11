---
title: Failover di StorSimple, ripristino di emergenza su un dispositivo StorSimple serie 8000 fisico | Microsoft Docs
description: Informazioni su come eseguire il failover del dispositivo StorSimple serie 8000 su un altro dispositivo fisico.
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
ms.date: 05/03/2017
ms.author: alkohli
ms.openlocfilehash: f3ac9545a341fc24ca12c9f2547805d6956cd98a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="fail-over-to-a-storsimple-8000-series-physical-device"></a>Failover su un dispositivo StorSimple serie 8000 fisico

## <a name="overview"></a>Panoramica

Questa esercitazione descrive i passaggi necessari per eseguire il failover di un dispositivo StorSimple serie 8000 fisico su un altro dispositivo fisico StorSimple in caso di emergenza. StorSimple usa la funzionalità di failover del dispositivo per migrare i dati da un dispositivo fisico di origine nel data center a un altro dispositivo fisico. Le indicazioni fornite in questa esercitazione si applicano ai dispositivi fisici StorSimple serie 8000 in cui sono installate le versioni software Update 3 e successive.

Per altre informazioni sul failover del dispositivo e su come usarlo per il ripristino di emergenza, passare a [Failover e ripristino di emergenza per dispositivi StorSimple serie 8000](storsimple-8000-device-failover-disaster-recovery.md).

Per eseguire il failover di un dispositivo fisico StorSimple su un'appliance cloud StorSimple, passare a [Eseguire il failover in un'appliance cloud StorSimple](storsimple-8000-device-failover-cloud-appliance.md). Per eseguire il failover di un dispositivo fisico su se stesso, passare a [Eseguire il failover sullo stesso dispositivo fisico StorSimple](storsimple-8000-device-failover-same-device.md).


## <a name="prerequisites"></a>Prerequisiti

- Assicurarsi di aver esaminato le considerazioni relative al failover dei dispositivi. Per altre informazioni, leggere [Considerazioni per il failover del dispositivo](storsimple-8000-device-failover-disaster-recovery.md).

- È necessario disporre di un dispositivo fisico StorSimple serie 8000 distribuito nel data center. Il dispositivo deve eseguire l'aggiornamento 3 o versione successiva di software. Per altre informazioni, vedere [Distribuire un dispositivo StorSimple locale](storsimple-8000-deployment-walkthrough-u2.md).


## <a name="steps-to-fail-over-to-a-physical-device"></a>Passaggi per il failover su un dispositivo fisico

Eseguire i passaggi seguenti per ripristinare il dispositivo su un dispositivo fisico di destinazione.

1. Verificare che il contenitore del volume di cui si desidera eseguire il failover sia associato alle snapshot cloud. Per altre informazioni, vedere l'articolo relativo all'[utilizzo del servizio Gestione dispositivi StorSimple per la creazione di backup](storsimple-8000-manage-backup-policies-u2.md).
2. Passare Gestione dispositivi StorSimple, quindi fare clic su **Dispositivi**. Nel pannello **Dispositivi**, visualizzare l'elenco di dispositivi connessi al servizio.
    ![Selezionare il dispositivo](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)
3. Selezionare e fare clic sul dispositivo di origine. Il dispositivo di origine presenta i contenitori dei volumi di cui effettuare il failover. Passare a **Impostazioni > Contenitori dei volumi**.
4. Selezionare un contenitore di volumi di cui si desidera eseguire il failover a un altro dispositivo. Fare clic sul contenitore di volumi per visualizzare l'elenco dei volumi presenti nel contenitore. Selezionare un volume e fare clic con il pulsante destro del mouse su **Porta offline** per portare il volume offline. Ripetere questo processo per tutti i volumi nel contenitore di volumi.
5. Ripetere il passaggio precedente per tutti i contenitori di volumi di cui si desidera eseguire il failover a un altro dispositivo.
6. Tornare al pannello **Dispositivi**. Nella barra dei comandi fare clic su **Failover**.
    ![Fare clic su failover](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev2.png)
    
7. Nel pannello **Failover** attenersi alla procedura seguente:
   
   1. Fare clic su **Origine**. Vengono visualizzati i contenitori dei volumi con volumi associati agli snapshot del cloud. Sono idonei al failover solo i contenitori visualizzati. Nell'elenco dei contenitori di volumi, selezionare i contenitori di volumi di cui si desidera eseguire il failover. **Vengono visualizzati solo i contenitori di volumi con gli snapshot del cloud e i volumi offline associati.**

       ![Selezionare l'origine](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev5.png)
   2. Fare clic su **Destinazione**. Per i contenitori dei volumi selezionati nel passaggio precedente, selezionare un dispositivo di destinazione dall'elenco a discesa dei dispositivi disponibili. Nell'elenco vengono visualizzati solo i dispositivi che hanno una capacità sufficiente a contenere i contenitori dei volumi di origine.

        ![Selezionare la destinazione](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev6.png)

   3. Infine, esaminare tutte le impostazioni di failover in **Riepilogo**. Dopo avere esaminato le impostazioni, selezionare la casella di controllo che indica che i volumi nei contenitori dei volumi selezionati sono offline. Fare clic su **OK**.

       ![Esaminare le impostazioni di failover](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev8.png)
  
8. StorSimple crea un processo di failover. Fare clic sulla notifica del processo per monitorare il processo di failover tramite il pannello **Processi**.

    Se nel contenitore del volume di cui è stato effettuato il failover sono presenti volumi locali, i singoli processi di ripristino per ogni volume locale (non per i volumi a livelli) sono visibili nel contenitore. Per il completamento di questi processi di ripristino può essere necessario molto tempo. È probabile che venga completato prima il processo di failover. Questi volumi presentano garanzie locali solo dopo il completamento dei processi di ripristino.

    ![Monitorare il processo di failover](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

9. Dopo il completamento del failover, andare al pannello **Dispositivi**.
   
   1. Selezionare il dispositivo utilizzato come dispositivo di destinazione per il processo di failover.

       ![Selezionare il dispositivo](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

   2. Andare al pannello **Contenitori dei volumi**. Dovrebbero essere elencati tutti i contenitori di volumi,  insieme ai volumi del dispositivo precedente.

       ![Visualizzare i contenitori dei volumi di destinazione](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev16.png)


## <a name="next-steps"></a>Passaggi successivi

* Dopo aver eseguito un failover, può essere necessario [disattivare o eliminare il dispositivo StorSimple](storsimple-8000-deactivate-and-delete-device.md).
* Per informazioni sull'uso del servizio Gestione dispositivi StorSimple, vedere [Use the StorSimple Device Manager service to administer your StorSimple device](storsimple-8000-manager-service-administration.md) (Usare il servizio Gestione dispositivi StorSimple per amministrare il dispositivo StorSimple).

