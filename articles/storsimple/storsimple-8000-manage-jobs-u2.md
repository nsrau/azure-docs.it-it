---
title: Visualizzare e gestire i processi per il dispositivo StorSimple serie 8000 | Microsoft Docs
description: Descrive il pannello Processi del servizio Gestione dispositivi StorSimple e come usarlo per tenere traccia dei processi di backup recenti, correnti e pianificati.
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
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: bf8038b7171053b75eeb9aed88bff4246e65a8a8
ms.contentlocale: it-it
ms.lasthandoff: 07/08/2017

---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-jobs-update-3-and-later"></a>Usare il servizio Gestione dispositivi StorSimple per visualizzare e gestire i processi (Aggiornamento 3 e successivi)

## <a name="overview"></a>Panoramica
Il pannello **Processi** rappresenta un portale centralizzato per la visualizzazione e la gestione dei processi avviati sui dispositivi connessi al servizio Gestione dispositivi StorSimple. È possibile visualizzare i processi pianificati, in esecuzione, completati, annullati e non riusciti per più dispositivi. I risultati vengono presentati in un formato tabulare.

![Pannello Processi](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

È possibile trovare rapidamente i processi desiderati filtrando i campi, ad esempio:

* **Stato**: i processi possono essere in corso, completati, annullati, non riusciti, annullati o completati con errori.
* **Intervallo di tempo**: i processi possono essere filtrati in base all'intervallo di data e ora. Può essere Ultima ora, Ultime 24 ore, Ultimi 7 giorni, Ultimi 30 giorni, Ultimo anno e Data personalizzata.
* **Tipo**: il tipo di processo può essere backup pianificato, backup manuale, backup di ripristino, volume clone, contenitori dei volumi con failover, volume aggiunto in locale, volume modificato, installazione di aggiornamenti, raccolta di log di supporto e creazione di appliance cloud.
* **Dispositivi** : i processi vengono avviati in un determinato dispositivo connesso al servizio.
  
I processi filtrati vengono quindi elaborati in base ai seguenti attributi:
  
* **Nome**: backup pianificato, backup manuale, backup di ripristino, volume clone, contenitori dei volumi con failover, volume aggiunto in locale, volume modificato, installazione di aggiornamenti, raccolta di log di supporto e creazione di appliance cloud.
* **Stato** : in esecuzione, completati, annullati, non riusciti, in fase di annullamento o completati con errori.
* **Entità** : i processi possono essere associati a un volume, un criterio di backup o un dispositivo. Ad esempio, un processo di clonazione è associato a un volume, mentre un processo di backup pianificato è associato a un criterio di backup. Viene creato un processo di dispositivo a causa di un ripristino di emergenza (DR) o di un'operazione di ripristino.
* **Dispositivo** : il nome del dispositivo su cui è stato avviato il processo.
* **Avviato alle** : l'ora di inizio del processo.
* **Durata**: il tempo necessario per completare il processo.

L'elenco dei processi viene aggiornato ogni 30 secondi.

In questa pagina è possibile eseguire le seguenti azioni relative ai processi:

* Visualizza i dettagli dei processi
* Annullare un processo

## <a name="view-job-details"></a>Visualizza i dettagli dei processi
Eseguire la procedura seguente per visualizzare i dettagli di qualsiasi processo.

#### <a name="to-view-job-details"></a>Per visualizzare i dettagli dei processi
1. Passare al servizio Gestione dispositivi StorSimple, quindi fare clic su **Processi**.

2. Nel pannello **Processi**, visualizzare i processi desiderati eseguendo una query con i filtri appropriati. È possibile cercare processi completati, in esecuzione o annullati.

    ![Pannello Processi](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

2. Selezionare e fare clic su un processo.

    ![Pannello Processi](./media/storsimple-8000-manage-jobs-u2/jobs3.png)

3. Nella finestra di dialogo dei dettagli del processo è possibile visualizzare lo stato, i dettagli, le statistiche temporali e le statistiche sui dati.
   
    ![Dettagli del processo](./media/storsimple-8000-manage-jobs-u2/jobs4.png)

## <a name="cancel-a-job"></a>Annullare un processo
Eseguire la procedura seguente per annullare un processo in esecuzione.

> [!NOTE]
> Alcuni processi, ad esempio la modifica di un volume per cambiare il tipo di volume o espandere un volume, non possono essere annullati.


### <a name="to-cancel-a-job"></a>Per annullare un processo
1. Nella pagina **Processi** , visualizzare i processi in esecuzione che si desidera annullare eseguendo una query con filtri appropriati. Selezionare il processo.

2. Fare clic sul processo selezionato per richiamare il menu di scelta rapida e fare clic su **Annulla**.

    ![Dettagli del processo](./media/storsimple-8000-manage-jobs-u2/jobs2.png)

3. Alla richiesta di conferma fare clic su **Sì**. Questo processo ora viene annullato.

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [gestire i criteri di backup di StorSimple](storsimple-8000-manage-backup-policies-u2.md).
* Informazioni su come [usare il servizio Gestione dispositivi StorSimple per gestire il dispositivo StorSimple](storsimple-8000-manager-service-administration.md).


