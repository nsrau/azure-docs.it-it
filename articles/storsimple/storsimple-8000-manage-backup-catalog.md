---
title: Gestire il catalogo di backup di StorSimple | Microsoft Docs
description: Illustra come usare la pagina del catalogo di backup del servizio Gestione dispositivi StorSimple per elencare, selezionare ed eliminare i set di backup.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: ac2577c6cd350d6d437d55e61ec73d954cb24893
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-backup-catalog"></a>Usare il servizio Gestione dispositivi StorSimple per gestire il catalogo di backup
## <a name="overview"></a>Panoramica
Il pannello **Catalogo di backup** del servizio Gestione dispositivi StorSimple visualizza tutti i set di backup creati quando si eseguono backup manuali o programmati. È possibile utilizzare questa pagina per elencare tutti i backup per un criterio di backup o un volume, selezionare o eliminare i backup o utilizzare un backup per ripristinare o clonare un volume.

In questa esercitazione viene illustrato come elencare, selezionare ed eliminare un set di backup. Per scoprire come ripristinare il dispositivo dal backup, vedere [Ripristinare il dispositivo da un set di backup](storsimple-8000-restore-from-backup-set-u2.md). Per scoprire come clonare un volume, vedere [Clonare un volume StorSimple](storsimple-8000-clone-volume-u2.md).

![Catalogo di backup](./media/storsimple-8000-manage-backup-catalog/bucatalog.png) 

Il pannello **Catalogo di backup** fornisce una query per limitare la selezione del set di backup. È possibile filtrare i set di backup che vengono recuperati in base ai parametri seguenti:

* **Dispositivo** : il dispositivo in cui è stato creato il set di backup.
* **Criterio di backup o Volume**: il criterio di backup o volume associato a questo set di backup.
* **Da e A** - intervallo di data e ora di creazione del set di backup.

I set di backup filtrati vengono quindi catalogati in base ai seguenti attributi:

* **Nome** : nome del criterio di backup o volume associato al set di backup.
* **Dimensioni** : dimensione effettiva del set di backup.
* **Creato il** : data e ora di creazione dei backup. 
* **Tipo** : i set di backup possono essere snapshot in locale o del cloud. Uno snapshot locale è un backup di tutti i dati di volume archiviati localmente sul dispositivo, mentre uno snapshot del cloud si riferisce al backup dei dati di volume che risiedono nel cloud. Gli snapshot in locale forniscono un accesso più rapido, mentre gli snapshot del cloud vengono scelti per la resilienza dei dati.
* **Avviato da** : i backup possono essere avviati automaticamente da una pianificazione o manualmente dall'utente. Per pianificare i backup, è possibile usare un criterio di backup. In alternativa, è possibile usare l'opzione **Esegui backup** per eseguire un backup manuale.

## <a name="list-backup-sets-for-a-backup-policy"></a>Elencare i set di backup di un criterio di backup
Completare la procedura seguente per elencare tutti i backup di un criterio di backup.

#### <a name="to-list-backup-sets"></a>Per elencare i set di backup
1. Passare al servizio Gestione dispositivi StorSimple e fare clic su **Catalogo di backup**.

2. Filtrare le selezioni come segue:
   
   1. Specificare l'intervallo di tempo.
   2. Selezionare un dispositivo appropriato.
   3. Filtrare in base ai **Criteri di backup** per visualizzare i backup corrispondenti.
   3. Nell'elenco a discesa dei criteri di backup, scegliere **Tutti** per visualizzare tutti i backup nel dispositivo selezionato.
   4. Fare clic su **Applica** per eseguire la query.
      
      I backup associati al criterio di backup selezionato dovrebbero vengono visualizzati nell'elenco dei set di backup.

      ![Passare a un catalogo di backup](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

## <a name="select-a-backup-set"></a>Selezionare un set di backup
Completare i passaggi seguenti per selezionare un set di backup per un volume o i criteri di backup.

#### <a name="to-select-a-backup-set"></a>Per selezionare un set di backup
1. Passare al servizio Gestione dispositivi StorSimple e fare clic su **Catalogo di backup**.
2. Filtrare le selezioni come segue:
   
   1. Specificare l'intervallo di tempo. 
   2. Selezionare un dispositivo appropriato. 
   3. Filtrare in base al volume o al criterio di backup del backup che si vuole selezionare.
   4. Fare clic su **Applica** per eseguire la query.
      
      I backup associati al volume selezionato o al criterio di backup dovrebbero essere visualizzati nell'elenco dei set di backup.

      ![Passare a un catalogo di backup](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

3. Selezionare ed espandere un set di backup È ora possibile visualizzare i set di backup suddivisi in base ai volumi che contengono. Le opzioni **Ripristina** ed **Elimina** sono disponibili nel menu di scelta rapida del set di backup. È possibile eseguire queste azioni sul set di backup selezionato.

    ![Passare a un catalogo di backup](./media/storsimple-8000-manage-backup-catalog/bucatalog2.png)

## <a name="delete-a-backup-set"></a>Eliminare un set di backup
Eliminare un backup quando non si desidera più conservare i dati associati. Eseguire la procedura seguente per eliminare un set di backup.

#### <a name="to-delete-a-backup-set"></a>Per eliminare un set di backup
 Passare al servizio Gestione dispositivi StorSimple e fare clic su **Catalogo di backup**.
2. Filtrare le selezioni come segue:
   
   1. Specificare l'intervallo di tempo. 
   2. Selezionare un dispositivo appropriato. 
   3. Filtrare in base al volume o al criterio di backup del backup che si vuole selezionare.
   4. Fare clic su **Applica** per eseguire la query.
      
      I backup associati al volume selezionato o al criterio di backup dovrebbero essere visualizzati nell'elenco dei set di backup.

      ![Passare a un catalogo di backup](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

3. Selezionare ed espandere un set di backup È ora possibile visualizzare i set di backup suddivisi in base ai volumi che contengono. Le opzioni **Ripristina** ed **Elimina** sono disponibili nel menu di scelta rapida del set di backup. Fare clic con il pulsante destro del mouse sul set di backup selezionato e scegliere **Elimina** dal menu di scelta rapida.

    ![Passare a un catalogo di backup](./media/storsimple-8000-manage-backup-catalog/bucatalog3.png)

4. Quando viene richiesta la conferma, esaminare le informazioni visualizzate e quindi fare clic su **Elimina**. Il backup selezionato verrà eliminato definitivamente.

    ![Passare a un catalogo di backup](./media/storsimple-8000-manage-backup-catalog/bucatalog4.png)  

5. Verrà visualizzata una notifica dell'operazione di eliminazione in corso e del corretto completamento. Al termine dell'eliminazione, aggiornare la query nella pagina. Il set di backup eliminato non verrà più visualizzato nell'elenco dei set di backup.

    ![Passare a un catalogo di backup](./media/storsimple-8000-manage-backup-catalog/bucatalog7.png)

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [usare la pagina Catalogo di backup per ripristinare il dispositivo da un set di backup](storsimple-8000-restore-from-backup-set-u2.md).
* Informazioni su come [usare il servizio Gestione dispositivi StorSimple per gestire il dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

