---
title: Disattivare ed eliminare un dispositivo StorSimple serie 8000 | Microsoft Docs
description: Viene descritto come rimuovere un dispositivo StorSimple dal servizio disattivandolo e poi eliminandolo.
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
ms.date: 05/23/2017
ms.author: alkohli
ms.openlocfilehash: 3c00867a29cf8343a57e74e2aabe3971ae6837af
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="deactivate-and-delete-a-storsimple-device"></a>Disattivare ed eliminare un dispositivo StorSimple

## <a name="overview"></a>Panoramica

In questo articolo viene descritto come disattivare ed eliminare un dispositivo StorSimple connesso a un servizio Gestione dispositivi StorSimple. Le indicazioni fornite in questo articolo si applicano solo ai dispositivi StorSimple serie 8000, incluse le appliance cloud StorSimple. Se si usa un array virtuale StorSimple, passare a [Disattivare ed eliminare un array virtuale StorSimple](storsimple-virtual-array-deactivate-and-delete-device.md).

Disattivando i server si interrompe la connessione tra il dispositivo e il servizio Gestione dispositivi StorSimple corrispondente. È possibile mettere fuori servizio un dispositivo StorSimple, ad esempio se si sta sostituendo o aggiornando il dispositivo o se non si usa più StorSimple. In questo caso, è necessario disattivare il dispositivo prima di eliminarlo.

Quando si disattiva un dispositivo, tutti i dati archiviati localmente nel dispositivo non saranno più accessibili. Possono essere recuperati solo i dati associati al dispositivo che sono stati archiviati nel cloud.

> [!WARNING]
> La disattivazione è un'operazione permanente e non può essere annullata. Un dispositivo disattivato non può essere registrato con il servizio Gestione dispositivi StorSimple a meno che non vengano ripristinate le impostazioni di fabbrica.
>
> Il processo di ripristino delle impostazioni di fabbrica elimina tutti i dati archiviati localmente sul dispositivo. Pertanto, è necessario eseguire uno snapshot nel cloud di tutti i dati prima di disattivare un dispositivo. In questo modo sarà possibile recuperare tutti i dati in una fase successiva.

Dopo aver letto questa esercitazione, si sarà in grado di:

* Disattivare un dispositivo ed eliminare i dati.
* Disattivare un dispositivo e conservare i dati.

> [!NOTE]
> Prima di disattivare un dispositivo fisico o un'appliance cloud StorSimple, arrestare o eliminare i client e gli host che dipendono da tale dispositivo.


## <a name="deactivate-and-delete-data"></a>Disattivare ed eliminare i dati

Se si vuole eliminare completamente il dispositivo senza conservarne i dati, seguire questa procedura.

#### <a name="to-deactivate-the-device-and-delete-the-data"></a>Per disattivare il dispositivo ed eliminare i dati

1. Prima di disattivare un dispositivo, è necessario eliminare i contenitori del volume (e i volumi) associati al dispositivo. È possibile eliminare i contenitori dei volumi solo dopo aver eliminato i backup associati.

    > [!NOTE]
    > Prima di disattivare un dispositivo fisico StorSimple o un’appliance cloud, assicurarsi che i dati del contenitore di volumi eliminato vengano effettivamente eliminati dal dispositivo. È possibile monitorare i grafici relativi all'utilizzo del cloud, e nel momento in cui viene visualizzato che l'utilizzo del cloud è in diminuzione a causa dei backup che sono stati eliminati, allora è possibile disattivare il dispositivo. Se si disattiva il dispositivo prima che si verifichi questo calo, i dati rimangono bloccati nell'account di archiviazione e ne conseguono addebiti.

