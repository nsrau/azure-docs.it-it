---
title: Operazioni di backup di StorSimple Snapshot Manager | Microsoft Docs
description: Viene descritto come usare lo snap-in MMC StorSimple Snapshot Manager per visualizzare e gestire i processi di backup pianificati, attualmente in esecuzione e completati.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: bf4dcff6-c819-4766-b9d9-9922831cb200
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 3c26a84e32a17cba83b5ca895f146e561072fa62
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90054960"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-backup-jobs"></a>Usare StorSimple Snapshot Manager per visualizzare e gestire i processi di backup

## <a name="overview"></a>Panoramica
Nel nodo **Processi** del riquadro **Ambito** vengono mostrate le attività di backup **pianificate**, delle **ultime 24 ore** e **in esecuzione** avviate in modo interattivo o da un criterio configurato. 

In questa esercitazione viene illustrato come usare il nodo **Processi** per visualizzare le informazioni sui processi di backup pianificati, recenti e attualmente in esecuzione. L'elenco dei processi e le informazioni corrispondenti vengono visualizzati nel riquadro **risultati** . È inoltre possibile fare clic con il pulsante destro del mouse su un processo elencato e visualizzare un menu di scelta rapida in cui sono elencate le azioni disponibili.

## <a name="view-scheduled-jobs"></a>Visualizzazione dei processi pianificati
Utilizzare la procedura seguente per visualizzare i processi di backup pianificati.

#### <a name="to-view-scheduled-jobs"></a>Per visualizzare i processi pianificati
1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager. 
2. Nel riquadro **Ambito** espandere il nodo **Processi**, quindi fare clic su **Pianificati**. Nel riquadro **Risultati** vengono visualizzate le informazioni seguenti:
   
   * **Nome**: nome dello snapshot pianificato
   * **Prossima esecuzione**: data e ora del successivo snapshot pianificato
   * **Ultima esecuzione**: data e ora dello snapshot pianificato più recente
     
     > [!NOTE]
     > Per gli snapshot eseguiti una sola volta, i valori dei campi **Prossima esecuzione** e **Ultima esecuzione** sono gli stessi.
     
     ![Processi di backup pianificati](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_scheduled.png) 
3. Per eseguire azioni aggiuntive su un processo specifico, fare clic con il pulsante destro del mouse sul nome del processo nel riquadro **Risultati** e selezionare un’opzione del menu.

## <a name="view-recent-jobs"></a>Visualizzazione dei processi recenti
Utilizzare la procedura seguente per visualizzare i processi di backup e ripristino completati nelle ultime 24 ore.

#### <a name="to-view-recent-jobs"></a>Per visualizzare i processi recenti
1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager.
2. Nel riquadro **Ambito** espandere il nodo **Processi**, quindi fare clic su **Ultime 24 ore**. Il riquadro **Risultati** mostra i processi di backup completati nelle ultime 24 ore (fino a un massimo di 64 processi). Le informazioni seguenti vengono visualizzate nel riquadro **Risultati**, a seconda delle opzioni di visualizzazione specificate:****
   
   * **Nome**: nome dello snapshot pianificato.
   * **Avvio**: data e ora di inizio dello snapshot.
   * **Fine**: data e ora di fine dello snapshot.
   * **Tempo trascorso**: quantità di tempo trascorso tra le ore definite dai campi **Avvio** e **Fine**.
   * **Stato**: stato del processo completato di recente. **Esito positivo** indica che la creazione del backup è stata completata. **Esito negativo** indica che il processo non è stato eseguito in modo corretto.
   * **Informazioni**: motivo dell'errore.
   * **Byte elaborati (MB)**: quantità di dati del gruppo di volumi elaborati (in MB). 
     
     ![Processi eseguiti nelle ultime 24 ore](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_Last_24_hours.png) 
3. Per eseguire azioni aggiuntive su un processo specifico, fare clic con il pulsante destro del mouse sul nome del processo nel riquadro **Risultati** e selezionare un’opzione del menu.
   
    ![Eliminare un processo](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Delete_backup.png)

## <a name="view-currently-running-jobs"></a>Visualizzazione dei processi attualmente in esecuzione
Utilizzare la procedura seguente per visualizzare i processi attualmente in esecuzione.

#### <a name="to-view-currently-running-jobs"></a>Per visualizzare i processi attualmente in esecuzione
1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager.
2. Nel riquadro **Ambito** espandere il nodo **Processi**, quindi fare clic su **In esecuzione**. A seconda delle opzioni di visualizzazione specificate,**** nel riquadro **Risultati** vengono visualizzate le informazioni seguenti:
   
   * **Nome**: nome dello snapshot pianificato.
   * **Avvio**: data e ora di inizio dello snapshot.
   * **Checkpoint**: azione corrente del backup.
   * **Stato**: percentuale di completamento.
   * **Tempo trascorso**: quantità di tempo trascorsa dall'inizio del backup. 
   * **Velocità effettiva media (MB)** : rapporto tra i byte totali di dati elaborati e il tempo totale usato per l'elaborazione (MB).
   * **Byte elaborati (MB)** : totale dei byte di dati elaborati (in MB).
   * **Byte scritti (MB)** : totale dei byte scritti (in MB). Questo valore include i dati e i metadati e quindi in genere è maggiore rispetto al valore dei byte elaborati.
     
     ![Processi attualmente in esecuzione](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_running.png)
3. Per eseguire azioni aggiuntive su un processo specifico, fare clic con il pulsante destro del mouse sul nome del processo nel riquadro **Risultati** e selezionare un’opzione del menu.

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [usare StorSimple Snapshot Manager per amministrare la soluzione di StorSimple](storsimple-snapshot-manager-admin.md).
* Informazioni su come [usare StorSimple Snapshot Manager per gestire il catalogo di backup](storsimple-snapshot-manager-manage-backup-catalog.md)