2. Disattivare il dispositivo come segue:
   
   1. Passare al servizio Gestione dispositivi StorSimple e fare clic su **Dispositivi**. Nel pannello **Dispositivi** selezionare il dispositivo che si desidera disattivare, fare clic con il tasto destro del mouse e quindi fare clic su **Disattiva**.

        ![Disattivare il dispositivo StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. Nel pannello **Disattiva** digitare il nome del dispositivo per confermare e quindi fare clic su **Disattiva**. Si avvia il processo di disattivazione il cui completamento richiede alcuni minuti.

        ![Disattivare il dispositivo StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)

3. Dopo la disattivazione, è possibile eliminare completamente il dispositivo. L’eliminazione di un dispositivo lo rimuove dall'elenco dei dispositivi connessi al servizio. Il servizio quindi non può più gestire il dispositivo eliminato. Completare la procedura seguente per eliminare il dispositivo:
   
   1. Passare al servizio Gestione dispositivi StorSimple e fare clic su **Dispositivi**. Nel pannello **Dispositivi** selezionare il dispositivo disattivato che si desidera eliminare, fare clic con il tasto destro del mouse e quindi fare clic su **Elimina**.

        ![Disattivare il dispositivo StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. Nel pannello **Elimina** digitare il nome del dispositivo per confermare e quindi fare clic su **Elimina**. Il processo di eliminazione richiede alcuni minuti.

        ![Disattivare il dispositivo StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. Si riceverà una notifica una volta completata l'eliminazione. Viene inoltre aggiornato l'elenco dei dispositivi per riflettere l'eliminazione.

## <a name="deactivate-and-retain-data"></a>Disattivare e conservare i dati

Se si è interessati all'eliminazione del dispositivo ma si vuole conservarne i dati, seguire questa procedura:

#### <a name="to-deactivate-a-device-and-retain-the-data"></a>Per disattivare un dispositivo e conservare i dati
1. Disattivare il dispositivo. Tutti i contenitori del volume e gli snapshot del dispositivo vengono mantenuti.
   
   1. Passare al servizio Gestione dispositivi StorSimple e fare clic su **Dispositivi**. Nel pannello **Dispositivi** selezionare il dispositivo che si desidera disattivare, fare clic con il tasto destro del mouse e quindi fare clic su **Disattiva**.

         ![Disattivare il dispositivo StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. Nel pannello **Disattiva** digitare il nome del dispositivo per confermare e quindi fare clic su **Disattiva**. Si avvia il processo di disattivazione il cui completamento richiede alcuni minuti.

         ![Disattivare il dispositivo StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)
2. È ora possibile eseguire il failover dei contenitori del volume e degli snapshot associati. Per le procedure, vedere [Failover e ripristino di emergenza per il dispositivo StorSimple](storsimple-8000-device-failover-disaster-recovery.md)
3. Dopo la disattivazione e failover, è possibile eliminare completamente il dispositivo. L’eliminazione di un dispositivo lo rimuove dall'elenco dei dispositivi connessi al servizio. Il servizio quindi non può più gestire il dispositivo eliminato. Completare la procedura seguente per eliminare il dispositivo:
   
   1. Passare al servizio Gestione dispositivi StorSimple e fare clic su **Dispositivi**. Nel pannello **Dispositivi** selezionare il dispositivo disattivato che si desidera eliminare, fare clic con il tasto destro del mouse e quindi fare clic su **Elimina**.

       ![Disattivare il dispositivo StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. Nel pannello **Elimina** digitare il nome del dispositivo per confermare e quindi fare clic su **Elimina**. Il processo di eliminazione richiede alcuni minuti.

       ![Disattivare il dispositivo StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. Si riceverà una notifica una volta completata l'eliminazione. Viene inoltre aggiornato l'elenco dei dispositivi per riflettere l'eliminazione.

     
## <a name="deactivate-and-delete-a-cloud-appliance"></a>Disattivare ed eliminare un'appliance cloud

Per un'appliance cloud StorSimple, la disattivazione dal portale dealloca ed elimina la macchina virtuale e le risorse create quando è stato eseguito il provisioning. Dopo la disattivazione dell'appliance cloud, non è possibile ripristinarne lo stato precedente.

![Disattivare un'appliance cloud StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate7.png)

La disattivazione comporta le seguenti azioni:

* L'appliance cloud StorSimple viene rimossa dal servizio.
* La macchina virtuale per l'appliance cloud StorSimple viene eliminata.
* Il disco sistema operativo e i dischi dati creati per l'appliance cloud StorSimple vengono rimossi.
* Il servizio ospitato e la rete virtuale creati durante il provisioning vengono mantenuti. Se non si utilizzano, è necessario eliminarli manualmente.
* Vengono mantenuti gli snapshot cloud creati per l'appliance cloud StorSimple.

Dopo la disattivazione dell'appliance cloud, è possibile eliminarla dall'elenco dei dispositivi. Selezionare il dispositivo disattivato, fare clic con il pulsante destro del mouse e quindi fare clic su **Elimina**. StorSimple invia una notifica quando il dispositivo viene eliminato e l'elenco dei dispositivi viene aggiornato.

## <a name="next-steps"></a>Passaggi successivi

* Per ripristinare il dispositivo disattivato sulle impostazioni predefinite di fabbrica, passare a [Reimposta il dispositivo sulle impostazioni predefinite](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).
* Per assistenza tecnica, [contattare il supporto Microsoft](storsimple-8000-contact-microsoft-support.md).
* Per altre informazioni sull'uso del servizio Gestione dispositivi StorSimple, vedere l'articolo relativo all'[uso di Gestione dispositivi StorSimple per amministrare il dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

